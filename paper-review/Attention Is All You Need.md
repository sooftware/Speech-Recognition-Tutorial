# 「Attention Is All You Need」 Review
  
![image](https://user-images.githubusercontent.com/42150335/86510278-4e035800-be29-11ea-98f8-ca9503f4428f.png)  
  
https://arxiv.org/abs/1706.03762  
  
## Abstract   
  
**Transformer**를 제안한 것으로 유명한 논문입니다. 기존의 모델들은 recurrent or convolutional Neural Network 기반의 인코더-디코더 구조 (Seq2Seq) 였습니다. 그 중 최고의 성적을 내는 모델은 어텐션 기반의 Seq2seq 구조였고, 본 논문에서는 어텐션 매커니즘만을 이용해 만든 **Transformer** (이하 트랜스포머) 를 제안합니다. 본 논문에서 제안한 트랜스포머는 WMT 2014 English-to-German 번역 태스크에서 28.4 BLEU, WMT 2014 English-to-French 번역 태스크에서는 41.0의 BLEU로 State-Of-The-Art를 달성했음을 밝혔습니다. 또한 이러한 결고는 8개의 GPU로 3.5일만에 달성했음을 밝히며, 학습 속도면에서도 매우 우수함을 밝혔습니다.   

## 1. Introduction  

RNN, LSTM, GRU 등의 네트워크는 언어모델, 기계번역 시퀀스 태스크에서 State-Of-The-Art의 성능을 보여주지만, RNN 특유의 시퀀셜한 구조 때문에 (이전 타임스텝의 히든스테이트가 필요한 구조) GPU를 사용함에도 불구하고 병렬 처리가 제한된다는 특징이 있습니다. 이러한 단점을 지적하며 본 논문이 제안한 트랜스포머는 어텐션 매커니즘만을 사용하며 RNN을 배제한 구조를 사용했다고 밝힙니다. 이러한 구조는 매우 빠른 학습을 가능하게 하며, 성능 면에서도 State-Of-The-Art를 달성할 정도로 우수한 장점들을 가지고 있습니다.  

## 2. Background  

Sequential Computation을 줄이기 위해 Extended Neutral GPU, ByteNoet, ConvS2S 등 몇몇 구조가 제안되었으나, 본 트랜스포머 모델은 Multi-Head Attention, Self Attention 등을 사용하며 RNN을 완전히 배제한 모델을 만들었다고 밝힙니다.  

## 3. Model Architecture
  
![fig1](https://paul-hyun.github.io/assets/2019-12-19/transformer-model-architecture.png)  

가장 중요한 모델 구조입니다.  
  
