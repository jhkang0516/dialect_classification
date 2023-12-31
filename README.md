# 지역별 방언 구분 프로그램 설계    

## 데이터 소개  
이 프로젝트에서는 AI Hub의 '한국어 방언 발화 음성'과 '한국인 대화 음성' 데이터 세트를 사용했습니다. 이 데이터 세트들은 표준어 또는 방언 사용자의 원활한 의사소통과 학습을 도울 수 있고 다양한 환경을 인식할 수 있는 AI 데이터 세트 구축을 위해 제작되었습니다. 각 데이터 세트는 지역별로 분할되어 있으며, 각 지역 방언 별로 약 3000시간 분량의 음성 데이터가 포함되어 있습니다.    
  
![image03](https://github.com/jhkang0516/dialect_classification/assets/115124611/507d6660-43db-4142-b428-da721b8584ff)  

데이터 전처리 과정에서는 JSON 메타데이터를 참고하여 발화를 문장으로 분할하고, 해당 문장 안에 방언 단어가 들어있는 경우만을 추출 대상으로 선별하였습니다. 이때 2초 미만의 문장은 감탄사, 짧은 대답 등 방언적 특성이 적게 드러나는 문장일 것이라 판단하여 추출 대상에서 제외했습니다. 이러한 과정을 거쳐 전체 데이터 중 지역별로 10,000개의 문장을 추출하며 총 6개 지역을 수집하여 60,000건의 샘플 데이터를 확보하였습니다.  
  
![image04](https://github.com/jhkang0516/dialect_classification/assets/115124611/8b853eb1-937b-48b0-8c76-f7ce4fcfe97e)  
<**노이즈 처리 전**>                           
 ![image05](https://github.com/jhkang0516/dialect_classification/assets/115124611/8b5282c7-6d97-4f66-b18e-c12aec199f21)  
<**노이즈 처리 후**>  
획득한 원시 음성 데이터 샘플은 python noisereduce 라이브러리를 활용한 노이즈 처리와 Amplitude Normalize 정규화를 거친 후 Librosa 라이브러리를 활용하여 MFCC 추출을 진행하였습니다. 이때, MFCC 0번 계수는 가장 긴 파장을 가진 주파수로서 그 절대적인 크기가 다른 계수들 대비 압도적이기에, 다른 계수들이 영향을 받는 현상을 줄이고자 제외하였습니다.  


    
## 토론 및 결론  
이 프로젝트에서는 AI Hub에서 제공하는 한국어 대화 음성 데이터 세트를 모델 학습과 테스트에 사용했습니다. 우리는 표준어, 강원도, 충청도, 경상도, 전라도, 제주도로 구분된 6개 지역에서 각각 10,000개의 방언 음성 데이터를 무작위로 추출하고, 이를 7:3 비율로 학습 데이터와 테스트 데이터로 사용했습니다. 우리는 RF, SVM, GRU, ResNeXt50 모델을 사용하여 학습을 진행하였고, 프로젝트에서 사용된 모델의 절반에서 선행 연구의 목표였던 약 60%의 정확도를 초과하는 결과를 얻을 수 있었습니다.  
  
90%의 정확도로 가장 좋은 성능을 보인 ResNeXt 모델의 성능 평가 지표는 다음과 같이 비교할 수 있습니다.   
  
![그림8](https://github.com/jhkang0516/dialect_classification/assets/115124611/4f2702ac-794f-43b8-9be4-41b25b566eda) ![그림9](https://github.com/jhkang0516/dialect_classification/assets/115124611/64273b96-7816-4344-b491-1e7098955c64)


그림을 통해 전라도와 제주도는 일반적으로 평가 지표 값이 낮다는 것을 확인할 수 있습니다.  

## 결론  
MFCC는 사람들이 인식하는 형태로 음성 데이터의 특징을 추출하고 패턴을 인식하는 데 매우 적합합니다. 이는 MFCC가 사람의 청각 시스템을 모방하여 모델링하기 때문입니다. 또한, MFCC는 원시 음향 데이터보다 훨씬 작은 크기의 특징 벡터를 생성함으로써 데이터의 차원을 줄이고 메모리 사용량과 학습 시간을 절약할 수 있습니다.  


## 결과 해석 및 새로운 발견  
  
### SVM  
정확도, 정밀도, 재현율, F1-Score의 값은 대략 50% 또는 그 이하로, 비선형 분류 SVM을 사용하여도 적절한 분류가 이루어지지 않았음을 보여줍니다.  

### RF  
Random Forest의 성능은 GridSearchCV에서 찾은 최적의 매개변수로 학습한 후 예측되었습니다. 그런 다음, accuracy_score로 성능을 평가하고 검증하였습니다.  

### CNN  
이 연습에서는 이를 기반으로 MFCC 변환 후 ResNeXt50 모델에서 음성 데이터를 학습시켰고, 이는 90%의 매우 높은 확률로 방언을 구별하였습니다.  

### RNN  
우리는 LSTM, GRU, Bi-LSTM 총 세 가지 모델을 테스트하였고, 하이퍼파라미터 조정을 통해 정밀도, 재현율, F1-Score를 약 70%까지 향상시킬 수 있었습니다.  

  
