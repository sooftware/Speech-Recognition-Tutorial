# Deep Speech: Scaling up end-to-end speech recognition  
  
![title](https://postfiles.pstatic.net/MjAyMDAyMjNfMTQ1/MDAxNTgyMzkwNTAyMzI5.qYtgph7nxA4sOZlHd8-dw9dOmXeEZvz3zifBjyMYNaUg.uX_2ZheLYRPxPHJogipB50IrpYX7yYi5jNPWbWGv2sog.PNG.sooftware/image.png?type=w773)  
  
https://arxiv.org/pdf/1412.5567.pdf (Awni Hannun et al. 2014)  
  
## Abstract  
  
 본 논문은 2014년도에 나온 논문이다. 논문을 읽어본 결과, 당시에는 음성인식에 End-to-End 방식의 딥러닝을 적용한 사례가 없던 모양이다. (또는 주목할만한 성과가 나오지 않았던 모양이다.) 본 논문에서는 End-to-End 방식으로 Switchboard Hub5'00 데이터셋에서 16.0%의 Error Rate를 기록하며 **State-Of-The-Art** (SOTA) 를 달성한 성과를 밝히고 있다. 기존 음성인식의 traditional한 방식은 전처리 과정이 상당히 많이 필요했지만, 이러한 과정 없이 데이터와 레이블만을 이용한 End-to-End 방식으로 이러한 성과를 냈음을 거듭 강조하고 있다. 그리고 이전 방식으로는 노이즈가 있는 환경에서 급격히 떨어졌는데 반하여, 본 논문 방식으로는 노이즈가 있는 환경에서도 좋은 성능을 기록했다고 한다. 즉, 기존 방식보다 더 간단하면서도 좋은 성능을 기록한 End-to-End 방식의 Speech-Recognition 모델을 소개하는 논문이다.  

## Introduction

Abstract에서 강조한 내용을 다시 한번 강조한다. 기존 traditional한 방식은 전문가들의 손이 많이 가야했다. (노이즈 필터링 등..) 하지만 그러한 노력에도 불구하고, 실제 노이즈가 낀 상황에서의 인식률은 좋지 못했다. 하지만 End-to-End 방식으로 이러한 2가지의 단점을 개선할 수 있다고 주장한다.   
  
 물론 End-to-End 방식으로 가기 위해서는 몇가지 고려해야할 사항들이 있었지만, 본 논문에서는 기존 연구 결과들을 참고하여 End-to-End 방식을 시도할 수 있었다고 말한다. 그리고, 본 논문에서는 RNN 모델을 사용했다. 뒤에 더 자세히 설명하겠지만 본 논문에서는 학습 시간을 단축시키기 위해 많은 고려를 한 것으로 보인다.  
   
## RNN Training Setup
  
![model](https://postfiles.pstatic.net/MjAyMDAyMjNfMjcy/MDAxNTgyMzkwNTUyMDky.Btvd1sq4aVjkLzhMoXlSY5zrjNa8WyfqUAHi7NeFtcog.mFCsfZv-R3VDe_Gt-PZYr7R-Ymc3x84GWlcRX7-SZyIg.PNG.sooftware/image.png?type=w773)  
  
해당 챕터에서는 자신들이 어떤 식으로 모델을 구성했는지에 대해 설명한다.  
모델의 핵심은 RNN으로 구성되어 있으며, 트레이닝 셋은 ${(x_1, y_1), (x_2, y_2), ... (x_t, y_t)}$ 와 같은 딕셔너리 형식으로 구성했다고 한다. (x는 스펙트로그램, y는 문자로 구성 )    
  
모델은 총 5개의 히든 레이어로 구성했다고 한다.
논문을 읽으면서 모델 아키텍쳐가 상당히 특이하다고 생각했다.  

현재 내가 알고있는 방식과는 사뭇 다른 방식이였는데, 히든 레이어 중 1, 2, 3번째 레이어는 병렬적 (Parallel) 하게 처리하기 위해 서로 독립적으로 포워딩 된다고 한다. RNN 아키텍쳐를 사용하게 되면 이전 셀의 아웃풋이 필요하기 때문에 어쩔 수 없이 병렬처리의 한계점이 있기 때문에 학습 속도 개선을 위해 각 인풋을 독립적으로 처리했다고 한다.    
 여기서도 나는 본 논문이 학습 시간을 단축시키기 위해 상당히 노력했다는 인상을 받았다.   
  
![forward](https://postfiles.pstatic.net/MjAyMDAyMjNfMTgz/MDAxNTgyMzkwNTIyMDQ0.Tq2hNX4U6a4mmT4V3f2sWemnruUnzCuLJYP5v5x8LlEg.8oF99BbkoyhYbmWQL0dD5LiWhNl61qe33P8OnGOG_Hsg.PNG.sooftware/image.png?type=w773)  
   
위의 수식을 통해 포워딩이 진행되는데, 여기서 g는 최소 0, 최대 20의 값을 가지는 ReLU 함수이다.  
그리고 4번째 레이어에서는 Bidirectional-RNN으로 구성했다.  
  
![Bi-RNN](https://postfiles.pstatic.net/MjAyMDAyMjNfMjI0/MDAxNTgyMzkwNTMyNzM2.yNOOR59yjQmH7I1v8h-YtNJ-S8JSf5q9oSiMHn1JGcAg.mrjf5ofIgIa2CQBbzZZG0ncV7JxxxR9pRcXY5ZzaG00g.PNG.sooftware/image.png?type=w773)  
  
(f)는 정방향 (forward), (b)는 역방향 (backward)을 표현한 것이다.  
이때 주의할 점으로는, forward는 t = 1 에서 t = T 방향으로 흐르고, backward는 t = T에서 t = 1 방향으로 흐른다는 점이다.  
  
![5-layer](https://postfiles.pstatic.net/MjAyMDAyMjVfMzkg/MDAxNTgyNjIxNjY0MzMw.CdMnrGnCNt8FrFb102hZFmRldRA1Xp_0kpWoFUbUv4sg.-VSEzem8ZH_3ry2W29awhSRASuz4Cb0MjIJnmwp6zcEg.PNG.sooftware/image.png?type=w773)  

그리고 마지막 5번째 레이어는 이렇게 forward, backward의 결과에 웨이트를 주고 1, 2, 3 번째 레이어와 동일한 ReLU를 활성화 함수로 사용했다.  
  
![softmax](https://postfiles.pstatic.net/MjAyMDAyMjNfODIg/MDAxNTgyMzkwNTQxMjI0.bn6ow6MHo58KaEaVu33JM8rzSvIYZqxjRpJMeKauUgsg.QumDhUt_Kx0Gqv_psT9xcHXmr44HLcGtELb7BkphlKsg.PNG.sooftware/image.png?type=w773)  
  
그리고 이렇게 나온 결과는 Softmax 함수에 넣어서 최종적으로 Classfication을 진행한다. 또한 loss 계산시에는 CTC loss를 사용했다.    
  
여기서 또 특이했던 점으로, LSTM이 아닌 기본 RNN을 사용했다는 점이다.  
그 이유로 본 논문에서는 LSTM의 단점은 메모리가 많이 소요되고, 학습이 오래걸린다는 점을 꼽았다.  
  
총 5개의 히든 레이어 중 실제 RNN 계층은 1개 뿐이라는 점과, LSTM이 아닌 RNN을 사용했다는 점이 인상깊었다.  
최근 연구에서는 기본 RNN을 사용하는 모습을 거의 볼 수 없는데, 당시에는 아직 GPU의 성능이 그리 좋지 않던 때라 그런지 학습 속도에 대해 굉장히 고려를 많이한 모습이 보였다.  
  
  
### Regularization
  
본 논문은 학습 시, 드랍아웃 비율을 5 - 10%정도를 유지했다고 한다.  
그리고 Spectrogram의 프레임 길이는 10ms, 포워딩은 5ms를 사용했다.  
(해당 부분은 오류가 있을수도 있습니다)  
  
통상적으로 음성 인식에서 프레임 길이는 20 - 40ms를 사용하기 때문에 프레임 길이가 상당히 짧다고 생각했다.  
해당 부분은 다른 이유가 있어서 짧게 한 건지, 당시에 프레임 길이에 대한 연구가 현재보다 덜 발달해서 그런 것인지는 확인을 해봐야 할 듯 하다.  
  
### Language Model

본 논문의 모델은 성능 테스트시에, 정확히 맞추거나 그럴싸하게 틀렸다고 한다.  
![performance-test](https://postfiles.pstatic.net/MjAyMDAyMjNfOTEg/MDAxNTgyMzkwNTYzMjQ4.k-V_c6x3VVmGMV8CBoxQ4c-pDxUPDKVaYr0ibScfYmUg.cLZNNBPSqZJiRrMTRtX9PTyn6IMS0k4v5o2tp8Y4_4Qg.PNG.sooftware/image.png?type=w773)  

arther => are there, n tickets => any tickets 등 꽤나 말이 되도록 틀린 것을 볼 수 있다.    
본 논문은 이보다 더 정확한 인식을 위하여 N-gram Language Model을 사용했다고 한다.  
  
매우 방대한 텍스트 Corpus로 N-gram language model을 학습시켰으며, 해당 언어 모델은 다음 공식에 사용됐다.  
  
![scoring](https://postfiles.pstatic.net/MjAyMDAyMjNfMTY1/MDAxNTgyMzkwNTcxMjQ0.cLKWNiiavUqrxHaBijz7yBT90bD-7QwqpA9My0qtc6Ug.67kMZDDcCx0HnvPj6hKRbAmAsuXhRhTT1Lihbx-TBKcg.PNG.sooftware/image.png?type=w773)  
  
여기서 알파, 베타는 설정 가능한 파라미터이다.  
본 논문에서는 성능을 향상시키기 위해 빔서치를 사용했는데, 이때 빔 사이즈를 1,000 - 8,000으로 상당히 크게 준 것을 볼 수 있었다.  
이후에 나온 논문들을 봤을 때, 빔 사이즈 단위는 기껏 해봐야 수십 정도였는데 본 논문은 상당히 큰 빔 사이즈를 사용한 것을 볼 수 있었다.  
  
## Optimizations  
  
해당 장에서는 어떻게 최적화를 했는지에 대해 설명하고 있다.  
주로 빠른 학습을 시키기 위해 어떤 노력을 했는지를 설명했다.  
  
### Data Parallelism 
  
데이터를 효과적으로 처리하기 위해 2-level data parallelism을 사용했다고 한다.  
  
미니배치 단위로 처리를 했는데, 이때 배치의 크기를 GPU 메모리 한계까지 사용했다고 한다.  
  
또한, 학습을 빨리하기 위해 NMT와 같은 Text-NLP에서 많이 사용되는, 길이 순으로 정렬해서 비슷한 길이끼리 배치로 묶었다고 한다. 이렇게 비슷한 길이끼리 배치로 묶게 되면, 배치 안에서 Max Length를 맞추기 위해 PAD token을 최소화 할 수 있다.  
   
#

본 논문은 음성 인식의 기반을 다진 논문인지라 논문에 소개된 대부분의 내용이 음성인식 튜토리얼 내용과 비슷했습니다.  
해당 논문 리뷰는 여기까지만 하겠습니다.  
  

