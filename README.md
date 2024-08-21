# **WordPiece Tokenizer**

### **[ Google’s Neural Machine Translation System: Bridging the Gap between Human and Machine Translation ]**

Google이 자신들의 챗봇인 BERT에 사용하기 위해 개발하였다
해당 논문에서 Wordpiece Tokenizer에 대한 설명 및 작동된 결과에 대해 기술하였다.

[ 논문 링크 ](https://arxiv.org/pdf/1609.08144)

---------------------------

# **논문요약**

### **초론**
- 신경망 기계 번역(NMT)에 대한 짧은 설명과 단점과 한계점을 설명한다.
- 구글이 개발한 GMNT에 대해 설명할 예정 및 테스트 결과

어차피 본문에서 설명할 내용이니 생략

--------------------------

# **1. Introduction**
### **신경망 기계 번역 (NMT - Neural Machine Translation)**
**NMT의 강점**
- end-to-end learning 학습방식, 순환 신경망(RNN)으로 구성되어 하나는 input 하나는 output 텍스트를 생성
- 기존의 구문기반번역 시스템의 단점을 극복할 수 있는 잠재력을 지닌다.
<br>

- 근데 실제로는 NMT가 더 정확도가 낮았다 ( 특히, 대규모 데이터에 대한 학습의 경우 )

※ end-to-end learning : end-to-end learning은 입력에서 출력까지 파이프라인 네트워크 없이 신경망으로 한 번에 처리한다는 의미이다. 파이프라인 네트워크란 전체 네트워크를 이루는 부분적인 네트워크이다.  인간이 직접 feature를 추출하지 않아도 되지만 연산이 복잡할 수록 오래걸린다

**NMT의 단점**
- 느린 학습 및 추론 속도 : 문장 전체를 학습하기 때문에, 추론 시간도 느려짐, 그래서 대규모 데이터일 경우, 속도가 매우 느림
- 희귀 단어 번역 정확성이 매우 떨어짐, "copy model"이나 "attention mechanism"을 사용해서 처리할 수 있을 수 있으나, 좋은 전략이 아닌거 같아 보임
- 간혹 문장 전체를 번역 못하고 그냥 출력해버림
<br>
<br>
<br>

### **GNMT Google의 프로덕션 NMT시스템**
- LSTM(Long short-Term Memory) RNN
- 8개의 레이어를 사용, Residual connection 사용
- 주의메커니즘은 디코더의 하위 레이어를 인코더의 상위 레이어에 연결하여 병렬 처리를 개선
- 저정밀도 산술을 이용하여 추론 계산 속도를 향상
- 희귀 단어 처리를 위해 **WordPiece** 사용

**평가**
- 언어별 조정 없이 다양한 데이터 세트에서 더 좋은 결과를 나타냈다
- 대충 자랑하는 내용

------------------------------

# **2. Related Work ( 연구 )**
### **통계적 기계 번역 ( SMT, Statistical Machine Translation)**
- 지난 수십 년간 번역 모델의 대표적인 모델로 구문 기반 시스템(PBMT)
- 단점을 보완하기 위해 여러 접근 방식을 통한 시도가 있었지만, 제한 된 겅송을 거두었다.
<br>
- 최근에는 여러 기술들이 제안되어 회귀 단어를 처리하기 위한 메커니즘, 다양한 종류의 주의 메커니즘 등, 많은 기술들의 시도로 정확도가 향상되었지만,
대규모의 번역 시스템에서는 좋은 성과를 보지 못했다.

-------------------------------

# **3. Model Architecture**

- sequence-to-sequence 프레임워크를 따른다. [ 인코더, 디코더, 주의네트워크 ]
- 입력 문장의 각 단어 토큰이 인토더를 통과 -> 벡터로 변환하여 리스트에 저장 -> 디코더가 해당 리스트를 받으면 EOS(end-of-sentence symbol)가 생성될 때 까지 1개 백터 당 하나의 symbol 생성
<br>
<br>

#### **수식 설명**
- 벡터 - 굵은 소문자 (예: **v**, **o**)
- 행렬 - 굵은 대문자 (예: **U**, **W**)
- 집합 - 필기체 대문자 (예: *V*, *T*)
- 시퀀스 - 대문자 (X, Y)
- 심볼 - 소문자 (x, y)
- M = (X = x1, x2, x3 ...) | N = ( Y = y1, y2, y3 ... )
<br>

<br>

### 인코더
<img src='https://drive.google.com/uc?export=download&id=14LGd9n5TPCDI_TG038kRXyHnYZevr07H' width="" height ="" /><br>

<br>

#### > 체인 법칙을 사용, 시퀀스 P의 조건부 확률(Y|X)
* y는 문장 앞에 추가되는 "문장 시작" 심볼

<img src='https://drive.google.com/uc?export=download&id=18inJ-lPuegCr1n1KJqDHiWUVOgHmlkMZ' width="" height ="" /><br>

<br>

#### 디코딩 최종
* GNMT의 디코더는 RNN, Softmax 레이어의 조합으로 구성

<img src='https://drive.google.com/uc?export=download&id=1TNvJm2or8iKwmWA1SkwxT-Nfrs7mAzqt' width="" height ="" /><br>

------------------------

## **Model Architrcture , Layer 설명 [ 그림 1 ]**

* 왼쪽 [ 인코더 ] , 가운데 [ Attention ] , 오른쪽 [ 디코더 ]

<img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbzLvQF%2FbtsDLIl3FTf%2Fd0eNhikrYv7Wu28bRiNk8K%2Fimg.png'>


* 인코더 : 8개 LSTM layer ( 양방향 1개, 단방향 7개 )
* 디코더 : 8개 layer
* 이 설정으로 하나의 모델 복제본이 8방향으로 분할되어, 서로 다른 GPU에 배치됨
+ 맨 아래 레이어가 병렬로 계산

<br>

### 중간 기능 정리
- > 한 문장을 넣었을때 각 symbol별로 1개의 x가 생성된다. 현재 x3 -> x2 -> x1 순서가 역방향(실제로 문장의 읽는 반대 순서)이다. 이렇게 역방향으로 시작하는 이유는 역전파 과정에서 그래디언트 흐름을 개선하고 장기 의존성을 학습하는 데 도움 을 주기 때문이다. 이때 GPU는 각 레이어마다 1개씩 부여받는다. GPU1에는 encoder layer1과 decoder layer1이 있는 식이다. 첫 실행시 실선으로 묶인 GPU1과 GPU2에 있는 작업이 동시에 처리(병렬)되며 양방향 전개가 이루어진다.

<br>

#### 현재 시점에서 Attention 계산공식
* 주의 모듈은 디코더 RNN의 이전 출력과 소스 문장의 벡터 목록을 사용하여 주의 컨텍스트를 계산한다. 이 과정은 피드포워드 네트워크를 사용하여 수행함
* 해당 수식을 통해 Attention 점수와 확률 계산

<img src='https://drive.google.com/uc?export=download&id=10OhJslwsrwA2vM7du89BsBzDfFe1Vo_L' width="" height ="" /><br>

-------------------------------

## **3.1 Residual Connections (잔차 연결) [그림2]**

- deep stack 에서 LSTM은 학습이 매우 느리고 그라데이션(기울기)이 폭발하고 사라지는 문제가 있음 > 데이터 유실
_ Residual Connections를 통해 해당 문제를 해결

```
- 한 레이어의 출력에 입력이 직접 더해져 활성화 함수를 통과한 이 출력은 다음 레이어의 입력으로 사용.

출력 = 활성화 함수(입력+전 레이어의 출력)
```

<br>

### **[ 일반 LSTM 레이어 ]**

<center><img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdACC0H%2FbtsDLIfla9J%2FYEKs6eJ6xiOFwK6qitVob0%2Fimg.png'>
<img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F6EXru%2FbtsDHrfoZVk%2FYBjO5DgKFocIXhfArKBLU1%2Fimg.png'></center>

- 여기서 x00의 LSTM1의 결과가 x01의 LSTM1의 결과에 곱해지고 그렇게 나온 결과가 x02의 LSTM1의 결과에 곱해지는데 이때 해당 결과들이 음수일 경우 결국은 0으로 수렴하면서 기울시 소실 문제가 발생한다. 반대로 곱해지는 값이 양수일 경우 기울기 폭발이 일어난다.
- 이렇게 될 경우 x01와 같은 새로이 입력되는 값들을 반영하기 어렵다.

<br>

### **[ 잔차 연결 LSTM 레이어 ]**

<center><img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fciq6m2%2FbtsDJTWdCSg%2Ftqs3G5jgIfm1dMqPqzkBF0%2Fimg.png'>
<img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbPBw9a%2FbtsDJTPtsco%2F65RlEEQYlya78KR236aakK%2Fimg.png'></center>

- 다음 레이어에 출력을 다음 레이어에 전달
-  x01, x02... 해당 값들이 반영되어, 기울기 소실이나 폭발 문제를 해결

--------------------------------

## **3.2 첫 번째 레이어 [ 양방향 인코더 ][그림3]**

- 번역 작업에서는 특정 단어를 번역하기 위한 정보가 문장 어느곳에서나 나올 수 있기 때문에, 첫 번째 레이어에서 양방향으로 인코딩을 하여 정확도를 올린다

<br>

<center><img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FIlock%2FbtsDJgxl2xP%2F3fdPvMB5HIv6O5lDUWm7rK%2Fimg.png' width="" height ="" /></center><br>

- 첫 실행 시, GPU1,2가 동시에 작업되며 (병렬) 양방향 전개가 이루어 진다.

------------------------------

## **3.3 Model Parallelism 병렬처리**

* 모델의 복잡성으로 인해, 학습 속도를 높이기 위해, 모델 병렬처리 및 데이터 병렬 처리를 모두 활용
* 모델 병렬처리 : 인코더와 디코더를 여러 GPU에 분할하고 실행
* 데이터 병렬처리 : 여러 모델 복제본을 동시에 학습

---------------------------

# **4. Segmentation Approaches**

- NMT 모델은 번역이 기본적으로 열린 어휘 문제임에도 불구하고, 고정된 어휘를 사용하는 경우가 많다. 특히 이름, 숫자, 날짜 등과 같은 희귀 단어에 대한 처리가 중요한데, 이를 위한 두 가지 주요 접근법이 있다.

- 첫 번째는 희귀 단어를 소스에서 타겟으로 그대로 복사하는 방법이다. 주로 이름이나 숫자와 같은 희귀 단어는 올바른 번역이 단순히 복사하는 것이므로, Attention 모델, 외부 정렬 모델, 또는 특수 목적의 포인팅 네트워크를 사용하여 이를 구현할 수 있다.

- 두 번째는 부분 단어 단위를 사용하는 방법으로, 여기서 워드피스 모델(WPM)이 주목받고 있습니다. WPM은 데이터 주도적이며 어떤 문자열의 연속에 대해 결정적인 세그멘테이션을 생성할 수 있는 모델입니다. 이를 활용해 훈련 데이터의 토큰을 부분 단어로 분해하여 모델을 학습시킨다.

-----------------------

# **4.1 Wordpiece Model (WPM)**

- Google의 음성 인식 시스템의 일본어/한국어 분할 문제를 해결하기 위해 개발된 WPM 구현을 채택
- 희귀 단어를 처리하기 위해, 단어를 하위 단어로 분할
- BPE와 비슷하게 가장 자주 나타나는 쌍으로 문자들을 분리하나, 빈도수가 아닌
언어 모델 가능성 likelihood (maximize the langauage-model likelihood) 을 최대화 하는 데이터 주도적인 접근법을 사용

```
예 )

# 모든 단어를 문자 단위로 분할 후, 가장 빈번하게 나타나는 문자 쌍을 병합하여 wordpiece로 만듬 > 이 과정을 설정한 수 만큼 반복함
Jet makers feud over seat width with big orders at stake
->> WPM으로 분해
wordpiece : _J et _makers _fe ud _over _seat _width _with _big _orders _at _stake"

Jet라는 단어는 '_J'와 'et'로 나누어지며, "_"는 단어의 시작을 알림

디코딩 과정에서 NMT는 wordpiece를 기반으로 번역을 수행


```
- 훈련 말뭉치와 정의된 토큰 개수 D가 주어진 상황에서, 최적화 문제는 선택된 워드피스 모델을 기반으로 세그멘테이션된 결과 말뭉치가 워드피스 수가 최소가 되는 D 워드피스를 선택
- GNMT는 단어의 시작 부분에만 특수 기호를 사용하고, 기본 문자의 수를 데이터에 따라 관리 가능한 수준으로 제한
- 은 문자들은 매우 희귀한 문자로 간주되어 워드피스 어휘를 오염시키지 않도록 특수한 알 수 없는 문자로 매핑 >> 드피스 어휘를 만들때 의미가 있는 애들만을 남기는 것을 의미


<br>

- wordpiece를 사용했을 때, 전반적으로 높은 BLEU 점수를 기록함

```
from transformers import BertTokenizer

# 사전 학습된 BERT 모델의 토크나이저 불러오기
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')

# 예제 문장
text = "Jet makers feud over seat width with big orders at stake"

# 텍스트 토큰화
tokens = tokenizer.tokenize(text)

# 결과 출력
print("WordPiece Token:", tokens)
```

---------------------------------

## **4.2 혼합 단어/ 문자모델**

- OOV 단어를 문자 시퀀스로 변환 후, 일반 문자와 구별하기 위해 특수문자 접두사를 붙힘
- 접두사 : <B> - 단어의 시작 , <M> - 단어의 중간, <E> - 단어의 긑
```
예) Miki (사전에 없은 단어) > <B>M <M>i <M>k <E>i
후에 디코딩 시, 원래 단어로 복원
```

-------------------

# **5. 학습 기준**

### **likelihood 최대화 수식**
- train 데이터를 넣고 모델을 훈련할때 아래와 같은 수식을 사용하여 결과값이 최대값이 되는 것을 목표로 한다.

<br>

<center><img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdCa3QW%2FbtsDGIOWLx2%2FbD8PnciKPmiWxWFA8ikHgk%2Fimg.png'></center>

하지만 이러한 수식의 사용의 주요 문제점은 번역에서 BLEU 점수로 측정되는 작업 보상 함수를 정확하게 반영하지 않는다. <br>
디코딩 중에 발생하는 오류에 대해 학습하지 못해 훈련 및 테스트 절차 간에 상당한 불일치가 있을 수 있는데 위의 수식을 그것을 반영하지 않는다는 것을 의미한다 <br>

때문에 GNMT 모델에서는 위의 수식(O ML)이 최대값이 될때까지 train 데이터로 훈련을 한 뒤 development set에서 BLEU가 개선되지 않을 때까지 아래의 수식인 O Mixed를 기준으로 모델을 개선하는 방식을 활용할 수 있는데 이것은 선택사항이다.
<center><img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FG7ycf%2FbtsDIx0tkrQ%2F7GDTzywjGGieii2fLoCPe1%2Fimg.png'>
<img src='https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcKeGd8%2FbtsDNIGaDTu%2Fl5m9a5jET4W9O4AxJWjSc0%2Fimg.png'></center>
* r(Y, Y) : 문장당 점수


이렇게 likelihood를 최대화하는 과정동안 학습이 되고 자연스럽게 모델의 파라미터 값이 바뀌며, 모델의 WordPiece도 바뀐다.

<br>


* BLEU 점수는 말뭉치 측정용으로 설계되어 단일 문장에 사용할 경우, 적합하지 않다.
* 자기들이 만든 GLEU 점수를 사용

<center><img src='https://drive.google.com/uc?export=download&id=1jq1pGxOwwnH1zJvDGkEwg3gE2DB6m8KA' width="" height ="" /><br></center>

- GLEU 점수는 출력 및 타겟 시퀀스의 1, 2, 3, 4-그램 서브시퀀스를 모두 기록합니다. 이를 통해 recall(타겟 시퀀스의 총 n-그램 수 대비 일치하는 n-그램 수의 비율)과 precision(생성된 출력 시퀀스의 총 n-그램 수 대비 일치하는 n-그램 수의 비율)을 계산합니다. GLEU 점수는 recall과 precision 중 최소값으로 정의되며, 항상 0에서 1 사이의 값을 가지며, 출력과 타겟을 교환할 때 대칭적

- 기대 보상 목표에서 r(Y, Y*)의 평균을 빼고, 훈련을 안정화하기 위해 최대 가능도와 강화 학습 목표의 선형 결합을 최적화
- 먼저 likelihood룰 사용 하여 모델을 학습 후, BLUE 점수가 더 이상 향상되지 않을 때 까지 이 모델을 구체화 한다.

----------------------

# **6. 양자화 가능한 모델 및 양자화 추론**
- NMT 모델을 실시간 상호작용 번역 서비스에 배포할 때 주요 도전 과제는 추론 시 계산 비용이 매우 높다는 것이다. 이로 인해 낮은 지연 시간의 번역이 어렵고 대량 배포가 매우 비용이 많이 든다

- 저정밀도 산술을 사용한 양자화된 추론은 모델의 추론 비용을 크게 줄일 수 있다. 이는 동일한 계산 장치에서 효율성을 크게 개선할 수 있는 방법이다.<br>
예 ) CNN 모델은 양자화된 상태에서 최소한의 정확도 손실로 4-6배의 속도 향상을 보여줌

