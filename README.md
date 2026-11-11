# Guided-Search Attention Surrogates

Code, pre-registration, and per-network results for

> H. Cho, "Guided-Search Attention Surrogates: Faithful Post-hoc Concept Explanations of Vision Networks," submitted to *IEEE Access*, 2026.

The method explains a trained image classifier **without modifying or retraining it**. Hidden states at one layer are read out on decision-relevant whitened axes (tv-O), and a small surrogate aggregates the per-position evidence with attention modeled on guided search and the attentional spotlight. The per-position evidence is the explanation map and sums exactly to the surrogate's score.

## Repository layout

| Path | Contents |
|---|---|
| `notebooks/xai_C2.ipynb` | Main confirmatory run: 46 networks, all pre-registered hypotheses (Tables 2–8, Figs. 2 and 5) |
| `notebooks/xai_MCD.ipynb` | Comparison with MCD (Vielhaben et al., TMLR 2023) under the same rule (Table 4) |
| `notebooks/xai_CHK.ipynb` | Convergence check with 3× optimization steps; noise-free example maps (Fig. 3, Fig. 4) |
| `notebooks/xai_FB2.ipynb` | FunnyBirds benchmark with the official framework and checkpoints (Table 9) |
| `notebooks/*.py` | The same code as plain Python (each notebook is a single cell) |
| `design/C2-preregistration.md` | Hypotheses, thresholds, test families, and decision rules fixed before the C2 run (written under the run's working name C1) |
| `design/MCD-design.md`, `design/FB2-design.md` | Designs of the MCD comparison and the FunnyBirds re-run, written after the C2 results were seen |
| `results/C2/` | Summary CSVs (`C2_verdict.csv` holds every hypothesis test), `per_network/` JSON for each of the 46 networks, `maps/` saved example maps, `env.txt` (full package list) |
| `results/MCD/`, `results/CHK/`, `results/FB2/` | Results of the other runs |
| `paper/tools/gen_tables.py` | Regenerates every table of the paper from `results/` |
| `paper/tools/fig_a1_scatter.py` | Regenerates Fig. 5 from `results/C2/per_network/` |
| `figures/` | Figures used in the paper |

## Reproducing the tables and figures from the saved results

```bash
pip install -r requirements.txt
python paper/tools/gen_tables.py results paper/tables
python paper/tools/fig_a1_scatter.py results/C2/per_network figures/fig_A1_scatter_col.png
```

## Re-running the experiments

The notebooks were run with Korean code comments and log messages; the versions here have them translated to English, and the code itself is identical (checked by comparing syntax trees). Each notebook is one cell written for Google Colab with a GPU runtime. It mounts Google Drive, writes models, results, and a copy of its own code to `MyDrive/xai_<name>/`, and resumes network by network if the session is interrupted. Datasets are downloaded automatically (CIFAR-10, Shapes3D, Imagenette, FunnyBirds); torchvision ImageNet weights and the official FunnyBirds checkpoints are fetched at run time.

Run order: `xai_C2` → `xai_MCD` and `xai_CHK` (reuse C2 models and settings) → `xai_FB2` (reuses the FunnyBirds baselines saved by C2).

**Network checkpoints.** C2 evaluates networks trained beforehand: CIFAR-10 ResNet-18 and ViT-S/16 models, and Shapes3D ResNet-18 (`xai_stage11d/`) and ViT (`xai_stage13/`) models. Training code and recipes are described in Section IV-A of the paper. The checkpoints are too large for this repository and will be released separately; the MD5 prefix of every checkpoint used is recorded in the per-network JSON files.

## Main results (from `results/C2/C2_verdict.csv`)

Mean per-network improvement of the proposed surrogate over the original average-based surrogate on LRP-SVD axes (all confirmed: Holm-corrected one-sided Wilcoxon *p* ≤ .008, every network improved):

| | CIFAR-10 ResNet-18 (n=10) | Shapes3D ResNet-18 (n=9) | Shapes3D ViT (n=18) |
|---|---|---|---|
| Decision fidelity | +.056 | +.364 | +.258 |
| Region-swap fidelity | +.075 | +.348 | +.187 |

Negative and unresolved results are reported in the paper and in `C2_verdict.csv` (e.g., the static spatial map is slightly better when objects are always centered; IG is better for pixel-level shortcut localization and on FunnyBirds).

## Citation

```bibtex
@article{cho2026guided,
  author  = {Cho, Heeseung},
  title   = {Guided-Search Attention Surrogates: Faithful Post-hoc Concept Explanations of Vision Networks},
  journal = {IEEE Access},
  note    = {Submitted},
  year    = {2026}
}
```

## License

MIT (see `LICENSE`). Third-party datasets, pretrained weights, and the FunnyBirds framework are subject to their own licenses.
