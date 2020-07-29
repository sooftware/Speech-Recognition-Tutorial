# 「STATE-OF-THE-ART SPEECH RECOGNITION WITH SEQUENCE-TO-SEQUENCE MODEL」 Review
  
![title](https://postfiles.pstatic.net/MjAyMDAzMjRfNjEg/MDAxNTg1MDI5MjM0NzE4.Erz3moRU6RYrvzJltHM-pZ8VS454S_ix0MMob30IT9Ig.0wAmtiJGW_QIIqWabuAESuw0B-kJCGa4C6Mvb7vQPVQg.PNG.sooftware/image.png?type=w773)  
https://arxiv.org/abs/1712.01769  
  
본 논문은 제가 진행하는 [프로젝트](https://github.com/sh951011/Korean-Speech-Recognition)의 Contributor 분께서 추천해주신 논문으로, 본 논문에서 적용한 Multi-Head Attention을 적용하여 인식률이 향상되었습니다. 또한 본 논문에서는 Word-Piece를 사용하지만, 한국어에서는 Word-Piece 적용시 성능이 저하된다고 합니다. [Show Issue](https://github.com/sh951011/Korean-Speech-Recognition/pull/9)

## Abstract
  
논문 이름에서부터 밝히듯이, 500h Voice Search 분야에서 **State-Of-The-Art (SOTA)** 를 달성한 논문입니다.  
 어텐션 기반의 Seq2seq구조인 Listen, Attend and Spell (LAS) 아키텍쳐를 사용했습니다. LAS 구조는 이전에 음향 모델, 발음 모델, 언어 모델로 구성된 방식에서 하나의 Neural Network로 End-to-End 학습이 가능한 구조입니다.   
   
 본 논문에서는 grapheme (문자 단위) 모델이 아닌 word-piece (단어 단위) 모델을 사용했으며, Multi-Head Attention을 도입했다고 합니다. 그 외에도 최적화를 위해 Synchronous training, scheduled sampling, label smoothing 등을 사용했다고 밝힙니다.  
   
특이한 점으로 빠른 인식 및 학습을 위해 인코더 부분에 Bidirectional-LSTM이 아닌 Unidirectional-LSTM을 사용했다고 합니다.  
  
## Introduction
  
Sequence-to-Sequence 모델은 Autonomic Speech Recognition (ASR) Task에서 탁월한 성능을 보여주었습니다.   
2015년에 「Listen, Attend and Spell」 논문에서 Seq2seq 아키텍처를 도입한 이후로 이 글을 쓰는 지금 2020년까지도 관련 논문들이 많이 나오고 있고 SOTA 모델에서도  Sequence-to-Sequence 모델이 많이 점유하고 있습니다.  
  
Neural Machine Translation 분야에서 엄청난 성능을 자랑하는 Transformer가 Speech 분야에서는 Transformer 모델이 다소 부진한 듯 합니다. (NMT에서의 압도적인 성능에 비해서입니다 ㅎㅎ..)   
  
제가 네이버 AI 해커톤 참여 당시, 멘토분께서 Transformer는 데이터가 적을 때 성능이 그렇게 좋지 않다고 하셨습니다. 아마 데이터가 적은 음성 분야라 더 두드러지는 특징이 아닐까 싶습니다. 실제로 네이버 대회 당시 100시간이라는 한정된 데이터로 진행을 하다보니, Transformer를 사용한 팀들이 어텐션 기반의 Seq2seq 모델을 사용한 팀들에게 밀리는 현상이 있었습니다. 본 논문도 Transformer보다 뒤에 나온 논문이지만, Transformer가 아닌 Seq2seq 기반으로 모델을 구성한 것을 볼 수 있습니다. 단, Transformer를 제안한 「Attention Is All You Need」 논문에서 제안된 **Multi-Head Attention**을 사용했습니다. 또한 뒤에서 다룰 **Word-Piece Model (WPM)**, **Scheduled Sampling (SS)**, **label smoothing**, **Asynchronous SGD**, **language model** 등을 사용했습니다. 자세한 내용은 뒤에서 다루겠습니다.

## System Overview

### Basic LAS Model

![las-model](https://postfiles.pstatic.net/MjAyMDAzMjRfMzgg/MDAxNTg1MDI5MjUyNzIx.A_WNIScwOfaYzJad-l7Hd62NVOkizEQZQhla-zH-lGUg.wwiDAnHkhNJmd_pVcs8sCp4mC68pxGpIDPb7dOvDJW4g.PNG.sooftware/image.png?type=w773)  

LAS 모델을 간략하게 설명해줍니다. Encoder(Listener)는 입력으로 들어온 특징 벡터를 ***h***라는 higher-level feature로 변환해줍니다. 이러한 ***h***를 가지고 Decoder(Speller)는 어텐션 매커니즘과 함께 출력을 만들어 냅니다. 이에 대한 자세한 설명은 [「Listen, Attend and Spell」 Paper Review](https://github.com/sh951011/Paper-Review/blob/master/Review/Listen%2C%20Attend%20and%20Spell.md)을 참고하시면 좋을 것 같습니다.

### Word-Piece Models

LAS 모델은 아웃풋의 단위가 보통 grapheme (character)이였습니다.  
  
하지만 이러한 구조는 OOV (Out-Of-Vocabulary) 문제가 발생시킬 수 있습니다. 이를 위한 대안으로, grapheme 단위가 아닌 phoneme (음소) 단위가 있습니다만, phoneme 단위의 단점은 추가적인 발음 모델과 언어 모델이 필요하다는 점입니다. 또한 본 논문에서 실험한 바로는, phoneme 단위는 grapheme 단위 모델보다 성능이 좋지 못했다고 합니다.
  
그래서 본 논문은 Word-Piece Model (WPM)을 사용했다고 합니다. WPM은 OOV 문제를 해결할 수 있습니다. 또한, 일반적으로 word-level의 언어 모델은 graphme-level의 언어 모델보다 Perplexity가 낮다고 합니다. (Perplexity가 낮을수록 우수한 모델입니다.) 그래서 본 논문에서는 이러한 경향을 봤을 때, WPM을 사용하게 되면 디코딩 과정에서 더 우수한 성능이 나오지 않을까라고 예상했다고 합니다. 
  
또한 이러한 WPM으로 진행하게 되면, 기존 문자 단위에 비해 더 적은 디코딩 스텝으로 계산되기 때문에 학습 및 추론 속도가 향상되는 장점도 가지게 됩니다. 그리고 결과적으로, WPM을 사용한 모델이 다른 모델보다 더 좋은 성능을 보였다고 합니다.
  
주의할 점으로 Word-Piece 같은 경우, 한국어에서는 오히려 성능이 저하된다고 합니다. 
  
### Multi-Head Attention
  
![MHA](https://postfiles.pstatic.net/MjAyMDAzMjRfMTQ2/MDAxNTg1MDI5MjY0MTU1.UHNwxE6qRO7tM9SrtoPhXoZz-thBq8hLzIgCkokhbe0g.kRmkmI0X3ZCu-AVN9CPpAH4JjPW1GSSFmvl9xRRsQKkg.PNG.sooftware/image.png?type=w773)

Multi-Head Attention (MHA)은 유명한 「Attention Is All You Need」 논문에서 기계번역 분야를 위해 제안되었습니다. 본 논문은 이러한 MHA를 Speech 분야로 확장해보았다고 합니다.   
  
![attn-distribution](https://postfiles.pstatic.net/MjAyMDAxMjVfMTIg/MDAxNTc5ODg4NDkxMDQz.miNLNdmdj0t3Ll12purypbOIE6PWRFijlxAF4ci5K28g.c-UT98v0QJumGmehmlwGkQ0bQxxV_jCKOCjOVH17ZcYg.PNG.sooftware/image.png?type=w773)

MHA는 기존의 어텐션을 multiple head로 확장한 구조입니다. 기존 어텐션이 1개의 어텐션 분포를 만들었다면, MHA의 각 head는 서로 다른 어텐션 분포를 만들어 내게 됩니다. 이러한 구조는 각 head가 encoder output의 서로 다른 곳을 Attend하게 해주는 효과가 있습니다. 
  
본 논문에서는 MHA를 적용 전, 후를 따로 비교하지는 않을 것 같습니다만, 제가 진행하는 음성 인식 프로젝트에서 비교해본 결과 MHA를 적용했던 모델이 압도적으로 좋은 성능을 보였습니다.
  
### Scheduled Sampling
  
본 논문에서 적용한 Scheduled Sampling이라는 개념에 대해 서술합니다.  
  
![teacher_forcing](https://postfiles.pstatic.net/MjAyMDAxMzFfMTcg/MDAxNTgwMzk4NTc3MzE2.rfhepBTuNa7UuGl7t4O2AAtVytd3Yd2d731im7KZ_jwg.kO58sY_DL9sBLx1LlZzq5A3hAplPA0gJA-6q4ZDr7Owg.PNG.sooftware/image.png?type=w773)

Seq2seq 구조에서는 학습을 빠르게 시키기 위해 [티쳐포싱](https://blog.naver.com/sooftware/221790750668)이라는 기법을 사용합니다. Seq2seq구조는 원래 이전 타임스텝의 추론 char / word를 다음 타임스텝의 입력으로 넣어야 합니다만, 학습 초기에는 대부분 잘못된 추론 결과가 나오게 됩니다. 이러한 부분을 개선해주기 위하여 이전 타임스텝 추론 결과가 아닌, Ground Truth를 넣어줌으로써 빠른 학습을 가능하게끔 해주는 기법입니다.  
  
#### Exposure Bias Problem
  
하지만 이러한 티쳐포싱 기법에는 단점이 있습니다. 학습 중에는 Ground Truth를 가지고 있지만, 실제 추론 과정에서는 Ground Truth가 없습니다. 그렇기 때문에 학습 과정과 추론 과정에서 차이(discrepancy)가 발생하게 됩니다.
  
![ss](https://postfiles.pstatic.net/MjAyMDAzMjZfMjc2/MDAxNTg1MjI1MjgwMDY2.4R2zFNqKvHotWWe7owD9cDRVO8dUpYZ7A2Sl7D6LbNEg.1DPR5iaZpI3yrEN9Mzzgr9v6KoA43Li4qDb9ZrrD5jog.PNG.sooftware/image.png?type=w773)

본 논문은 이러한 Exposure Bias Problem의 차이를 줄이기 위해서 스케쥴링을 해줍니다.  
학습 초기에는 티쳐포싱 100%로 진행이 되지만, 학습이 진행될수록 비율을 점점 낮춰서 최종적으로는 티쳐포싱 60%까지 줄여서 학습을 진행했다고 합니다.  
이렇게 스케쥴링 해줌으로써 실제 추론과 학습 단계에서의 차이를 줄였다고 합니다.  
  
### Label-Smoothing
  
또한 본 논문은 Label-Smoothing을 적용했다고 밝힙니다. Label-Smoothing은 데이터에 대한 Over-Confidence를 조금 덜어주는 역할을 합니다. 아마 Overfitting은 많이 봤겠지만, Over-Confidence는 생소한 분들이 많으실 겁니다. Over-Confidence란 데이터를 너무 믿는다는 겁니다. 아무래도 레이블링이라는 작업이 결국은 사람이 하는 것이다 보니, 어느 정도의 오류가 있습니다. 이러한 오류가 있는 데이터를 학습하다보면 아무래도 정확한 학습하기가 힘듭니다. 그래서 이러한 Over-Confidence를 줄여주기 위하여 Label-Smoothing이라는 개념이 있습니다.  
  
정확히 말하자면 Label-Smoothing loss입니다. loss를 계산할 때 적용이 됩니다. loss 계산시에, 원-핫 인코딩 되어 있는 레이블링에 의해 정답에 대해서만 loss가 계산되지만, 이때 정답 레이블은 1, 나머지 레이블은 0으로 되어 있는 것이 아니라, 정답 레이블은 confidence, 나머지 레이블은 uncertainty로 바꾸어 loss 계산을 합니다.  
  
confidence + uncertainty = 1.0이 되도록 설정을 합니다.   
  
아래는 이를 PyTorch로 이를 구현한 코드입니다.  
  
```python
class LabelSmoothingLoss(nn.Module):
    def __init__(self, vocab_size, ignore_index, smoothing=0.1, dim=-1):
        super(LabelSmoothingLoss, self).__init__()
        self.confidence = 1.0 - smoothing
        self.smoothing = smoothing
        self.vocab_size = vocab_size
        self.dim = dim
        self.ignore_index = ignore_index

    def forward(self, logit, target):
        with torch.no_grad():
            label_smoothed = torch.zeros_like(logit)
            label_smoothed.fill_(self.smoothing / (self.vocab_size - 1))
            label_smoothed.scatter_(1, target.data.unsqueeze(1), self.confidence)
            label_smoothed[target == self.ignore_index, :] = 0
        return torch.sum(-label_smoothed * logit)

>>> criterion = LabelSmoothingLoss(vocab_size, ignore_index, smoothing=0.1, dim=-1)  
```
  
### Second-Pass Rescoring
  
물론 Seq2seq의 Decoder가 어느 정도의 language model의 성격을 갖습니다만, 훈련 데이터의 텍스트만이 반영되기 때문에 language model로서의 한계점은 분명합니다. 그래서 다른 논문에서도 그러하듯이, 방대한 텍스트 데이터로 학습한 external language model과 결합을 합니다. 다만 이러한 결합은 훈련 과정이 아닌, 추론 과정에서만 결합을 합니다.   
  
![equation](https://postfiles.pstatic.net/MjAyMDAzMjZfMTgy/MDAxNTg1MjI2Mjc1Mjg2.29UeaSIPa-Q8Zj240gWng9JP6MDGWKheLHTyEDL8sUsg.iiwPV2VpCFjjsMYlOAck_qHJrrtH_WTntaxBovbhkH8g.PNG.sooftware/image.png?type=w773)
   
위의 식과 같이, Acoustic Model에서 나온 확률과 Language Model에서 나온 확률, 단어의 개수를 고려하여 Rescoring을 해줍니다.   
  