- 양자화의 도전 과제 : 깊은 LSTM 네트워크의 경우, 양자화 오차가 여러 스텝을 거치며 증폭될 수 있다. 이를 해결하기 위해, LSTM 스택에서 양자화 오차를 줄이기 위한 추가 제약을 도입

### **수식**

- **양자화된 LSTM 스택의 계산**

<center><img src='https://drive.google.com/uc?export=download&id=1zx-3ydXLdWx1QEVHkYlIm9virEPjbMRt' width="" height ="" /><br></center>
<br>


- **위 수식에서 LSTM을 확장하여 내부 게이트 로직 포함**

<center><img src='https://drive.google.com/uc?export=download&id=1gqVr8sAKPDi_L0GpkWYpyNto5Ev84E8V' width="" height ="" /><br></center>

<br>

- **추론 시 모든 부동 소수점 연산을 8비트 또는 16비트 정수 연산으로 대체 가중치 행렬 W는 8비트 정수 행렬 WQ와 부동 소수점 벡터 s로 표현**

<center><img src='https://drive.google.com/uc?export=download&id=1Ta1fHd0OGnu1xtXhQAJEjE-DBLmU66j8' width="" height ="" /><br></center>

<br>

- **16비트 정수: 누적값(cit 및 xit)은 [-δ, δ] 범위를 가지는 16비트 정수로 표현, 모든 행렬 곱셈은 8비트 정수 곱셈으로 수행되고, 모든 활성화 함수 및 요소별 연산은 16비트 정수 연산으로 수행**

