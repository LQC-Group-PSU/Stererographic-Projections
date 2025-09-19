# Stererographic-Projections

This repository contains MATLAB utilities that post-process phase-field simulations of ferroelectric materials by mapping polarization vectors onto stereographic (orthographic) projections. The plots and videos produced by the scripts help interpret which ferroelastic variants are present, visualize switching pathways, and quantify the density of trajectories during field cycling.

## Repository structure

| File | Description |
| --- | --- |
| `SPanalysis.m` | Script for generating a single orthographic projection from one simulation snapshot. The script loads a `.dat` file, computes a 2D histogram of the in-plane polarization components, and overlays both the raw points and the logarithmic density map. Helper functions at the bottom of the file rotate polarization vectors into the chosen crystal frame and return the standard rhombohedral/tetragonal/orthorhombic (R/T/O) directions for reference. |
| `SP_INTERP.m` | Workflow for examining a series of snapshots. The script batches over all matching `.dat` files, filters polarization vectors that align with a chosen axis, records the evolution, and writes an `MP4` animation while also accumulating trajectory information for later path-density analysis. The helper routines included in this script mirror those of `SPanalysis.m` and also provide `getData`/`saveData` utilities for reading and writing the phase-field datasets. |

## Data expectations

Both scripts expect input files with the format produced by the phase-field solver: a one-line header containing the grid dimensions (`nx ny nz`) followed by rows of lattice indices and polarization components (`i j k P1 P2 P3`). The helper `getData` function (defined in `SP_INTERP.m`) removes zero-polarization entries before returning an `N×6` array.

Update the `filename` (or `filename1`) variables near the top of each script to point to your dataset. Wildcards can be used (e.g., `Polar.*.dat`) to process a sequence.

## MATLAB toolboxes

The scripts rely on functions from the MATLAB base environment and the Statistics and Machine Learning Toolbox:

* `hist3` for building 2D histograms.
* `VideoWriter` and `getframe` for saving animations (`SP_INTERP.m`).

## Typical workflow

1. Export polarization data from your phase-field simulation into the expected `.dat` format.
2. Adjust the path variables at the top of either script so they reference your dataset. For sequences, ensure the files are ordered numerically so the video frames follow the physical time/field steps.
3. (Optional) Modify the angular cut-off (`buffer`) or rotation angles (`ang1`, `ang2`, `ang3`) to isolate specific domain orientations.
4. Run the desired script inside MATLAB:
   ```matlab
   % For a single snapshot
   SPanalysis

   % For a sequence with video output
   SP_INTERP
   ```
5. Inspect the generated figures or the `SPnoFilter.mp4` animation. Use the color bars to relate density/intensity to domain population and the reference vectors (`v_R`, `v_T`, `v_O`) to identify phases.

## Customization notes

* The plotting ranges (`xlim`, `ylim`, `caxis`) are tuned for Pb0.5Sr0.5TiO3 simulations. Adjust them to match your material system.
* To include additional rotations, edit the `ang1`, `ang2`, and `ang3` variables in either script or call `vecTransform`/`vecInv` directly.
* Uncomment the reference vector plotting commands in the scripts to visualize the expected R/T/O directions on top of the density map.

Feel free to adapt the scripts to other ferroelectric systems or data formats; the helper functions can serve as a starting point for extending the workflow.
