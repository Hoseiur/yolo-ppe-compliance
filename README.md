# Retail Vision: Safety-Compliance Detection with YOLOv8

End-to-end object detection project for a Deep Learning course. A YOLOv8 model detects personal protective equipment (PPE) and missing PPE, and the detections are turned into image-level **compliance alerts**.

> **Note on scale:** results come from a CPU-scaled run (416 px images, 40% of the training set, 10 epochs, two optimizers). Absolute accuracy is lower than a full GPU run would give; the pipeline and the comparisons are the point.

## What is in this project
1. **Data audit:** class balance, object sizes, spatial distribution, ground-truth samples.
2. **Optimizer experiment:** SGD vs AdamW on YOLOv8n, same seed and settings.
3. **Held-out evaluation:** model chosen on validation, reported on the test split (mAP, per-class AP, PR/F1 curves, confusion matrix).
4. **Operating-point tuning:** confidence threshold from the F1 curve.
5. **Compliance alerts:** an image is a violation if a `no_*` class is detected; precision/recall trade-off with a balanced and a safety-first threshold.
6. **Deployment speed:** PyTorch vs ONNX on CPU.
7. **Demo video** with a COMPLIANT / VIOLATION banner.

## Results

Training runs (validation split):

```
name,weights,optimizer,lr0,epochs_run,best_epoch,precision,recall,map50,map5095,params_m,size_mb,train_min
v8n_SGD,yolov8n.pt,SGD,0.01,10,10,0.61636,0.50672,0.51286,0.3212,3.14,6.5,18.2
v8n_AdamW,yolov8n.pt,AdamW,0.001,10,10,0.57198,0.51287,0.49981,0.30462,3.14,6.5,17.3
```

Final model summary:

```
FINAL MODEL : v8n_SGD  (SGD, lr0=0.01, 3.14 M params, 6.5 MB)
VAL mAP50-95 : 0.321   mAP50: 0.523   P: 0.634   R: 0.524
Best conf threshold (F1)   : 0.22
Alert (balanced)           : {'mode': 'balanced (max F1)', 'threshold': 0.05, 'precision': 0.883, 'recall': 0.857, 'f1': 0.87, 'caught': 144, 'missed': 24, 'false_alarms': 19}
Alert (safety-first)       : {'mode': 'safety-first (recall>=95%)', 'threshold': 0.05, 'precision': 0.883, 'recall': 0.857, 'f1': 0.87, 'caught': 144, 'missed': 24, 'false_alarms': 19}
Speed                      : [{'setup': 'PyTorch FP32 - CPU', 'ms_per_image': 72.3, 'FPS': 13.8}, {'setup': 'ONNX - CPU', 'ms_per_image': 25.5, 'FPS': 39.2}]
```

Per-class metrics (test split):

```
class,precision,recall,AP50,AP50-95
Person,0.8345355999420542,0.8544698544698545,0.8879077441625546,0.6717714169538821
vest,0.7984330373917257,0.8235294117647058,0.8429382877710812,0.5757257129405899
helmet,0.7887860837373613,0.6351281112651763,0.6762774769839961,0.480790485959271
forklift,0.5411482846258147,0.7808219178082192,0.6605742017682781,0.40804196240452006
gloves,0.6232913039167384,0.6010498687664042,0.6078368160131757,0.3513938268493895
no_vest,0.8710082770538483,0.5194428553733946,0.6046921261542171,0.345872807628698
boots,0.5687112355030264,0.5966735966735967,0.5197360270201649,0.3409700456143388
no_helmet,0.7351083963701871,0.41899441340782123,0.43394142477571834,0.20493870276439802
no_boots,0.4097180789905107,0.3539325842696629,0.3186118921414205,0.18521545741973963
no_gloves,0.6518001580825464,0.2512315270935961,0.32524825168310567,0.17414942255800997
goggles,0.44518323471914417,0.4460431654676259,0.37149717917122743,0.10307280403066373
no_goggle,0.3373100506836083,0.012195121951219513,0.02502763902485298,0.014121184955116878
```

Violation alert operating points:

```
mode,threshold,precision,recall,f1,caught,missed,false_alarms
balanced (max F1),0.05,0.883,0.857,0.87,144,24,19
safety-first (recall>=95%),0.05,0.883,0.857,0.87,144,24,19
```

Inference speed:

```
setup,ms_per_image,FPS
PyTorch FP32 - CPU,72.3,13.8
ONNX - CPU,25.5,39.2
```


Figures are in `yolo_retail_cpu/figures/`, the demo video is `yolo_retail_cpu/demo_raw.mp4` (download to play), and the fully executed notebook with every output is `full_done.ipynb`.

## Repository layout
| Path | Contents |
|---|---|
| `YOLO_Retail_Vision_PPE_cpu_local.ipynb` | Notebook used for the reported results (CPU) |
| `full_done.ipynb` | The CPU notebook executed, with every output |
| `yolo_retail_cpu/figures/` | Plots, tables (CSV) and SUMMARY.txt |
| `runs/` | Ultralytics training runs (curves, weights) |

## Reproduce
```bash
python -m venv .venv
# Windows: .venv\Scripts\activate    Linux/macOS: source .venv/bin/activate
pip install ultralytics seaborn scikit-learn pandas matplotlib jupyter nbconvert ipykernel
jupyter nbconvert --to notebook --execute YOLO_Retail_Vision_PPE_cpu_local.ipynb --output full_done.ipynb --ExecutePreprocessor.timeout=-1
```
The dataset downloads automatically on first run.

## Limitations
- CPU-scaled run; only SGD and AdamW compared; one seed per run, so small differences may be noise.
- Image-level alerts only; no tracking across video frames.
- Single dataset and camera style; privacy implications of detecting people must be considered before any real deployment.

## Credits and licence
- Built with Ultralytics YOLOv8 (AGPL-3.0): https://github.com/ultralytics/ultralytics
- Dataset: Ultralytics Construction-PPE, downloaded automatically and not redistributed here. Check its licence terms on the Ultralytics docs.
- Because the code depends on Ultralytics (AGPL-3.0), this repository is released under AGPL-3.0.
