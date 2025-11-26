```r
# =========================
# Script Analisis Markov
# Pemodelan Stokastik Perpindahan Aktivitas TPB - Kelompok 7 RA
# File: script_analisis.R
# Input: Dataset/data_responden.csv
# Output: matriks transisi, P^n, steady-state, plots (Graf & Heatmap), simulasi lintasan
# =========================

# --- Persiapan ---
library(tidyverse)
library(igraph)
library(reshape2)   # untuk melt
# install.packages(c("tidyverse","igraph","reshape2"))  jika belum

# --- Parameter ---
data_path <- "Dataset/data_responden.csv"
n_step <- 3          # contoh hitung P^3
n_sim_steps <- 20    # panjang simulasi lintasan contoh
sim_start_state <- NULL  # set NULL agar acak dari distribusi stasioner jika mau

# --- Baca data: deteksi wide vs long ---
df_raw <- read_csv(data_path, show_col_types = FALSE)

# Fungsi untuk cek apakah data wide (banyak kolom jam) atau long (respondent,time,state)
is_wide <- function(df){
  # wide jika ada lebih dari 2 kolom selain id dan kolom bernama pattern jam
  ncol(df) > 3
}

if(is_wide(df_raw)){
  # Asumsi: baris = responden, kolom: id/NIM (opsional) + slot waktu (time1,time2,...)
  # Pilih kolom yang berisi state (string). Kita anggap kolom pertama adalah id bila non-state.
  # Deteksi kolom state: berisi nilai kategori (bukan numeric sepanjang kolom)
  candidate_cols <- colnames(df_raw)
  # cari kolom non-numeric (treat as state columns)
  state_cols <- candidate_cols[sapply(df_raw, function(x) !is.numeric(x))]
  # jika semua kolom non-numeric (mungkin tanpa id), gunakan semua
  if(length(state_cols) <= 1){
    state_cols <- candidate_cols
  }
  df_long <- df_raw %>%
    select(all_of(state_cols)) %>%
    mutate(response_id = row_number()) %>%
    pivot_longer(cols = -response_id, names_to = "time_slot", values_to = "state") %>%
    arrange(response_id)
} else {
  # Asumsi format long minimal: respondent, time (opsional), state
  colnames(df_raw) <- tolower(colnames(df_raw))
  # cari nama kolom yang cocok
  if(all(c("respondent","state") %in% colnames(df_raw))){
    df_long <- df_raw %>% rename(response_id = respondent) %>% mutate(time_slot = ifelse("time" %in% colnames(df_raw), time, NA))
  } else if(all(c("id","state") %in% colnames(df_raw))){
    df_long <- df_raw %>% rename(response_id = id) %>% mutate(time_slot = ifelse("time" %in% colnames(df_raw), time, NA))
  } else {
    # fallback: anggap baris = sequence already in two cols: first = respondent, second = state
    df_long <- df_raw %>% rename(response_id = 1, state = 2)
  }
  # pastikan urut per respondent per waktu (time_slot jika ada)
  if("time" %in% colnames(df_raw) | "time_slot" %in% colnames(df_long)){
    df_long <- df_long %>% arrange(response_id, time_slot)
  } else {
    df_long <- df_long %>% group_by(response_id) %>% mutate(time_slot = row_number()) %>% ungroup()
  }
}

# Bersihkan NA dan trim
df_long <- df_long %>% filter(!is.na(state)) %>% mutate(state = as.character(state) %>% str_trim())

# --- States (urutan alfabetis untuk konsistensi) ---
states <- df_long$state %>% unique() %>% na.omit() %>% sort()
k <- length(states)
message("States detected: ", paste(states, collapse = ", "))

# --- Matriks Frekuensi Transisi ---
# Buat pasangan transisi (state_t -> state_{t+1}) per response_id
trans_pairs <- df_long %>%
  group_by(response_id) %>%
  arrange(time_slot) %>%
  mutate(next_state = lead(state)) %>%
  filter(!is.na(next_state)) %>%
  ungroup() %>%
  count(state, next_state) %>%
  complete(state = states, next_state = states, fill = list(n = 0)) %>%
  arrange(state, next_state)

freq_mat <- matrix(trans_pairs$n, nrow = k, ncol = k, byrow = FALSE,
                   dimnames = list(states, states))

# --- Matriks Probabilitas Transisi P (baris-normalisasi) ---
row_sums <- rowSums(freq_mat)
P <- freq_mat
for(i in seq_len(k)){
  if(row_sums[i] == 0){
    # jika tidak ada transisi dari state i (isolated), buat self-loop prob 1
    P[i, ] <- 0
    P[i, i] <- 1
  } else {
    P[i, ] <- freq_mat[i, ] / row_sums[i]
  }
}

# --- Output numerik utama ---
cat("\nMatriks Frekuensi (counts):\n")
print(freq_mat)
cat("\nMatriks Probabilitas Transisi P:\n")
print(round(P, 4))

# --- Hitung P^n ---
Pn <- P %>% as.matrix() %>% `%^%`(n_step)  # butuh operator matrix power; fallback implement below if error
# fallback implement matrix power manually jika operator tidak tersedia
matrix_power <- function(A, n){
  if(n == 1) return(A)
  if(n == 0) return(diag(nrow(A)))
  res <- A
  for(i in 2:n) res <- res %*% A
  return(res)
}
Pn <- matrix_power(P, n_step)
cat("\nP^", n_step, ":\n", sep = "")
print(round(Pn, 4))

# --- Distribusi Stasioner: eigenvector kiri (pi P = pi) ---
ev <- eigen(t(P))
# cari eigenvalue 1 paling mendekati
idx <- which.min(abs(ev$values - 1))
pi_vec <- Re(ev$vectors[, idx])
pi_vec <- pi_vec / sum(pi_vec)
names(pi_vec) <- states
cat("\nDistribusi stasioner (steady-state) pi:\n")
print(round(pi_vec, 4))

# --- Simulasi lintasan (contoh) ---
set.seed(123)
if(is.null(sim_start_state)){
  # mulai dari distribusi stasioner untuk visualisasi representatif
  start_state <- sample(states, size = 1, prob = pi_vec)
} else start_state <- sim_start_state

simulate_markov <- function(P, states, start, steps){
  cur <- start
  path <- character(steps + 1)
  path[1] <- cur
  for(i in 1:steps){
    cur_idx <- which(states == cur)
    cur <- sample(states, size = 1, prob = P[cur_idx, ])
    path[i+1] <- cur
  }
  return(path)
}
sim_path <- simulate_markov(P, states, start_state, n_sim_steps)
cat("\nContoh simulasi lintasan (start =", start_state, "):\n")
print(sim_path)

# --- Visualisasi: Heatmap matriks transisi ---
heat_df <- as.data.frame(P) %>% rownames_to_column("from") %>% pivot_longer(-from, names_to = "to", values_to = "p")
# plot heatmap (ggplot)
p_heat <- ggplot(heat_df, aes(x = to, y = from, fill = p)) +
  geom_tile(color = "white") +
  geom_text(aes(label = round(p, 3)), size = 3) +
  labs(title = "Heatmap Matriks Probabilitas Transisi (P)", x = "To", y = "From") +
  theme_minimal() + theme(axis.text.x = element_text(angle = 45, hjust = 1))
ggsave("Visualisasi/heatmap_P.png", p_heat, width = 7, height = 5, dpi = 300)

# --- Visualisasi: Graf transisi (igraph) ---
# buat graph berarah dengan bobot = probabilitas
g <- graph_from_adjacency_matrix(P, mode = "directed", weighted = TRUE, diag = TRUE)
E(g)$width <- E(g)$weight * 5
V(g)$size <- 30
V(g)$label.cex <- 0.9
png("Visualisasi/graf_transisi.png", width = 900, height = 700)
set.seed(42)
plot(g, edge.arrow.size = 0.6, main = "Graf Transisi Markov (weighted edges = P)")
dev.off()

# --- Simpan matriks & hasil ke file CSV/TXT ---
dir.create("Output", showWarnings = FALSE)
write_csv(as_tibble(freq_mat, rownames = "from"), "Output/freq_matrix.csv")
write_csv(as_tibble(round(P,6), rownames = "from"), "Output/P_matrix.csv")
write_csv(as_tibble(round(Pn,6), rownames = "from"), paste0("Output/Pn_matrix_n", n_step, ".csv"))
write_csv(tibble(state = states, pi = round(as.numeric(pi_vec),6)), "Output/steady_state.csv")
write_lines(sim_path, "Output/simulasi_lintasan.txt")

cat("\nSelesai. File output tersimpan di folder: Output/ dan gambar di Visualisasi/\n")
