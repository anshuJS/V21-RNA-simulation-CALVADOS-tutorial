# V21 RNA simulation — CALVADOS tutorial

A hands-on, self-contained tutorial for **coarse-grained RNA molecular dynamics** with the
[CALVADOS](https://github.com/KULL-Centre/CALVADOS) model, built around one example: a 21-nt RNA, **V21**
(`UAUAAUUAAUAACUAAUUACU`).

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/anshuJS/V21-RNA-simulation-CALVADOS-tutorial/blob/main/V21_RNA_CALVADOS_tutorial.ipynb)

**Click the badge to run it in Google Colab — no installation, free CPU runtime.**

## What the notebook covers

1. **The ideas** — what molecular dynamics is ($\mathbf{F}=-\nabla U$, velocity-Verlet), why we coarse-grain,
   and the CALVADOS force field, with equations and **live animations** of:
   - a bead oscillating in a harmonic well,
   - the **Ashbaugh–Hatch** stickiness term (vary λ),
   - **Debye–Hückel** electrostatics and **salt screening** (the curve collapsing as salt rises).
2. **Setting up V21** — step by step, **visualising the system in 3-D** at each stage (`py3Dmol`):
   a real all-atom RNA with the CG beads overlaid (the atoms→beads mapping), the built 42-bead chain,
   and the 6 base-pair restraints that impose a hairpin.
   (The notebook *configures* a run but does not execute a long simulation — that needs a GPU.)
3. **Run a short MD yourself (optional)** — build a CALVADOS-*style* CG RNA force field directly in
   **OpenMM** (bonds, angle, Ashbaugh–Hatch, Debye–Hückel — the Part-1 equations as code) and run a few
   thousand steps on the CPU; watch $R_g$ compact as salt rises.
4. **Analysis** — using the **pre-computed trajectories shipped in this repo** (1 µs CALVADOS runs of V21
   across a salt series, both disordered and hairpin variants): radius of gyration, end-to-end distance,
   the salt-titration trend, **free-energy profiles** $F(R_g)=-k_BT\ln P(R_g)$, **contact maps** (threshold)
   + **contact-distance** analysis revealing the hairpin stem as an anti-diagonal, and an **animated 3-D
   trajectory**. All analysis is plain **NumPy** + `py3Dmol` — no `mdtraj`, so there are no
   compiled-extension / version-compatibility problems on Colab.

## What's in `data/`

```
data/
├── residues_C2RNA.csv                 CALVADOS RNA bead parameters
├── reference_full_traj_summary.csv    Rg / e2e from the FULL 1 µs runs (for comparison)
├── V21_top.pdb                        shared 42-bead topology (all sims share it)
├── example_rna_allatom.pdb            a real 5-nt all-atom RNA (for the atoms→beads figure)
└── trajectories/
    ├── disordered/V21_dis_<salt>mM/coords.npz   xyz (1000,42,3) + box (1000,3), nm
    └── hairpin/   V21_hp_<salt>mM/coords.npz
```

- **12 salt points** each: 0, 10, 20, 50, 100, 150, 200, 300, 400, 500, 700, 1000 mM.
- Trajectories are **down-sampled** (every 100th frame of the 1 µs runs → 1000 frames) and stored as plain
  NumPy `.npz` (coordinates + box, loadable with `numpy.load`, no MD library needed); they reproduce the
  full-run Rg/e2e to ±0.02 nm.

## Model / setup summary

- CALVADOS **v0.7.0**; RNA = 2 beads/nucleotide (phosphate + base), generic `r` bead for every base
  (the model has no A/U/C/G-specific or Watson–Crick parameters).
- 40 nm cubic box, 300 K, pH 7, single RNA molecule.
- **Hairpin** = the disordered model **+ 6 harmonic custom restraints** on the RNAfold stem base pairs
  (3–19, 4–18, 5–17, 6–16, 7–15, 8–14), `k = 700` kJ/mol/nm², `r = 0.62` nm.

## Running your own (beyond the tutorial)

Set `platform='CUDA'` and `steps≈1e8` (1 µs) in the generated `config.yaml`/`prepare.py` and run on a GPU
machine with OpenMM CUDA. The notebook shows the exact `prepare.py`.

## Credits

- **CALVADOS:** KULL-Centre/CALVADOS (Tesei, Lindorff-Larsen, and colleagues).
- Tutorial, V21 setup, and trajectories: this repository.

License: MIT (see `LICENSE`).
