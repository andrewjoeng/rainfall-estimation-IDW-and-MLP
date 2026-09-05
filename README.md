# Estimasi Curah Hujan Bulanan di Pulau Jawa
### Perbandingan Inverse Distance Weighted (IDW) & Multilayer Perceptron (MLP)

**[Nama Kamu]** — Fisika (Peminatan Geofisika), Universitas Negeri Yogyakarta, 2024
Skripsi | Python · Geospatial Data Analysis · Machine Learning

---

## Ringkasan

Curah hujan adalah salah satu variabel lingkungan paling berpengaruh terhadap risiko operasional di sektor energi — mulai dari perencanaan proyek energi baru terbarukan (potensi hidro, drainase lahan panel surya) hingga mitigasi risiko cuaca ekstrem di area operasi lapangan. Namun, data curah hujan observasi (stasiun) sering kali tidak tersedia merata secara spasial, sehingga estimasi di lokasi tanpa data menjadi krusial.

Project ini membangun dan membandingkan tiga pendekatan untuk mengestimasi **distribusi curah hujan bulanan** di seluruh wilayah Pulau Jawa (2001–2014), menggunakan data satelit PERSIANN yang dikoreksi bias terhadap data referensi MSWEP:

- **IDW1** — koreksi bias diterapkan pada data **sebelum** interpolasi spasial
- **IDW2** — interpolasi dilakukan pada data mentah, koreksi bias diterapkan **setelah** interpolasi
- **MLP** — model neural network yang dilatih pada data yang sudah dikoreksi bias

## Mengapa Ini Relevan untuk Industri Energi

- **Asesmen potensi EBT** — pola curah hujan spasial mendukung studi kelayakan pembangkit hidro dan analisis ketersediaan air.
- **Manajemen risiko operasional** — estimasi curah hujan di area tanpa stasiun pemantau membantu mengantisipasi gangguan cuaca terhadap aktivitas lapangan (eksplorasi, logistik, konstruksi).
- **Deteksi kejadian ekstrem** — salah satu temuan utama project ini (lihat di bawah) langsung relevan untuk early warning risiko banjir/cuaca ekstrem di sekitar aset operasional.

## Metodologi

1. **Preprocessing** — konversi format data (CSV ⇄ NetCDF), pemisahan data time series menjadi file bulanan.
2. **Regridding** — penyamaan resolusi spasial PERSIANN & MSWEP ke grid target 0,125° di area Pulau Jawa.
3. **Bias correction** — koreksi bias PERSIANN terhadap MSWEP menggunakan quantile mapping.
4. **IDW1 & IDW2** — interpolasi spasial dengan optimasi parameter *power* dan *radius* pencarian via 5-fold cross-validation (hasil optimal: power = 3, radius = 30 km).
5. **MLP** — model `MLPRegressor` (scikit-learn), 3 hidden layer (150, 75, 30 neuron), *early stopping*.
6. **Evaluasi** — MAE (bulanan/musiman/tahunan) terhadap data referensi, serta POD/FAR/CSI untuk performa deteksi curah hujan ekstrem (persentil ≥95%).

## Hasil

**MAE rata-rata (2001–2014)**

| Model | MAE Tahunan | MAE Bulanan |
|---|---|---|
| **MLP** | **46,01** (terbaik) | **45,15** (terbaik) |
| IDW2 | 47,44 | 48,37 |
| IDW1 | 49,01 | 49,43 |

**Deteksi curah hujan ekstrem (persentil ≥95%)**

| Model | POD | FAR | CSI |
|---|---|---|---|
| **IDW2** | **0,619** (terbaik) | **0,117** (terkecil) | **0,579** (terbaik) |
| IDW1 | 0,472 | 0,167 | 0,419 |
| MLP | 0,151 | 0,638 | 0,118 |

**Insight utama:** MLP unggul untuk estimasi curah hujan rata-rata secara umum, tetapi **IDW2 jauh lebih andal untuk mendeteksi kejadian ekstrem** — MLP cenderung melewatkan (miss) curah hujan ekstrem, sementara IDW2 jauh lebih sensitif. Ini menunjukkan trade-off praktis: pilihan model idealnya disesuaikan dengan tujuan penggunaan (estimasi umum vs. peringatan dini ekstrem), dan membuka peluang pendekatan **hybrid IDW2 + MLP**.

## Contoh Visualisasi

Peta klimatologi bulanan hasil pemodelan (2001–2014) — lihat `assets/climatology_bulanan_MLP.png` di repo ini.

## Tools & Library

`Python` · `Google Colab` · `NumPy` · `Pandas` · `Scikit-learn` · `xarray` · `Matplotlib`

## Struktur Repo

```
├── notebook/
│   └── Estimasi_Curah_Hujan_IDW_MLP_Final.ipynb   # pipeline lengkap: preprocessing → hasil
├── assets/
│   └── climatology_bulanan_MLP.png                 # contoh output visualisasi
└── README.md
```

## Kontak

[Nama Kamu] — [email] · [LinkedIn] · [GitHub]