<center><img src='https://drive.google.com/uc?export=download&id=19fr1eML_hvYtsaWta2hWu55ZqWbFDFxD' width="" height ="" /><br></center>

<br>


- **추론 시 정수 연산: 추론 시 소프트맥스 함수와 주의 모델을 제외한 모든 연산은 양자된다. 훈련 중에는 부동 소수점 숫자를 사용하고, 추론 시에는 양자화된 정수 연산을 사용하여 효율성을 높인다.**

<br>

#### **결과 비교**

- 양자화된 추론은 추론 시간의 효율성을 크게 높이면서도 번역 품질에 거의 영향을 미치지 않는다. CPU, GPU, TPU에서 양자화된 모델을 사용한 추론 성능을 비교한 결과, TPU를 사용한 양자화된 추론이 가장 효율적

<center><img src='https://drive.google.com/uc?export=download&id=1kY7KtEJS3FcdhyToXZjLX4BXpW0LuIZF' width="" height ="" /><br></center>

최대 가능성 훈련 중 WMT'14 영어에서 프랑스어로의 일반(양자화되지 않은) 훈련과 양자화 인식 훈련에 대한 로그 난해도 대 단계. 훈련 손실은 비슷하지만 양자화 인식 손실이 약간 더 나은 것을 알 수 있다.

