# EDABK_YOLOV4_WeaponDetection 

## I. Tham khảo

[Github](https://github.com/ari-dasci/OD-WeaponDetection/tree/master/Weapons%20and%20similar%20handled%20objects)

[Blog](https://miai.vn/2020/05/25/yolo-series-train-yolo-v4-train-tren-colab-chi-tiet-va-day-du-a-z/)

## II. Setup hyperparameters

### 1. yolov4 config

Sử dụng file custom config: [yolov4-custom.cfg](./yolov4-custom.cfg)

- [yolo.names](./yolo.names)
- [yolo.data](./yolo.data)

Các tham số lưu ý:

- subdivisions: 32
- width: 608
- height: 608
- classes:6
- max_batches = max(num_classes*2000, 6000) = 12000
- steps=9600,10800 (bằng 0.8, 0.9 max_batches)
- filters=33 ( dòng 963, 1051, 1139). Tính bằng (num_classes+5)*3

### 2. Makefile

- GPU=1
- CUDNN=1
- OPENCV=1

## III. Achitecture

```shell
conv     32       3 x 3/ 1    608 x 608 x   3 ->  608 x 608 x  32 0.639 BF
   1 conv     64       3 x 3/ 2    608 x 608 x  32 ->  304 x 304 x  64 3.407 BF
   2 conv     64       1 x 1/ 1    304 x 304 x  64 ->  304 x 304 x  64 0.757 BF
   3 route  1 		                           ->  304 x 304 x  64 
   4 conv     64       1 x 1/ 1    304 x 304 x  64 ->  304 x 304 x  64 0.757 BF
   5 conv     32       1 x 1/ 1    304 x 304 x  64 ->  304 x 304 x  32 0.379 BF
   6 conv     64       3 x 3/ 1    304 x 304 x  32 ->  304 x 304 x  64 3.407 BF
   7 Shortcut Layer: 4,  wt = 0, wn = 0, outputs: 304 x 304 x  64 0.006 BF
   8 conv     64       1 x 1/ 1    304 x 304 x  64 ->  304 x 304 x  64 0.757 BF
   9 route  8 2 	                           ->  304 x 304 x 128 
  10 conv     64       1 x 1/ 1    304 x 304 x 128 ->  304 x 304 x  64 1.514 BF
  11 conv    128       3 x 3/ 2    304 x 304 x  64 ->  152 x 152 x 128 3.407 BF
  12 conv     64       1 x 1/ 1    152 x 152 x 128 ->  152 x 152 x  64 0.379 BF
  13 route  11 		                           ->  152 x 152 x 128 
  14 conv     64       1 x 1/ 1    152 x 152 x 128 ->  152 x 152 x  64 0.379 BF
  15 conv     64       1 x 1/ 1    152 x 152 x  64 ->  152 x 152 x  64 0.189 BF
  16 conv     64       3 x 3/ 1    152 x 152 x  64 ->  152 x 152 x  64 1.703 BF
  17 Shortcut Layer: 14,  wt = 0, wn = 0, outputs: 152 x 152 x  64 0.001 BF
  18 conv     64       1 x 1/ 1    152 x 152 x  64 ->  152 x 152 x  64 0.189 BF
  19 conv     64       3 x 3/ 1    152 x 152 x  64 ->  152 x 152 x  64 1.703 BF
  20 Shortcut Layer: 17,  wt = 0, wn = 0, outputs: 152 x 152 x  64 0.001 BF
  21 conv     64       1 x 1/ 1    152 x 152 x  64 ->  152 x 152 x  64 0.189 BF
  22 route  21 12 	                           ->  152 x 152 x 128 
  23 conv    128       1 x 1/ 1    152 x 152 x 128 ->  152 x 152 x 128 0.757 BF
  24 conv    256       3 x 3/ 2    152 x 152 x 128 ->   76 x  76 x 256 3.407 BF
  25 conv    128       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 128 0.379 BF
  26 route  24 		                           ->   76 x  76 x 256 
  27 conv    128       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 128 0.379 BF
  28 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  29 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  30 Shortcut Layer: 27,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  31 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  32 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  33 Shortcut Layer: 30,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  34 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  35 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  36 Shortcut Layer: 33,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  37 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  38 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  39 Shortcut Layer: 36,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  40 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  41 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  42 Shortcut Layer: 39,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  43 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  44 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  45 Shortcut Layer: 42,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  46 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  47 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  48 Shortcut Layer: 45,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  49 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  50 conv    128       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 128 1.703 BF
  51 Shortcut Layer: 48,  wt = 0, wn = 0, outputs:  76 x  76 x 128 0.001 BF
  52 conv    128       1 x 1/ 1     76 x  76 x 128 ->   76 x  76 x 128 0.189 BF
  53 route  52 25 	                           ->   76 x  76 x 256 
  54 conv    256       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 256 0.757 BF
  55 conv    512       3 x 3/ 2     76 x  76 x 256 ->   38 x  38 x 512 3.407 BF
  56 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
  57 route  55 		                           ->   38 x  38 x 512 
  58 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
  59 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  60 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  61 Shortcut Layer: 58,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  62 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  63 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  64 Shortcut Layer: 61,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  65 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  66 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  67 Shortcut Layer: 64,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  68 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  69 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  70 Shortcut Layer: 67,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  71 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  72 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  73 Shortcut Layer: 70,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  74 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  75 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  76 Shortcut Layer: 73,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  77 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  78 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  79 Shortcut Layer: 76,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  80 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  81 conv    256       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 256 1.703 BF
  82 Shortcut Layer: 79,  wt = 0, wn = 0, outputs:  38 x  38 x 256 0.000 BF
  83 conv    256       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 256 0.189 BF
  84 route  83 56 	                           ->   38 x  38 x 512 
  85 conv    512       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 512 0.757 BF
  86 conv   1024       3 x 3/ 2     38 x  38 x 512 ->   19 x  19 x1024 3.407 BF
  87 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
  88 route  86 		                           ->   19 x  19 x1024 
  89 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
  90 conv    512       1 x 1/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.189 BF
  91 conv    512       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x 512 1.703 BF
  92 Shortcut Layer: 89,  wt = 0, wn = 0, outputs:  19 x  19 x 512 0.000 BF
  93 conv    512       1 x 1/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.189 BF
  94 conv    512       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x 512 1.703 BF
  95 Shortcut Layer: 92,  wt = 0, wn = 0, outputs:  19 x  19 x 512 0.000 BF
  96 conv    512       1 x 1/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.189 BF
  97 conv    512       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x 512 1.703 BF
  98 Shortcut Layer: 95,  wt = 0, wn = 0, outputs:  19 x  19 x 512 0.000 BF
  99 conv    512       1 x 1/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.189 BF
 100 conv    512       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x 512 1.703 BF
 101 Shortcut Layer: 98,  wt = 0, wn = 0, outputs:  19 x  19 x 512 0.000 BF
 102 conv    512       1 x 1/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.189 BF
 103 route  102 87 	                           ->   19 x  19 x1024 
 104 conv   1024       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x1024 0.757 BF
 105 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
 106 conv   1024       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x1024 3.407 BF
 107 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
 108 max                5x 5/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.005 BF
 109 route  107 		                           ->   19 x  19 x 512 
 110 max                9x 9/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.015 BF
 111 route  107 		                           ->   19 x  19 x 512 
 112 max               13x13/ 1     19 x  19 x 512 ->   19 x  19 x 512 0.031 BF
 113 route  112 110 108 107 	                   ->   19 x  19 x2048 
 114 conv    512       1 x 1/ 1     19 x  19 x2048 ->   19 x  19 x 512 0.757 BF
 115 conv   1024       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x1024 3.407 BF
 116 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
 117 conv    256       1 x 1/ 1     19 x  19 x 512 ->   19 x  19 x 256 0.095 BF
 118 upsample                 2x    19 x  19 x 256 ->   38 x  38 x 256
 119 route  85 		                           ->   38 x  38 x 512 
 120 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 121 route  120 118 	                           ->   38 x  38 x 512 
 122 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 123 conv    512       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 512 3.407 BF
 124 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 125 conv    512       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 512 3.407 BF
 126 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 127 conv    128       1 x 1/ 1     38 x  38 x 256 ->   38 x  38 x 128 0.095 BF
 128 upsample                 2x    38 x  38 x 128 ->   76 x  76 x 128
 129 route  54 		                           ->   76 x  76 x 256 
 130 conv    128       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 128 0.379 BF
 131 route  130 128 	                           ->   76 x  76 x 256 
 132 conv    128       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 128 0.379 BF
 133 conv    256       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 256 3.407 BF
 134 conv    128       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 128 0.379 BF
 135 conv    256       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 256 3.407 BF
 136 conv    128       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x 128 0.379 BF
 137 conv    256       3 x 3/ 1     76 x  76 x 128 ->   76 x  76 x 256 3.407 BF
 138 conv     33       1 x 1/ 1     76 x  76 x 256 ->   76 x  76 x  33 0.098 BF
 139 yolo
[yolo] params: iou loss: ciou (4), iou_norm: 0.07, obj_norm: 1.00, cls_norm: 1.00, delta_norm: 1.00, scale_x_y: 1.20
nms_kind: greedynms (1), beta = 0.600000 
 140 route  136 		                           ->   76 x  76 x 128 
 141 conv    256       3 x 3/ 2     76 x  76 x 128 ->   38 x  38 x 256 0.852 BF
 142 route  141 126 	                           ->   38 x  38 x 512 
 143 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 144 conv    512       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 512 3.407 BF
 145 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 146 conv    512       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 512 3.407 BF
 147 conv    256       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x 256 0.379 BF
 148 conv    512       3 x 3/ 1     38 x  38 x 256 ->   38 x  38 x 512 3.407 BF
 149 conv     33       1 x 1/ 1     38 x  38 x 512 ->   38 x  38 x  33 0.049 BF
 150 yolo
[yolo] params: iou loss: ciou (4), iou_norm: 0.07, obj_norm: 1.00, cls_norm: 1.00, delta_norm: 1.00, scale_x_y: 1.10
nms_kind: greedynms (1), beta = 0.600000 
 151 route  147 		                           ->   38 x  38 x 256 
 152 conv    512       3 x 3/ 2     38 x  38 x 256 ->   19 x  19 x 512 0.852 BF
 153 route  152 116 	                           ->   19 x  19 x1024 
 154 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
 155 conv   1024       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x1024 3.407 BF
 156 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
 157 conv   1024       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x1024 3.407 BF
 158 conv    512       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x 512 0.379 BF
 159 conv   1024       3 x 3/ 1     19 x  19 x 512 ->   19 x  19 x1024 3.407 BF
 160 conv     33       1 x 1/ 1     19 x  19 x1024 ->   19 x  19 x  33 0.024 BF
 161 yolo
[yolo] params: iou loss: ciou (4), iou_norm: 0.07, obj_norm: 1.00, cls_norm: 1.00, delta_norm: 1.00, scale_x_y: 1.05
nms_kind: greedynms (1), beta = 0.600000 
Total BFLOPS 127.310 
avg_outputs = 1047617 
```

## IV. Kết quả chạy

### 1. File weights

Dưới 1000 vòng lặp => save weights mỗi 100 vòng

Trên 1000 vòng lặp => save weights mỗi 10,000 vòng

[yolov4-custom_last.weights](https://drive.google.com/drive/folders/1j8IuxE9mM1FJqEwpBOwejllq90ilzqis?usp=sharing)

### 2. File logs

[yolov4.log](https://drive.google.com/file/d/1jFuoDcPgVR3Xx9ywdRtT26RAIka0Lw97/view?usp=sharing)

### 3. Loss

![train loss](./training_loss_plot.png)

<p text-align='center'>

_current avg loss = 1.5705_

_iteration = 1400_

</p>

### 4. Test với ảnh test

![](./images/test1.png)

![](./images/test2.png)

![](./images/test3.png)

![](./images/test4.png)