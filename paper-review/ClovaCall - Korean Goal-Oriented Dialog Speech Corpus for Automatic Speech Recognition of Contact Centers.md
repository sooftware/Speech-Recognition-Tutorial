# ClovaCall: Korean Goal-Oriented Dialog Speech Corpus for Automatic Speech Recognition of Contact Centers  
  
![image](https://user-images.githubusercontent.com/42150335/80241423-8a367180-869e-11ea-8438-6b651ab65fb6.png)  
  
[논문링크](https://arxiv.org/abs/2004.09367)

2020-04-20에 클로바에서 공개한 따끈따끈한 논문입니다. 한국어 음성 데이터셋 공개와 더불어 베이스라인 코드와 AI Hub, 공개한 데이터셋에 대한 학습 결과까지 포함하고 있습니다. 현재 제가 진행하는 프로젝트와 완전히 동일한 주제이면서도 같은 데이터셋 (AI Hub) 을 이용한 학습까지 했다고하니 굉장히 기대하면서 읽었습니다. 게다가 논문을 낸 기관이 네이버 Clova여서 더 기대가 됐네요.  

## Abstract  

ASR은 여러 어플리케이션에서 필수적입니다만, License-free한 데이터는 찾기 힘들고, 유명한 Switchboard와 같은 데이터는 조금 구식입니다. 또한 가장 큰 문제점은 이러한 오픈 데이터는 대부분 영어로 이루어져 있다는 점입니다. 그래서 본 논문에서는 대용량의 한국어 음성 데이터셋을 공개한다고 밝힙니다. 11,000명의 화자에게서 얻은 60,000 쌍의 음성 데이터셋입니다. (1,000시간의 AI Hub 데이터셋이 2,000명의 화자로 이루어진 점과 비교하여 다양한 화자들로 구성된 데이터셋임을 알 수 있습니다.) 또한 해당 데이터셋을 검증하기 위한 베이스라인 코드를 같이 공개했습니다. 해당 데이터셋 및 코드는 [이곳](https://github.com/ClovaAI/ClovaCall)에 공개되어 있습니다.  

## 1. Introduction
  
음성인식 서비스는 다양한 분야에 적용가능한 핵심 기술입니다. 하지만 이러한 음성인식 서비스 개발을 위한 오픈 데이터들은 (Wall Street Journal (WSJ), TIMIT, Switchboard, CallHome, Librispeech) 공개된 지 오래된 구식의 데이터들이며, 모두 영어로 이루어져 있습니다. 물론 [AI Hub](http://www.aihub.or.kr/aidata/105)와 [zeroth](https://github.com/goodatlas/zeroth)와 같이 오픈된 한국어 음성 데이터셋도 있지만, 이 데이터셋은 일상대화와 같은 주제들을 다룬 데이터셋입니다. 이러한 데이터셋들을 사용하게 되면 특정 도메인을 타겟으로 한 태스크에서는 성능이 좋지 않습니다. 그래서 본 논문에서는 음식점을 도메인으로한 데이터셋을 공개했다고 밝힙니다. 대부분의 문장은 10초 이내의 짧은 발화로 구성되어 있으며, End-point detection이나 alignment가 이슈가 되지 않는다고 합니다.  

또한 본 논문에서는 이러한 데이터셋이 어느 정도의 성능을 낼 수 있는지를 보이기 위해 Deep Speech 2 (DS2)와 Listen, Attend and Spell (LAS)의 2개의 모델로 실험을 진행했다고 밝힙니다. 이때 **Pretraining-finetuning**, **from-scratch training**, **scratch training with data augmentation** 의 3가지 방법을 통해 비교했다고 합니다. 이에 더하여, 비교를 위해 이미 공개되어 있는 2개의 한국어 음성 데이터셋에 대해서도 같이 실험을 진행했습니다. 2개의 데이터셋은 QA Dataset (task-specific)과 AI Hub Dataset (dialog)를 의미합니다.   
  
## 2. Related Work
  
본 장에서는 기존의 여러 데이터셋들에 대해 소개하고 있습니다. WSJ, TIMIT, Switchboard, CallHome, LibriSpeech 등의 데이터셋을 소개하고 있으며, 해당 데이터셋들은 여러 ASR 모델들의 성능을 비교하는 벤치마크로 사용되고 있습니다. 하지만 다시 강조하듯이, 이러한 데이터셋들은 일상 대화라는 주제를 가진 데이터셋이며, 특정 도메인을 주제로한 데이터셋들은 거의 오픈되지 않습니다. 또한 이렇게 오픈된 일상 대화 데이터셋들은 특정 도메인을 타겟으로 한 모델의 Pretraining 데이터셋이 될 수 있지만, 일상 대화라는 도메인과 특정 도메인을 타겟으로하는 데이터셋은 서로 꽤나 상이하여 Pretraining 하더라도 좋은 결과를 내지 못하고 있다고 합니다.  
  
## 3. Korean Clova Call Speech Corpus  
  
### 3.1 Naver Clova AI for Contact Center  
  
ClovaCall 데이터셋 구축은 *NAVER Clova AI for Contact Center (AICC)* 프로젝트의 메인 주제였습니다. ClovaCall 데이터셋은 자연어 이해, 음성인식, 음성합성 등에 활용될 수 있으며, '음식점 예약'이라는 시나리오를 주제를 목적으로한 대용량 데이터셋임을 다시 한번 강조합니다.  
  
### 3.2 Data Cconstruction from Humans  
  
데이터 구축 과정에 대해 설명합니다. 데이터 구축은 다음 과정을 거쳤다고 합니다.  
  
1. making a Sentence Pool (어떤 문장들로 데이터셋을 구성할지)
  
2. call-based recording (전화상으로 들어온 소리를 녹음)   
  
3. refining the recorded speech data (데이터 정제)  
  
자세한 내용은 논문을 참고해주시면 되겠습니다. (데이터 구축 과정이 제 관심분야는 아닌지라 ㅎㅎ;;)  
  
### 3.3 Statistical Analysis  
  
![image](https://user-images.githubusercontent.com/42150335/80275445-3a968b00-871c-11ea-86f4-296e88ba1269.png)  
  
본 논문에서는 ClovaCall 데이터셋과 AI Hub 데이터셋을 분석한 결과를 보이고 있습니다. **단어, 문자, 음소, 발화 길이**를 분석하여 막대그래프로 표현했습니다.   
  
## 4. Speech Recognition Result  
  
이제 드디어 제가 관심있는 파트가 등장했습니다.  
  
### 4.1 Experimental Setup

#### Dataset & Training Schemes

앞서 언급했듯이, **Pretraining-finetuning**, **from-scratch training**, **scratch training with data augmentation**와 같은 3가지 방식으로의 학습방식 결과를 비교했습니다.   
  
데이터셋은 총 3가지를 사용했습니다.  
  
1. **AI Hub** for Pretraining  
  
2. **QA Call** for Finetuning  
  
3. **Clova Call** for Finetuning  
  
AI Hub 데이터셋은 Pretraining을 위해 사용되었고, QA Call 데이터셋은 Clova Call 데이터셋과의 성능 비교를 위해 사용되었습니다. 또한 Noise-Augmentation & Spec-Augmentation 2가지 기법을 각자 사용하여 결과를 비교했습니다.  
  
#### Data preprocessing  
  
먼저 AI Hub 데이터셋과 CLova Call 데이터셋의 샘플링 레이트가 서로 다릅니다. AI HUb는 16k의 샘플링 레이트를 가지는데 반해, Clova Call 데이터는 통화로부터 수집하다보니, 8k의 샘플링 레이트를 가집니다. 그래서 Clova Call 데이터를 Upsampling을 통해 8k => 16k의 샘플링 레이트를 가지도록 수정했습니다. 그리고 모든 모델은 **log-spectrogram**을 인풋으로 가지며, 20ms의 *window_size*와 10ms의 *stride*를 가집니다. (librosa 라이브러리를 사용했습니다.) 또한 모든 스펙트로그램은 *instance-wise standardazation* 방식으로 정규화를 진행했습니다.  
  
개인적으로 왜 Mel-Spectrogram이 아닌, 그냥 Spectrogram을 사용했는지에 대해 궁금증이 남아서, 공동 제 1저자 분 중 한분께 메일로 문의를 드렸습니다. 혹시 답변 해주신다면 답변 내용도 남기겠습니다.  

#### ASR Models  

ASR 모델로는 DeepSpeech2, Listen, Attend and Spell 2개의 아키텍처로 비교했습니다.  
  
 DeepSpeech2 아키텍처는 *2D-Convolutional layer with 32 channel*를 포함하고 있으며, 5개의 Bidirectional LSTM layer로 구성되어 있습니다. (각 방향당 800개의 unit을 사용했습니다.) 또한 트레이닝은 CTC loss를 이용하여 학습했다고 합니다.  
  
LAS 아키텍쳐는 DeepSpeech2와 같은 *Convolution layer*를 포함시키고, 각 방향당 512개의 unit을 가진 3층의 *Bidirectional LSTM layer*로 인코더를 구성하며, 512개의 unit을 가진 2층의 *Unidirectional LSTM layer*로 디코더를 구성했다고 합니다. 또한 어텐션 매커니즘으로는 「Attention based Models for Speech Recognition」- [Review Link](https://github.com/sooftware/Paper-Review/blob/master/Review/Attention-Based%20Models%20for%20Speech%20Recognition.md)에서 제안한 Location Aware 어텐션 매커니즘을 사용했습니다.  
  
모델 평가 지표로는 Character Error Rate (CER)을 사용했으며 다음과 같은 Metric을 따릅니다.  
  
![image](https://user-images.githubusercontent.com/42150335/80277350-42a8f780-8729-11ea-9609-665ea1944f7b.png)  
  
### 4.2 Comparison Results on Datasets  
  
![image](https://user-images.githubusercontent.com/42150335/80277359-60765c80-8729-11ea-928f-1f9941c7f36a.png)  
  
이제 결과를 비교하는 시간입니다. 결과만 놓고보자면, AI Hub 데이터셋으로 Pretraining 한 후, ClovaCall 데이터셋으로 Finetuning한 LAS 모델이 가장 좋은 성능 (Error Rate 7%) 을 냈습니다.  
  
또한 주목할만한 점은, AI Hub 데이터셋만으로 학습시킨 모델의 경우, Specific-domain의 데이터로 테스트 했을 때, 성능이 매우 저조했다는 점입니다. 가장 좋은 성능을 낸 LAS 모델로 비교를 하자면 Error Rate 69.2%를 기록했네요.  
  
또한 본 실험에서는 Data Augmentation이 의미있는 결과를 내지 못했습니다. 본 논문에서는 이를 이미 노이즈가 낀 상황에서 데이터를 만들었다는 점과, 파라미터 수가 작은 LAS 모델에서 Spec Augmentation을 적용했다는 점이 의미있는 효과를 내지 못한 점으로 꼽았습니다.  

## 5. Concluding Remarks  
  
결국 이 논문의 핵심을 특정 도메인을 대상으로 한 ASR 시스템을 구축하기 위해서는 해당 도메인의 데이터셋이 필요한데, 저희가 그런 '음식점 예약' 도메인에서의 큰 데이터셋을 공개했고, 이 데이터셋을 사용했더니 결과가 좋았습니다 ! 를 강조한 논문이였습니다. 

  

  
  


  
