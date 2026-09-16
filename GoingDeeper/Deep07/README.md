# AIFFEL Campus Online Code Peer Review Templete
- 코더 : 박희지
- 리뷰어 : 강지수

# PRT(Peer Review Template)

- [x] **1. 주어진 문제를 해결하는 완성된 코드가 제출되었나요?**
    - MLM/NSP 데이터 생성부터 약 1M parameter의 mini BERT 구현,
      10 epoch 학습 및 결과 시각화까지 모든 루브릭이 구현되어 있습니다.
      <img width="1104" height="635" alt="스크린샷 2026-09-16 오전 10 38 26" src="https://github.com/user-attachments/assets/a3d844e3-f1d0-4874-afc9-a7b64b9c4ed7" />


- [x] **2. 핵심 코드의 주석 및 doc string을 통해 코드가 잘 이해되었나요?**
    - `create_pretrain_mask()`, `create_pretrain_instances()`,
      `load_pre_train_data()`, `run_pretrain()` 등 주요 함수에
      입력/출력과 처리 목적이 구체적으로 설명되어 있어
      전체 pretraining pipeline을 따라가기 쉬웠습니다.
      <img width="956" height="651" alt="스크린샷 2026-09-16 오전 10 39 14" src="https://github.com/user-attachments/assets/c9488aa6-4f23-4078-b47e-86c23fd8de46" />

- [x] **3. 새로운 시도 또는 추가 실험을 수행했나요?**
    - base 실험의 NSP accuracy를 그대로 받아들이지 않고,
      짧은 문서 제목이 shortcut으로 작동하는 것을 조건별 분석으로 발견했습니다.
    - 이후 제목 제거 + document-level validation split을 적용한
      두 번째 실험을 통해 전체 점수는 낮아지더라도
      일반 문장 pair의 성능과 평가의 신뢰도를 높인 과정이 !!특히!! 좋았습니다.
    - MLM도 전체 accuracy만 보지 않고 `[MASK]`, random,
      unchanged 조건별 accuracy와 실제 Top-5 prediction을 분석해
      모델이 무엇으로 정답을 맞혔는지를 확인한 점이 좋았습니다. ... 저도 배워야 할 부분이었습니다.
      <img width="1011" height="179" alt="스크린샷 2026-09-16 오전 10 40 22" src="https://github.com/user-attachments/assets/9552ca28-2471-42f7-b874-9112191dbbee" />
      <img width="1240" height="494" alt="스크린샷 2026-09-16 오전 10 40 03" src="https://github.com/user-attachments/assets/59fe3fff-16ca-42c4-9c2c-ba08a1ccbeff" />

- [x] **4. 회고를 잘 작성했나요?**
    - shortcut learning, static masking, 일부 corpus만 사용한 점, MLM이 문맥보다 빈도 높은 기호를 예측하는 현상 등
      실험의 한계를 구체적으로 정리했습니다.
      <img width="1271" height="251" alt="스크린샷 2026-09-16 오전 10 42 18" src="https://github.com/user-attachments/assets/2158093d-356f-4af6-9920-d2bb811f4245" />
      <img width="1266" height="168" alt="스크린샷 2026-09-16 오전 10 43 06" src="https://github.com/user-attachments/assets/a3d6a0a0-477a-4ee4-a818-d3af28e0545c" />


- [x] **5. 코드가 간결하고 효율적인가요?**
    - 희지님의 코드는 언제나 전처리, 검증, 모델, 학습, 평가가 함수 단위로 잘 분리되어 있어
      실험 조건을 변경하고 반복하기 좋은 구조입니다. 믿어 의심치 않습니다.


# 회고

희지님 코드를 리뷰하면서 높은 accuracy 자체보다
"모델이 무엇을 보고 맞혔는가"를 조건별로 분해해 확인하는 것이 중요하다는 점 다시 한 번 깨달았습니다. 

!!특히!! 제목이라는 shortcut을 발견하고, (지난번 DLThon 때도 느꼈지만 희지님은 누수, 쇼트컷 예방을 탁월하게 잘하는 꼼꼼 관리자)
document-level split과 제목 제거 실험으로 가설을 다시 검증한 과정이 인상적이었습니다.

추가로 LMS의 NSP negative 생성 방식은 A/B 순서를 바꾸는 방식이고 저도 그 방식으로 했어요.
원 BERT의 random-next NSP와 어떤 차이가 있는지 알아보면 좋겠지만 시간이 부족했는데, 희지님은 NSP 방식에 대해 추가 연구하실 의향이 있으려나요...?

좋은 코드를 보여주셔서 고맙습니다. 
