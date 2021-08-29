**Final Branch**

- Encoder와 Decoder Skip Connection에 **CBAM**을 적용
- **ASR Transformer**에 사용하였던 ScheduleAdam을 사용
- Self Attention 적용한 **Discriminator** 적용
- Loss를 wSDR이 아닌 **SI-SNR**로 바꿈(DCCRN꺼 그대로)
> `python main.py --gpu(or MultiGPU) --batch-size ** --warm-steps (batch에 따라 유연성있게) --epochs 40
> --decay_epoch 20`

## DCUnet

 1. Noisy Spectrogram -> Clean Spectrogram(Mask)

![1](https://user-images.githubusercontent.com/76771847/127819187-c25d1db2-0504-4c60-a0e8-2422d658e3d6.png)

 2. Tanh을 사용했을 경우 Bounded Region

![2](https://user-images.githubusercontent.com/76771847/127819335-b0467ac3-66a8-4d59-bb73-20be048ddf8f.png)

 3. Real-Img 따로 conv 수행 

![3](https://user-images.githubusercontent.com/76771847/127819646-5d76de1b-024d-4184-a4bd-3e283522ac6a.png)

 4. Architecture(Unet)
 
![4](https://user-images.githubusercontent.com/76771847/127819727-7e7c7b8e-b915-41eb-b9d3-817d215c7bda.png)

## Reference

paper: https://openreview.net/pdf?id=SkeRTsAcYm

Unet: https://arxiv.org/abs/1505.04597

Phase: https://angeloyeo.github.io/2019/10/11/Fourier_Phase.html
(Phase와 Magnitude 지식 필요)

**Code: https://github.com/pheepa/DCUnet/blob/master/dcunet.ipynb**

Code: https://github.com/sweetcocoa/DeepComplexUNetPyTorch

Code: https://github.com/chanil1218/DCUnet.pytorch

Code: https://github.com/sweetcocoa/DeepComplexUNetPyTorch/tree/c68510a4d822f19fa366f1da84eff8c0c25ff88a

## Implement

**Training**

> `python main.py --epochs 100 --gpu 0 --batch-size 4
--clean-train-dir 'PATH' --noisy-train-dir 'PATH' --noisy-valid-dir 'PATH --clean-valid-dir 'PATH --sample-rate (DATA)`

**Evaluate**
> `python main.py -e(or --evaluate) --resume (PATH) --noisy-test-dir (PATH) --clean-test-dir (PATH)`

**Generate**
> `python main.py -g(or --generate) --resume (PATH) --denoising-file (file)`

## Result

- **PESQ: 3.1231 기록**

| Mix | predict | GT |
|---|---|---|
| [mixture.wav](./example/mixed.wav?raw=true) |  [predict.wav](./example/predict.wav?raw=true)  |  [GT.wav](./example/GT.wav?raw=true)  |

## Test

LAS model 기반으로 Clovacall Dataset ASR

**CER(Character Error Rate)**

| Clean | Mixed | Denoising |
|---|---|---|
|**21%**|**106%**|**39%**|

- 106%: Predict Length가 Target Length보다 작을 경우(e.g Target: 안녕하세요 / Predict: 그래)
- 약 **70%** 감소 시키는 효과를 보였음