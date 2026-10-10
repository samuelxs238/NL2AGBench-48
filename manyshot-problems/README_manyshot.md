## Few-Shot Reference Examples

The few-shot prompts give each model 54 reference examples pairing a geometry problem with a verified AlphaGeometry translation.

- **Examples 1–4** are from `examples.txt` in the [AlphaGeometry repository](https://github.com/google-deepmind/alphageometry). They show the DSL format without a natural-language statement and are not difficulty-rated.
- **Examples 5–54** were sampled from the **HAGeo-409** dataset, 10 from each of five difficulty bands. The score is the `Difficulty_Score` from HAGeo-409, and the problem ID is the dataset's `Problem_ID`.

### AlphaGeometry `examples.txt`

| # | Name | What it proves |
|---|------|----------------|
| 1 | `orthocenter` | The altitudes of a triangle are concurrent (AH ⊥ BC) |
| 2 | `orthocenter_aux` | Same result, using an auxiliary intersection point |
| 3 | `incenter_excenter` | The internal and external bisectors at C are perpendicular |
| 4 | `euler` | The orthocenter, centroid, and circumcenter are collinear |

### HAGeo-409 samples

| # | Problem ID | Difficulty band | Score | What it proves |
|---|------------|-----------------|-------|----------------|
| 5 | `2018RMMp1` | 1–3 | 2.1 | Circumcircles of BKP and CLQ are tangent |
| 6 | `2019CGMOp1` | 1–3 | 2.9 | EK = DK |
| 7 | `2022CzechAPSlovakp5` | 1–3 | 2.4 | B, C, X, Y are concyclic |
| 8 | `2013G4` | 1–3 | 2.8 | QB = QR |
| 9 | `2017IranGOMp2` | 1–3 | 2.1 | A, P, Q are collinear |
| 10 | `2010CHNWesternMOp2` | 1–3 | 2.1 | E, C, M, D are concyclic |
| 11 | `2018G2` | 1–3 | 2.7 | A, P, X, Y are concyclic |
| 12 | `2013BalkanMOp1` | 1–3 | 2.5 | M, K, L, P are concyclic |
| 13 | `2018EuropeanMCupSp2` | 1–3 | 2.4 | O is the circumcenter of the three circumcenters |
| 14 | `2022EGMOp1` | 1–3 | 2.0 | T, H, S are collinear |
| 15 | `2015IranGOAp3` | 3–4 | 3.1 | X, Y, Z are collinear |
| 16 | `2019CMOp3` | 3–4 | 3.6 | PQ is tangent to the circumcircle of ABC |
| 17 | `2006CTSTp7` | 3–4 | 3.0 | O1O3, O2O4, and OP are concurrent |
| 18 | `2017ARMOg10p8` | 3–4 | 3.2 | Tangents at B′ and I meet on AC |
| 19 | `2016CTSTp7` | 3–4 | 3.9 | Circles PDL, PEM, PFN share a second point |
| 20 | `2019IranGOMp3` | 3–4 | 3.3 | Three perpendicular bisectors are concurrent |
| 21 | `2012ARMOg10p8` | 3–4 | 3.8 | F lies on line OI |
| 22 | `2008CTSTp13` | 3–4 | 3.1 | D, I, P are collinear |
| 23 | `2008ARMOg9p3` | 3–4 | 3.8 | Centroid of the perpendicular triangle lies on MH |
| 24 | `2016G2` | 3–4 | 3.0 | XD and AM meet on the circumcircle |
| 25 | `2016G5` | 4–5 | 4.3 | Circumcenter of XSY is equidistant from P and M |
| 26 | `2004CTSTp22` | 4–5 | 4.0 | EF, E1F1, and PD1 are concurrent |
| 27 | `2022CHNSouthEastMOg11p6` | 4–5 | 4.0 | Three circles share a common point |
| 28 | `2009G8` | 4–5 | 4.0 | Orthocenter of I1I2I3 lies on g |
| 29 | `2023IranGOMp4` | 4–5 | 4.2 | AB, SH, TR are concurrent |
| 30 | `2016ARMOg10p8` | 4–5 | 4.0 | Circumcircle of Δ is tangent to the circumcircle |
| 31 | `2012USATSTp6` | 4–5 | 4.0 | PQ ⊥ EF |
| 32 | `2019CTSTp1` | 4–5 | 4.6 | AM ⊥ CD |
| 33 | `2021GOWACAp3` | 4–5 | 4.8 | X lies on the incircle |
| 34 | `2013USATSTp2` | 4–5 | 4.8 | ∠ACT = ∠BCT |
| 35 | `ShuZhiMiGeo109` | 5–6 | 5.5 | Incenter of DEF is the Nagel point of ABC |
| 36 | `2024ELMOSLp4` | 5–6 | 5.1 | IL ∥ TG |
| 37 | `2024KoMaLA864` | 5–6 | 5.6 | H, J, O are collinear |
| 38 | `ShuZhiMi2024SpFp19` | 5–6 | 5.1 | ℓa, ℓb, ℓc, and OI are concurrent |
| 39 | `2025ShuZhiMiMOM3p5` | 5–6 | 5.5 | E, F, P, Q are concyclic |
| 40 | `ShuZhiMiGeo352` | 5–6 | 5.7 | H′O ∥ GhH |
| 41 | `2020CSMGOp6` | 5–6 | 5.1 | I lies on the common chord of circles XBQ and XCP |
| 42 | `2021IsraelOlympicRev` | 5–6 | 5.0 | BB′ and CC′ meet on circle APbPc |
| 43 | `2021GOWACAp5` | 5–6 | 5.0 | Incenter of PXY lies on AD |
| 44 | `2020AQGOp5` | 5–6 | 5.2 | A, K, R are collinear |
| 45 | `2021IranTSTp6` | 6–7 | 6.1 | XP and EF meet on the altitude from A |
| 46 | `ShuZhiMiGeo117` | 6–7 | 6.4 | OI bisects KX |
| 47 | `2025VietnamTSTp2` | 6–7 | 6.6 | HJ ∥ H′S |
| 48 | `2022FakeCMOp3` | 6–7 | 6.4 | PaQa, PbQb, PcQc are concurrent |
| 49 | `ShuZhiMiGeo178` | 6–7 | 6.1 | AX, BY, CZ are concurrent |
| 50 | `2024ShuZhiMiMOM0p3` | 6–7 | 6.3 | Three Newton lines are concurrent |
| 51 | `ShuZhiMiGeo409` | 6–7 | 6.2 | S is the circumcenter of OHD |
| 52 | `MouseMOp3` | 6–7 | 6.4 | Euler lines of ABC and TBC are parallel |
| 53 | `2023IranGOAp5` | 6–7 | 6.3 | Circumcircles of ART and PEF are tangent |
| 54 | `2023PlanetCupp10` | 6–7 | 6.5 | PY, QX, AG meet on the circumcircle |
