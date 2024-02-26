# Robot-Butler

## 3D Обнаружение 

Для точного и надежного определения расположения объектов вокруг робота необходимо использовать несколько датчиков (изображения и облако точек). С этим хорошо справляется модель BEVFusion (https://arxiv.org/abs/2205.13542) (на датасете nuScenes test mAP = 70.23, NDS = 72.88). На основе данной модели, реализовала свое решение. 

## Для запуска требуется:

Для начала следует собрать Docker образ со всеми конфигурациями

```
docker build . -t name:tag
```

Для отладки, обучения и тестирования модели соберем контейнер с помощью docker-compose.yaml

```
docker-compose up 3detection
```

## Загрузка данных

Датасет можно загружить и предобработать аналогично [data](https://github.com/open-mmlab/mmdetection3d/blob/1.0/docs/en/datasets/nuscenes_det.md) 

## Обучение 

```
cd bevfusion

torchpack dist-run -np 8 python tools/train.py configs/nuscenes/det/transfusion/secfpn/camera+lidar/swint_v0p075/convfuser.yaml --model.encoders.camera.backbone.init_cfg.checkpoint pretrained/swint-nuimages-pretrained.pth --load_from pretrained/lidar-only-det.pth 
```
Для расчета метрик нужно запустить tools/test.py после обучения

## Тестирование 

Можно загрузить веса `./tools/download_pretrained.sh`
Для тестирование модели нужно выполнить следующую команду

```
torchpack dist-run -np 8 python tools/test.py configs/nuscenes/det/transfusion/secfpn/camera+lidar/swint_v0p075/convfuser.yaml pretrained/bevfusion-det.pth --eval bbox
```
