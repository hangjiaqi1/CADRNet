# Multi-Scan Multi-Object Instance Reasoning Segmentation DataLoader

PyTorch DataLoader for Multi-Scan dataset, supporting multi-object instance reasoning segmentation with full point clouds and per-object masks.

## 📁 Directory Structure

```
project_root/
├── Multi-scan-dataloader/
│   ├── dataset_reason3d_format.py    # Main DataLoader
│   └── requirements.txt              # Dependencies reference
│
├── reason3d/
│   └── Multi-Scan/
│       ├── Multi-Scan-1-v2.json     # 1-object reasoning data
│       ├── Multi-Scan-2-v2.json     # 2-object reasoning data
│       └── Multi-Scan-3-v2.json     # 3-object reasoning data
│
└── scans/
    ├── scene0000_00/
    │   ├── scene0000_00_vh_clean_2.labels.ply          # Point cloud with labels
    │   ├── scene0000_00_vh_clean_2.0.010000.segs.json  # Segmentation indices
    │   └── scene0000_00.aggregation.json               # Object aggregation
    ├── scene0000_01/
    │   └── ...
    └── ...
```

## 🔧 Environment Requirements

### Python Version
- Python 3.8 or higher

### Required Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| torch | 2.0.1+cu118 | Deep learning framework |
| numpy | 1.24.3 | Numerical operations |
| plyfile | 0.9 | PLY file reading |

### Optional Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| torchvision | 0.15.2+cu118 | Vision utilities (optional) |
| open3d | 0.17.0 | Point cloud visualization (optional) |
| pandas | 2.0.3 | Data analysis (optional) |
| tqdm | 4.66.1 | Progress bars (optional) |

## 📦 Data Requirements

### ScanNet Dataset

The scans/ directory must contain the ScanNet dataset with the following structure for each scene:

```
scans/
└── scene0000_00/
    ├── scene0000_00_vh_clean_2.labels.ply          # Point cloud with RGB and labels
    ├── scene0000_00_vh_clean_2.0.010000.segs.json  # Segmentation indices
    └── scene0000_00.aggregation.json               # Object aggregation information
```

**Required Files per Scene:**
- `*_vh_clean_2.labels.ply`: 3D point cloud with vertex positions (x, y, z), RGB colors, and semantic labels
- `*_vh_clean_2.0.010000.segs.json`: Segmentation indices mapping each point to a segment ID
- `*.aggregation.json`: Object aggregation mapping segment IDs to object IDs with class labels

**Note:** Download the ScanNet dataset and ensure all three file types are available for each scene referenced in the Multi-Scan JSON files.

## 🚀 Usage

### Basic Usage

```python
from dataset_reason3d_format import MultiScanReason3DDataset, collate_reason3d
from torch.utils.data import DataLoader

# Create dataset
dataset = MultiScanReason3DDataset(
    reasoning_dir="d:/scannet/reason3d/Multi-Scan",
    scans_dir="d:/scannet/scans",
    load_point_cloud=True,
    training=True
)

# Create dataloader
dataloader = DataLoader(
    dataset,
    batch_size=4,
    shuffle=True,
    num_workers=0,
    collate_fn=collate_reason3d
)

# Iterate
for batch in dataloader:
    coords = batch['coords']              # [B*N, 4]
    class_labels = batch['class_labels']  # List[List[str]]
    gt_pmasks = batch['gt_pmasks']        # List[List[Tensor]]
```

### Test Code

```bash
# Windows
$env:KMP_DUPLICATE_LIB_OK="TRUE"
python dataset_reason3d_format.py --reasoning_dir "d:/scannet/reason3d/Multi-Scan" --scans_dir "d:/scannet/scans"

# Linux/Mac
export KMP_DUPLICATE_LIB_OK=TRUE
python dataset_reason3d_format.py --reasoning_dir "d:/scannet/reason3d/Multi-Scan" --scans_dir "d:/scannet/scans"
```