<br>

- 마지막으로, TPU의 디코딩이 CPU의 디코딩보다 3.4배 빠르다는 것을 보여준다.양자화 된 산술은 CPU 또는 GPU보다 TPU에서 훨씬 빠름

<center><img src='https://drive.google.com/uc?export=download&id=12cIvUO8itVsPLBXBxzVcq30jZnLXueJr' width="" height ="" /><br></center>

-----------------------

# **7. Decoder**

#### **beam search**
- bean search는 학습된 모델을 사용하여 주어진 문장X에 대해 최대 점수 s(Y, X)를 갖는 출력 시퀀스 Y를 찾는 과정
- 길이 정규화가 없으면 빔 검색은 각 단계에서 음의 로그 확률이 추가되어 문장이 길수록 낮은 점수가 나오기 때문에, 짧은 결과를 선호

#### **길이 정규화(length normalizaion)**
- 처음에는 길이로 나누는 방식으로 정규화했으나, 개선된 방법으로 길이 α로 나누는 방식을 사용. α는 개발 세트에서 최적화되며, 일반적으로 0.6에서 0.7 사이가 최적의 값임

- 최대 점수 : s(Y, X)의 수식
<center><img src='https://drive.google.com/uc?export=download&id=1N9XUFilve4mRul7WCZVnOoViXc7cz_fz' width="" height ="" /><br></center>

