# ml: Analysis, baselines, and measurement

Python work that produces what the apps bundle and checks the claims in
[spec C4](../spec/02-architecture.md#c4-measurement-plan).

| Folder | Contents |
|--------|----------|
| `notebooks/` | Exploratory analysis of the DGamesDataSet (feature importance, confound checks) |
| `measurement/` | Measurement stub: clustering validity, demographics-only baseline (F1 0.64), timing-jitter harness |
| `priors/` | Bundled age-bracket priors (μ, σ) exported for the apps; signed before bundling |
| `data/` | **Local only, never committed.** See `data/README.md` |
