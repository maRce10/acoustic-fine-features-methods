# Waveform-based acoustic analysis is sensitive to acoustic fine structure to a similar degree as birds, while traditional techniques are not

Code and data to reproduce the analyses and figures of:

> McLean C. R., Odom K. J., Araya-Salas M., Prior N. H. *Waveform-based acoustic analysis is sensitive to acoustic fine structure to a similar degree as birds, while traditional techniques are not.* (under review)

We compare nine acoustic structure quantification methods (warbleR, Raven Pro and Sound Analysis Pro) for their ability to detect phase differences in synthesized Schroeder-phase harmonic complexes, and evaluate the two waveform-based methods (waveform correlation and waveform DTW) under decreasing signal-to-noise ratios.

The rendered analysis reports are available at **https://marce10.github.io/acoustic-fine-features-zebra-finch/**

## Repository structure

```
_quarto.yml, index.qmd, styles.css                 Quarto website configuration (rendered site in docs/, cached results in _freeze/)
scripts/
  1_schroeder_synthesis_and_method_comparison.qmd  Schroeder synthesis, acoustic measurements, MRM models (Table 1, Figs. 1-6)
  2_background_noise_effect.qmd                    White noise addition and waveform similarity across SNR (Fig. 7)
  MRM2.R                                           Multiple regression on distance matrices (modified from ecodist::MRM)
  qmd.css                                          Style sheet for the rendered reports
data/
  raw/
    200ms_SAPsim.xlsx                              Sound Analysis Pro pairwise similarity output
    extended_selection_table_schroeders_clips/     Exported Schroeder clips + annotations; 200ms/ holds the 0.2 s, 32-bit clips used as SAP input
    additional_species/                            Recordings used in Fig. 3 (+ time points spreadsheet) and zebra finch call used in Fig. 1
  processed/
    200ms_schroeders/, single_schroeders/          Synthesized Schroeders (repeated cycles, 200 ms; single cycle)
    extended_selection_table_*.RDS                 warbleR extended selection tables of the synthesized Schroeders
    200ms_schroeder_master.wav, master_annotations_200ms_schroeders.csv, 200ms_schroeders.txt
                                                   Master sound file and annotations used in Raven Pro (200ms_schroeders.txt includes Raven measurements)
    BatchCorrOutput_v3_hamming.txt, raven_waveform_correlation_200ms_schroeders.txt
                                                   Raven Pro spectrogram cross-correlation and waveform correlation matrices
    *_200ms_schroeders.RDS, *_single_schroeders.RDS
                                                   Pairwise similarity/distance data
    warbler_spectrographic_features_200ms_schroeders.RDS, warbler_mfcc_descriptors_200ms_schroeders.RDS
                                                   warbleR spectrographic features and MFCC descriptors (warbleR 1.1.37, soundgen 3.0.0)
    matrix_correlation_*.RDS, mfcc_warbler_distance_200ms_schroeders.RDS
                                                   MRM model fits shown in Fig. 6
    waveform_similarity_adjusted_snr_*.RDS         Waveform similarity on noise-added Schroeders
    matrix_regression_*_varying_snr_*.RDS          MRM model fits shown in Fig. 7
output/
  figures/                                         Manuscript figures (fig1 ... fig7)
  figures/snr_examples/                            Example noise-added Schroeders at each signal-to-noise ratio
```

## Reproducing the analyses

Open `acoustic_fine_features_methods.Rproj` and run the Quarto documents in `scripts/` in order. Paths are relative to the project root. To rebuild the website run `quarto render` in the project root (output goes to `docs/`, which is served by GitHub Pages).

- Random steps are seeded: `set.seed(123)` before every `MRM2()` call (permutation p-values) and `seed = i` (the target SNR) in `baRulho::add_noise()`. All model fits in `data/processed/` were produced with these seeds.
- Waveform correlation and waveform DTW are computed with `warbleR::waveform_similarity()` (`type = "sliding"`, `n = 100`) on both single-cycle and repeated-cycle Schroeders.

- Time-consuming steps (sound synthesis, acoustic measurements, model fitting with 10,000 permutations) are set to `eval: false`; their outputs are already saved in `data/processed/`, and the documents read them to produce the results and figures. Set those chunks to `eval: true` to recompute everything.
- Raven Pro (v1.6.5) and Sound Analysis Pro measurements were made outside R; their outputs are included in `data/processed/` and `data/raw/`.
- Fig. 5 is assembled from the two panels created by the code (`fig5a_*`, `fig5b_*`) into `fig5_schroeder_representations_combined.png` outside R.

### Large files not included in the repository

Two intermediate files with the noise-added Schroeders exceed GitHub's file size limit and are not tracked:

- `data/processed/200ms_schroeders_adjusted_snr_white_noise.RDS` (1.1 GB)
- `data/processed/single_schroeder_adjusted_snr_white_noise.RDS` (292 MB)

They can be regenerated with the "Add synthetic noise" chunks in `scripts/2_background_noise_effect.qmd` (noise is random, so values will differ slightly). The downstream waveform similarity data and model fits used in Fig. 7 are included.

## Main R packages

warbleR (v1.1.35+, includes `waveform_similarity()`), baRulho (v2.1.5), ecodist, PhenotypeSpace (`github: maRce10/PhenotypeSpace`), Rraven, seewave, tuneR, dtw, ggplot2, viridis. Packages are loaded with `sketchy::load_packages()` at the start of each document.

## Contact

Marcelo Araya-Salas ([marce10.github.io](https://marce10.github.io/))
