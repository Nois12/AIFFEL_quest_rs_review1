# AIFFEL Campus Online Code Peer Review Templete
- 코더 : 박희지
- 리뷰어 : 조영근

# PRT(Peer Review Template)
- [x]  **1. 주어진 문제를 해결하는 완성된 코드가 제출되었나요?**
    - 데이터 다운로드부터 전처리, 모델 구성, 학습, 체크포인트 저장, 지표 계산, 예문 답변, beam search, 대화형 로드까지 실행 가능한 흐름이 연결되어 있다. 최종 결과물에 해당하는 학습 지표와 예문 답변도 노트북 출력에 포함되어 있다.

    
- [x]  **2. 전체 코드에서 가장 핵심적이거나 가장 복잡하고 이해하기 어려운 부분에 작성된 
주석 또는 doc string을 보고 해당 코드가 잘 이해되었나요?**
    - `build_corpus()`, `lexical_sub()`, `typo_noise()`, `augment_corpus()`, `run_experiment()` 등에 docstring이 있다. 특히 “같은 답변을 가진 쌍을 train과 validation에 나누면 이미 외운 답변으로 평가하게 된다”, “답변은 디코더 정답이므로 질문만 증강한다”와 같이 구현 이유까지 Markdown으로 설명했다.
        
- [x]  **3. 에러가 난 부분을 디버깅하여 문제를 해결한 기록을 남겼거나
새로운 시도 또는 추가 실험을 수행해봤나요?**
    - 구버전 gensim으로 저장된 `ko.bin`을 gensim 4.x의 `Word2Vec.load()`로 읽을 때 `wv` 속성 오류가 발생한 원인을 기록했다. 이후 Python 2 pickle을 `latin1`로 직접 읽고 `index2word`와 `syn0`를 `KeyedVectors`로 옮기는 해결책을 구현했다.
    - 또한 baseline에서 dropout, label smoothing, learning-rate scale, 중복 제거 기준과 분할 방식을 단계적으로 바꾸는 추가 실험을 수행했다. 실험마다 한 가지 변경을 유지하려는 원칙과 validation set이 달라 직접 비교할 수 없는 실험을 구분한 점이 특히 좋다.
        
- [x]  **4. 회고를 잘 작성했나요?**
    - 회고에서 데이터 설계가 모델보다 성능에 큰 영향을 준 점, 질문만 증강해야 하는 이유, 과적합이 완전히 해결되지 않은 점, BLEU의 한계, 낮은 생성 다양성을 구체적인 수치와 함께 기록했다. 실행 플로우도 원본 CSV에서 평가와 체크포인트까지 단계별로 설명되어 있어 독자가 전체 구조를 빠르게 파악할 수 있다.
        
