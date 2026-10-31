# Automated-UTM-Tensile-Analyzer
Automated Python pipeline for processing raw UTM load-displacement exports into engineering stress-strain curves and ASTM E8 mechanical properties.

# Automated UTM Tensile Test Analyzer

An end-to-end Python data-processing pipeline that converts raw Universal Testing Machine (UTM) load-displacement CSV exports into cleaned engineering stress-strain datasets, extracts key mechanical properties, and generates publication-grade figures.

## Sample Output
![Engineering Stress-Strain Curve](stress_strain.png)

## Key Technical Features
* **Automated Pre-Processing & Baseline Zeroing:** Strips UTF-8 BOM artifacts, coerces non-numeric sensor dropouts (`NaN`/`inf`), and removes initial machine preload and grip-displacement offsets using multi-point baseline medians (flagging preloads >5% of peak load).
* **Sliding-Window Young's Modulus ($E$) Detection:** Eliminates manual range selection and toe-region slack bias by scanning a fixed stress-fraction window (15% of UTS) across the 10–80% UTS pre-peak band, filtering for $R^2 \ge 0.995$, selecting the steepest elastic segment, and growing the region outward within a 1% UTS residual tolerance before final least-squares refitting.
* **Sub-Point 0.2% Offset Yield Strength ($\sigma_y$):** Computes the exact intersection between the experimental curve and the 0.002-strain shifted elastic construction line via linear interpolation.
* **Fracture Detection & ASTM E8 / ISO 6892-1 Elongation:** Identifies true specimen fracture prior to sudden post-necking load collapse and separates **total elongation at fracture** from **plastic elongation after fracture** by subtracting elastic springback ($\varepsilon_f - \sigma_f / E$).
* **Publication-Quality Visualization:** Exports 300 DPI PNG and vector PDF plots formatted with an Okabe-Ito colorblind-safe palette, serif/STIX math typography, and an automated zoomed inset of the elastic-to-yield transition.

## Input Requirements
* A CSV file containing `Load (N)` and `Displacement (mm)` columns.
* Specimen initial gauge length $L_0$ (mm) and original cross-sectional area $A_0$ ($\text{mm}^2$).
* Built-in synthetic benchmark mode (`USE_SAMPLE_DATA = True`) simulating a 200 GPa mild-steel specimen with toe slack, Gaussian sensor noise, baseline offsets, necking, and fracture drop.

## Dependencies & Usage
```bash
pip install numpy pandas matplotlib
```
1. Open the notebook in JupyterLab or Google Colab.
2. Set `GAUGE_LENGTH_MM` and `AREA_MM2` in the **Inputs** cell (or enable `USE_SAMPLE_DATA = True` to run the synthetic mild-steel verification dataset).
3. Run all cells to output the extracted property summary table, `cleaned_stress_strain.csv`, `stress_strain.png`, and `stress_strain.pdf`.