<br>

### **효율성**
- 가설 수 제한: 각 단계에서 빔 크기 이하의 로컬 점수를 갖는 토큰만 고려하여 효율성을 높입니다.
- 정규화된 최고 점수 기반 가지치기: 최고 정규화 점수가 발견되면 그보다 빔 크기 이상 낮은 모든 가설을 가지치기합니다. 이는 검색 속도를 30%에서 40%까지 향상시킵니다.
- 디코딩 중 처리량을 늘리기 위해, 비슷한 길의 문장을 배치에 넣고 병렬 계산을 사용

-----------------------

## **평가 ( 샘플 데이터 셋을 활용한 여러 모델들을 사용한 결과 평가 ) 생략**

----------------------

# **결론**

Google의 GMNT 모델을 통해 기존 NMT 모델을 통한 번역 시스템의 문제점을 해결했다

주요 연구결과
- wordpiece 모델이 희귀 단어를 효과적으로 처리할 수 있다.
- 모델과 데이터의 병렬 처리 조합을 사용하여 sequence-to-sequence NMT모델을 효율적으로 학습할 수 있다.
- 모델 양자화가 번역 속도를 높임
- 길이 정규화 등 여러 추가 정보들이 NMT의 효율을 올려준다.

### *우리 GMNT는 번역 오류를 60%나 감소시켰따*

