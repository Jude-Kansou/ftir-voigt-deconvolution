# FTIR Voigt Deconvolution for Protein Secondary Structure

Resolving the overlapping **Amide I–III** bands of an FTIR spectrum into physically
meaningful sub-peaks by fitting a sum of **height-normalised Voigt profiles**, to
quantify protein secondary structure.

A protein's secondary structure (α-helix, β-sheet, turns, disordered) is encoded in
the Amide region of the infrared spectrum, but the sub-bands overlap heavily — a
single broad envelope hides several components. This pipeline separates them.

## Method

1. **Preprocess** — load raw spectra, restrict to the Amide window, smooth
   (`preprocess_spectra.ipynb`).
2. **Peak detection** — locate candidate sub-bands on the smoothed spectrum with
   `scipy.signal.find_peaks`, then exclude everything outside the Amide I–III range.
3. **Voigt model** — build a sum of height-normalised Voigt profiles. The Voigt
   profile (a convolution of Gaussian and Lorentzian broadening) is evaluated
   through the complex **Faddeeva function** (`scipy.special.wofz`); amplitude is
   normalised so it equals the true peak height.
4. **Fit** — solve by non-linear least squares with the bounded **`dogbox`** solver,
   keeping peak heights and widths physical (widths bounded away from zero).
5. **Evaluate** — RMSE / MAE / R² plus a residual plot.

## Results

On the test spectrum the fit reaches **R² = 0.996** (RMSE 0.055, MAE 0.045), with
residuals held within roughly ±0.15 absorbance across the band.

## Contents

```
notebooks/
  preprocess_spectra.ipynb    # load + window + clean raw FTIR spectra
  voigt_deconvolution.ipynb   # peak detection, Voigt fit, metrics, plots
```

## Running it

```bash
pip install numpy pandas scipy scikit-learn matplotlib openpyxl
jupyter lab
```

**Data note:** the raw spectral data files are not included in this repository —
point the notebooks at your own FTIR `.xlsx` (columns `wavenumber`, `absorbance`).

## Notes

Written from scratch — every step (the Voigt profile, the sum-of-Voigts model, the
peak-detection and fitting logic) was implemented by hand, before AI coding tools.

Developed as undergraduate research under Prof. Loong-Tak Lim, University of Guelph.

## License

MIT © Jude Kansou
