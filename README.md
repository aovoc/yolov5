
This repository bases on Ultralytics open-source YOLO repository https://github.com/ultralytics/yolov5. changes model architecture such as replacing upsample with transpose, setting upsample's ceil_mode to True in order to convert to caffe correctly and deploy on edge device.


## Pretrained Checkpoints

| Model | AP<sup>val</sup> | AP<sup>test</sup> | AP<sub>50</sub> | Speed<sub>GPU</sub> | FPS<sub>GPU</sub> || params | FLOPs |
|---------- |------ |------ |------ | -------- | ------| ------ |------  |  :------: |
| YOLOv5s-origin([ckpt](https://drive.google.com/open?id=1Drs_Aiu7xx6S-ix95f9kNsA6ueKRpN2J))                  | 35.5     | 35.5     | 55.0     | **2.1ms** | **476** || 7.1M   | 12.6B
| YOLOv5s-ours([ckpt](https://drive.google.com/file/d/1F6NFPNFC_IMTb3JjS8X-2v3nFsPRh_oB/view?usp=sharing))    | 34.9     | -     | 54.6     | **2.1ms** | **476** || 7.1M   | 12.6B

** AP<sup>test</sup> denotes COCO [test-dev2017](http://cocodataset.org/#upload) server results, all other AP results in the table denote val2017 accuracy.  
** All AP numbers are for single-model single-scale without ensemble or test-time augmentation. Reproduce by `python test.py --img 736 --conf 0.001`  
** Speed<sub>GPU</sub> measures end-to-end time per image averaged over 5000 COCO val2017 images using a GCP [n1-standard-16](https://cloud.google.com/compute/docs/machine-types#n1_standard_machine_types) instance with one V100 GPU, and includes image preprocessing, PyTorch FP16 image inference at --batch-size 32 --img-size 640, postprocessing and NMS. Average NMS time included in this chart is 1-2ms/img.  Reproduce by `python test.py --img 640 --conf 0.1`  
** All checkpoints are trained to 300 epochs with default settings and hyperparameters (no autoaugmentation). 


## Requirements

Python 3.7 or later with all `requirements.txt` dependencies installed, including `torch >= 1.5`. To install run:
```bash
$ pip install -U -r requirements.txt
```


## Inference

Downloading **YOLOv5s-ours** (https://drive.google.com/file/d/1F6NFPNFC_IMTb3JjS8X-2v3nFsPRh_oB/view?usp=sharing) as yolov5s.pt.

Inference can be run on most common media formats. Results are saved to `./inference/output`.
```bash
$ python detect.py --source file.jpg  # image 
                            file.mp4  # video
                            ./dir  # directory
                            0  # webcam
                            rtsp://170.93.143.139/rtplive/470011e600ef003a004ee33696235daa  # rtsp stream
                            http://112.50.243.8/PLTV/88888888/224/3221225900/1.m3u8  # http stream
```

To run inference on examples in the `./inference/images` folder:

```bash
$ python detect.py --source ./inference/images/ --weights yolov5s.pt --conf 0.4

```


## Reproduce Our Training

Download [COCO](https://github.com/ultralytics/yolov5/blob/master/data/get_coco2017.sh), install [Apex](https://github.com/NVIDIA/apex) and run command below. Training times for YOLOv5s/m/l/x are 2/4/6/8 days on a single V100 (multi-GPU times faster). Use the largest `--batch-size` your GPU allows (batch sizes shown for 16 GB devices).
```bash
$ python train.py --data coco.yaml --cfg yolov5s.yaml --batch-size 196
```