- [x]  **5. 코드가 간결하고 효율적인가요?**
    - 반복되는 처리를 함수로 분리했고, 후보 캐시와 배치 단위 평가를 사용했다. `load_run()`으로 학습을 다시 수행하지 않고 기록과 체크포인트를 재사용할 수 있는 점도 효율적이다. 시드를 고정하고 실험 설정을 딕셔너리로 관리하여 재현성도 확보했다.


 - [x] **6. 프로젝트 루브릭 기준 리뷰**

   - 1. 전처리와 약 3만 건 훈련데이터 구축
        - `preprocess_sentence()`에서 영문 소문자화, 허용 문자 외 제거, 다중 공백 축소를 수행한다. 이후 Mecab 형태소 분석, 최대 길이 20 필터, `(질문, 답변)` 쌍 중복 제거를 적용한다. 답변 단위로 train/validation을 분할하여 동일 답변이 두 세트에 함께 나타나지 않게 한 점은 평가 누수를 줄이는 설계다.
        - 증강은 train에만 적용되며, 원본 질문·Lexical Substitution 질문·오타 질문을 각각 답변과 연결한다. 답변에는 증강을 적용하지 않아 디코더가 오타나 비문을 정답으로 학습하지 않도록 했다. 결과는 원본 10,430쌍, 유사어 치환 9,530쌍, 오타 노이즈 10,367쌍, 총 30,327쌍이다.
        - 추가로 형태소 길이 분포를 분석하여 `MAX_LEN=20`을 선택했고, 20 길이 컷에서 원본 쌍의 98.66%가 남는다는 근거를 제시했다.
        - <img width="779" height="355" alt="06_native_length_distribution" src="https://github.com/user-attachments/assets/8d0cc20f-d0b0-46f9-b19b-ff5a734840a1" />
        
    - 2. Transformer 학습 안정성과 과적합 점검
        - 모델 설정은 `n_layers=1`, `d_model=368`, `n_heads=8`, `d_ff=1024`, `dropout=0.4`이며, 학습에는 warmup 1,000 step, batch size 64, label smoothing 0.1, lr scale 0.5를 적용했다. `run_experiment()`는 매 epoch마다 train loss, validation loss, validation BLEU, distinct-2, 예문 답변을 기록한다.
        - `pair_dedup` 실험에서 validation loss 기준 best는 epoch 3의 3.3882이고, BLEU 기준 best는 epoch 4의 0.0447이다. epoch 4 이후 validation loss가 상승하므로 마지막 epoch를 무조건 사용하지 않고 best checkpoint를 선택한 것은 과적합 대응으로 적절하다. 또한 loss 기준과 BLEU 기준의 best 모델을 별도로 저장해 평가 목적에 맞는 모델을 재사용할 수 있게 했다.
        - 학습 곡선과 BLEU·distinct-2를 함께 시각화하여 단일 지표만으로 모델을 판단하지 않은 점도 좋다. 다만 best BLEU가 0.0447에 머물고 best 시점의 distinct-2가 0.106으로 높지 않으므로, “안정적으로 훈련되었다”는 판정은 **과적합을 관리하는 학습 절차를 갖추었다는 의미**로 이해하는 것이 적절하다. 생성 품질 자체는 추가 개선 여지가 있다.
        - <img width="1290" height="370" alt="07_native_learning_curve" src="https://github.com/user-attachments/assets/99ca0d46-684d-4516-bc74-a13c536bce2a" />
        
    - 3. 질문에 대한 생성 답변 사례
        - 노트북은 다음과 같은 예문을 학습 중 매 epoch에 출력하고, 최종 단계에서 greedy와 beam search를 비교한다.
        - <img width="471" height="450" alt="image" src="https://github.com/user-attachments/assets/015dbdf7-0095-4590-8973-c81858985683" />
        - 이처럼 적절한 사례와 어색한 사례를 모두 제시한 점이 좋다. 결과 해석에서 best loss 모델의 범용 답변과 낮은 `distinct-2`를 언급하여 모델 성능을 과장하지 않았다.
        
# 회고(참고 링크 및 코드 개선)

```
# 개선한다면 회고 마지막에 다음 실험의 우선순위를 추가하는 것이 좋다. 예를 들어 다중 참조 답변 평가, 더 다양한 의미 보존형 증강, early stopping 기준 비교, beam search의 길이 패널티 조정 등을 구체적인 후속 과제로 적을 수 있다.리뷰어의 회고를 작성합니다.
# 다만 일부 코드 줄이 PEP 8 권장 길이를 넘고, Windows 경로와 Linux 경로를 모두 고려한 경로 처리가 더 명시적이면 좋다. 또한 `run_experiment()`가 학습·평가·예문 출력·체크포인트 저장을 모두 담당하므로, 장기적으로는 학습 루프와 평가·기록 함수를 분리하면 테스트와 유지보수가 쉬워진다.
# 이 리뷰를 통해 챗봇 프로젝트에서는 모델 구조보다 **데이터 분할과 평가 설계가 먼저 검증되어야 한다**는 점을 확인했다. 동일 답변이 train과 validation에 섞이지 않도록 답변 단위로 분할한 설계는 validation 결과의 신뢰도를 높인다. 또한 loss, BLEU, distinct-2, 실제 예문 답변을 함께 보고 판단한 접근은 한 지표의 한계를 보완한다.
# 가장 우선순위가 높은 개선은 생성 답변의 다양성과 의미 적합성을 함께 높이는 것이다. 현재 best 모델은 validation loss와 BLEU 기준으로 선택되지만, `distinct-2`가 낮고 범용 답변이 반복된다. 다음 단계에서는 early stopping에 BLEU 또는 다중 지표 조건을 반영하고, 동일 질문에 대한 복수 참조 답변을 활용하는 평가를 추가하는 것이 좋다. 증강도 단순한 단어 치환을 넘어 문장 의미를 보존하는 paraphrase 데이터와 실제 사용자 오타 데이터를 검증셋과 분리하여 비교할 수 있다.
```