--------------------

# **논문에 기재된 용어들에 대한 보충 설명**

1. NMT( Neural Machine Translation )란?<Br>
신경망을 이용한 기계 번역, 머신러닝 기술이 적용된 엔진을 통해서 전체적인 문맥을 파악하고, 문장을 통째로 번역한다.

<br>

2. END-TO-END LEARNING<br>
end-to-end learning은 입력에서 출력까지 파이프라인 네트워크 없이 신경망으로 한 번에 처리한다는 의미이다. 파이프라인 네트워크란 전체 네트워크를 이루는 부분적인 네트워크이다.  인간이 직접 feature를 추출하지 않아도 되지만 연산이 복잡할 수록 오래걸린다

<br>

3. phrase-based translation systems (구문 기반 번역 시스템)<br>
문장을 번역할때 단어 대신 어구(phrase) 단위로 수행하는 방식으로 구성되어 있다.<br>
어구 추출 (Phrase Extraction): 소스 언어의 문장에서 번역에 유용한 어구를 추출. 이때 어구는 여러 단어로 이루어진 구를 의미하며, 통계적인 기준에 따라 선택.
어구 정렬 (Phrase Alignment): 소스 언어의 어구와 대상 언어의 어구 사이의 대응 관계를 찾음. 어떤 어구가 어떤 언어로 번역되는지를 결정하는 작업이 이에 해당.
어구 평가 (Phrase Scoring): 각 어구 쌍에 대한 번역의 품질을 측정하는 스코어를 부여. 이 스코어는 어구의 등장 빈도, 번역 모델의 신뢰도 등을 고려하여 계산.
해독 (Decoding): 최종적인 번역을 생성. 여러 어구의 조합을 고려하여 번역 결과물을 도출.

<br>

