# Streamline Selection by Parcellation 

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **An automated and reproducible pipeline to extract streamlines from a tractogram based on a labeled parcellation image.**

Splits the parcellation into one binary mask per label (nibabel) and runs MRtrix3 tckedit -include for each mask in parallel, writing one tractogram per parcel; with ends_only only streamlines that terminate in the parcel are kept. Use it to get region-wise bundles from a whole-brain tractogram. The tractogram and parcellation must be in the same space.

| | |
|---|---|
| 📥 **Inputs** | `neuro/track/tck` · `neuro/parcellation/volume` |
| 📤 **Outputs** | `neuro/tcks` |
| 🐳 **Container** | `brainlife/dipy:1.4.1` |
| ⚙️ **Resources** | 1 CPUs · 8 GB memory · 2 h walltime |
| 🔗 **Run it** | [doi.org/10.25663/brainlife.app.866](https://doi.org/10.25663/brainlife.app.866) |

#### 📥 Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `tractogram` | `neuro/track/tck` | yes | Whole-brain .tck tractogram. Same space as the parcellation. |
| `parcellation` | `neuro/parcellation/volume` | yes | Labeled volumetric parcellation; each non-zero label becomes one ROI. |

#### 📤 Outputs

| Output | Datatype | Description |
|---|---|---|
| `tcks` | `neuro/tcks` | One .tck tractogram per parcellation label with the streamlines that intersect (or end in) that parcel. |

<details>
<summary><b>⚙️ Parameters</b> (1)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `ends_only` | boolean | `false` | filtering only the streamlines ending in the parcels |

</details>

📚 **References used by this App:**
- Tournier JD, Smith R, Raffelt D, et al. MRtrix3: A fast, flexible and open software framework for medical image processing and visualisation. NeuroImage 202, 116137 (2019). [doi:10.1016/j.neuroimage.2019.116137](https://doi.org/10.1016/j.neuroimage.2019.116137)

<!-- brainlife-app:end -->

An automated and reproducible pipeline to extract streamlines from a tractogram based on a labeled parcellation image. The tool supports efficient region-wise filtering using MRtrix and allows for parallel execution, making it suitable for structural connectivity analyses and parcel-based streamline extraction workflows.

## Author  
Gabriele Amorosino  
(email: gabriele.amorosino@utexas.edu)

## Description  
This workflow unpacks a labeled NIfTI segmentation file into individual binary masks using Python and then applies MRtrix's `tckedit` to extract streamlines intersecting each parcel. The user can optionally restrict to streamlines terminating within each parcel using the `--ends_only` flag. The outputs include region-specific tractograms corresponding to each segmentation label.

The pipeline is designed for containerized execution (e.g., Singularity), promoting reproducibility and compatibility across environments.

## Requirements  
Singularity

## Usage  

Running on Brainlife.io  
### Via Web UI  
- Go to Brainlife.io and search for the `app-segmentation2tracts` app.  
- Click the **Execute** tab.  
- Upload the following inputs:
  - Whole-brain tractogram (.tck)
  - Labeled parcellation file (.nii.gz)
  - (Optional) `ends_only` flag to restrict by streamline endpoints  
- Submit the job to generate per-region streamline bundles.

### Via CLI  
Install the Brainlife CLI: https://brainlife.io/docs/cli/  
Log in:
```bash
bl login
```
Run the app:
```bash
bl app run --id <app_id> --project <project_id> \
  --input tractogram:<tractogram_id> \
  --input parc:<parcellation_id> \
  --input ends_only:true
```
Replace dataset and project IDs as appropriate.

## Running Locally  
### Using a Configuration File  
Clone the repository:
```bash
git clone https://github.com/gamorosino/app-segmentation2tracts.git
cd app-segmentation2tracts
```

Prepare a config.json file:
```json
{
    "tractogram": "sub-01_dwi_tracts.tck",
    "parcellation": "sub-01_parc.nii.gz",
    "ends_only": "true",
    "output_dir": "./tracts_by_region"
}
```

Run the pipeline:
```bash
bash ./main sub-01_dwi_tracts.tck sub-01_parc.nii.gz true ./tracts_by_region
```

Outputs  
- `ROIs/` — Unpacked binary masks from the segmentation  
- `tracts_by_region/` — Streamlines intersecting each parcel (`track_<label>.tck`)

## Citation  
If you use this repository, please cite the relevant tools and frameworks:

- Tournier, J. D., Smith, R., Raffelt, D., et al. (2019). MRtrix3: A fast, flexible and open software framework for medical image processing and visualisation. *NeuroImage*, 202, 116137.
- Hayashi, S., Caron, B. A., Heinsfeld, A. S., ... & Pestilli, F. (2024). brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods*, 21(5), 809–813.