4. LSTM<br>
LSTM은 "Long Short-Term Memory"의 약자로, 순환 신경망(Recurrent Neural Network, RNN)의 한 종류. LSTM은 RNN의 한계를 극복하고 장기 의존성(Long-Term Dependencies)을 효과적으로 학습할 수 있는 구조를 제공하는 신경망 아키텍처. 보통 번역의 경우 RNN을 기반으로 한 LSTM, GRU 등의 모델로 구성되어 있음
- Cell State (기억 상태): LSTM은 고유한 기억 상태를 유지. 이 기억 상태는 네트워크가 이전 시간 단계에서 어떤 정보를 기억할 것인지 결정하는 중요한 역할.
- Hidden State (은닉 상태): LSTM은 은닉 상태를 계산하여 출력. 이는 현재 시간 단계에서의 입력과 이전 시간 단계에서의 은닉 상태를 기반으로 함.
- 게이트 (Gate) 구조: LSTM은 여러 게이트를 사용하여 정보의 흐름을 제어. 세 가지 주요 게이트는 아래와 같음.
    - Forget Gate (잊어버리기 게이트): 어떤 정보를 기억 상태로부터 삭제할 지 결정.
    - Input Gate (입력 게이트): 현재 시간 단계에서의 입력 중 어떤 정보를 기억 상태에 추가할 지 결정.
    - Output Gate (출력 게이트): 기억 상태에서 어떤 정보를 은닉 상태로 출력할 지 결정.<br>

LSTM은 이러한 게이트 구조를 통해 장기 의존성을 학습하고, 그라디언트 소실 또는 폭발과 같은 문제를 완화. 이를 통해 LSTM은 긴 시퀀스에 대한 효과적인 학습이가능해지며, 자연어 처리 분야에서 문장의 길이나 문맥을 고려한 모델링에 활용

<br>

5. 인코더와 디코더

디코더(Decoder)와 인코더(Encoder)는 기계 번역 및 자연어 처리와 같은 작업에서 사용되는 신경망의 구성 요소로 주로 순환 신경망 (RNN), 변형 트랜스포머 (Transformer), 또는 LSTM과 같은 모델의 부분으로 활용됨

- 인코더 (Encoder):
    - 목적: 입력 시퀀스를 모델이 이해할 수 있는 형태로 변환하고, 그 정보를 컴팩트한 표현으로 인코딩하는 역할을 합니다.
    - 동작: 주어진 입력 시퀀스를 단계별로 처리하면서 각 단계의 은닉 상태를 생성합니다. 이러한 은닉 상태는 입력 시퀀스의 정보를 요약한 표현으로 볼 수 있습니다.
- 디코더 (Decoder):
    - 목적: 인코더에서 생성된 정보를 바탕으로 원하는 출력 시퀀스를 생성하는 역할을 합니다.
    - 동작: 디코더는 주어진 인코더의 출력과 이전 시간 단계에서의 자신의 상태를 기반으로 다음 출력을 생성합니다. 디코더는 일반적으로 시작 토큰(예: <start>)으로부터 시작하여 시퀀스를 한 단어씩 예측하고, 종료 토큰(예: <end>)이 나올 때까지 계속 진행합니다.

즉, 인코더는 입력 문장 "Hello, how are you?"를 받아 각 단어의 중요한 정보를 담은 은닉 상태로 변환하고 디코드는 인코더에서 나온 정보를 바탕으로 출력 문장을 생성, 디코더는 최종적으로 인코더의 은닉 상태와 이전에 생성한 단어를 사용하여 "안녕, 어떻게 지내니?"와 같은 출력을 생성

<br>

6. Attention Mechanism<br>
모델이 입력 시퀀스의 특정 부분에 집중하도록 하는 메커니즘. 어텐션의 기본 아이디어는 디코더에서 출력 단어를 예측하는 매 시점(time step)마다, 인코더에서의 전체 입력 문장을 다시 한 번 참고하는 것이다. 전체 입력 문장을 전부 다 동일한 비율로 참고하는 것이 아니라, 해당 시점에서 예측해야할 단어와 연관이 있는 입력 단어 부분을 좀 더 집중(attention)하여 살펴봄.

--------------------------------

Google에서 처음 WPM 모델을 개발 할 때, 음성 인식 시스템의 일본어/한국어 분할 문제를 해결하기 위해 먼저 사용함

[해당 논문](https://static.googleusercontent.com/media/research.google.com/ko//pubs/archive/37842.pdf)
