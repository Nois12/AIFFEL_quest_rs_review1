# AIFFEL Campus Online Code Peer Review Templete
- 코더 : 박희지
- 리뷰어 : 김시온


# PRT(Peer Review Template)
- [X]  **1. 주어진 문제를 해결하는 완성된 코드가 제출되었나요?**
    - 문제에서 요구하는 최종 결과물이 첨부되었는지 확인
        - 중요! 해당 조건을 만족하는 부분을 캡쳐해 근거로 첨부

    문제에서 요구하는 조건은 다음과 같았다.

    - 조건 1) 인식결과의 시각화 및 성능 분석을 적절히 수행하였는가?
    - 조건 2) 분류근거를 설명 가능한 Class activation map을 얻을 수 있는가?
    - 조건 3) CAM을 얻기 위한 기본모델의 구성과 학습이 정상 진행되었는가?


    -> 조건 1) visualize_cam_on_image로 CAM을 원본 이미지에 히트맵 합성하였다.또한 get_bbox로 바운딩박스 추출하고 visualize_both_bbox_on_image로 정답 박스와 비교 시각화하고 get_iou로 IoU 계산하였다.그리고 valid_dataset 200장에 대해 CAM(layer4)/GradCAM(layer4)/GradCAM(layer3) 3가지 방법의 mean IoU, median IoU, IoU>0.5 비율, pointing game까지 정량 비교하였다.

     <img width="511" height="213" alt="image" src="https://github.com/user-attachments/assets/0f7b27d9-9b66-437e-823c-a57b62c4a7cc" />

     -> 조건 2) 분류근거를 설명 가능한 CAM 획득하였다. (forward hook + FC 가중치 가중합)
       
     <img width="643" height="507" alt="image" src="https://github.com/user-attachments/assets/0562dd9a-baf3-4b27-a26d-ff338a21954c" />


     -> 조건 3) CAM을 얻기 위한 기본모델 구성과 학습을 하였다.
       
      <img width="511" height="122" alt="image" src="https://github.com/user-attachments/assets/4ea5d791-61e1-4692-91c4-e21df53c739c" />

    
- [X]  **2. 전체 코드에서 가장 핵심적이거나 가장 복잡하고 이해하기 어려운 부분에 작성된 
주석 또는 doc string을 보고 해당 코드가 잘 이해되었나요?**
    - 해당 코드 블럭을 왜 핵심적이라고 생각하는지 확인
    - 해당 코드 블럭에 doc string/annotation이 달려 있는지 확인
    - 해당 코드의 기능, 존재 이유, 작동 원리 등을 기술했는지 확인
    - 주석을 보고 코드 이해가 잘 되었는지 확인
        - 중요! 잘 작성되었다고 생각되는 부분을 캡쳐해 근거로 첨부

        -> generate_cam, generate_grad_cam에 hook 등록/제거 목적, 가중합 계산 이유가 인라인 주석으로 설명된다.
      
       <img width="623" height="147" alt="image" src="https://github.com/user-attachments/assets/98bac3eb-47dd-47c0-84af-d50309167a51" />

        
- [X]  **3. 에러가 난 부분을 디버깅하여 문제를 해결한 기록을 남겼거나
새로운 시도 또는 추가 실험을 수행해봤나요?**
    - 문제 원인 및 해결 과정을 잘 기록하였는지 확인
    - 프로젝트 평가 기준에 더해 추가적으로 수행한 나만의 시도, 
    실험이 기록되어 있는지 확인
        - 중요! 잘 작성되었다고 생각되는 부분을 캡쳐해 근거로 첨부

      -> threshold별 IoU 민감도 스윕, tuning/test 셋 분리 후 최적 threshold 비교, threshold에 무관한 pointing game 지표 도입등 추가 실험을 매우 풍부하게 수행하였다.
      
    <img width="407" height="400" alt="image" src="https://github.com/user-attachments/assets/ee6fb03d-27c0-4562-af8b-fca2ca5c190c" />

          
        
- [X]  **4. 회고를 잘 작성했나요?**
    - 주어진 문제를 해결하는 완성된 코드 내지 프로젝트 결과물에 대해
    배운점과 아쉬운점, 느낀점 등이 기록되어 있는지 확인
    - 전체 코드 실행 플로우를 그래프로 그려서 이해를 돕고 있는지 확인
        - 중요! 잘 작성되었다고 생각되는 부분을 캡쳐해 근거로 첨부


        -> 결과 분석과 회고가 잘 작성되어있다.
      
        <img width="748" height="283" alt="image" src="https://github.com/user-attachments/assets/fea25a6b-7cfd-4c97-9acc-45c5f5cf3e08" />

        
- [X]  **5. 코드가 간결하고 효율적인가요?**
    - 파이썬 스타일 가이드 (PEP8) 를 준수하였는지 확인
    - 코드 중복을 최소화하고 범용적으로 사용할 수 있도록 함수화/모듈화했는지 확인
        - 중요! 잘 작성되었다고 생각되는 부분을 캡쳐해 근거로 첨부

       -> generate_cam, generate_grad_cam, get_bbox 등 재사용 가능한 함수로 모듈화가 잘 되어있다.
      
      <img width="396" height="75" alt="image" src="https://github.com/user-attachments/assets/c1c29502-43f3-4a1f-855d-a92793f2e3ea" />



# 회고(참고 링크 및 코드 개선)
```
# 리뷰어의 회고를 작성합니다.
# 코드 리뷰 시 참고한 링크가 있다면 링크와 간략한 설명을 첨부합니다.
# 코드 리뷰를 통해 개선한 코드가 있다면 코드와 간략한 설명을 첨부합니다.
```
리뷰어 (김시온) : 요구한 조건에 맞으면서도 간결하고 효울적이게 작성이 되어있고, 풍부한 추가실험을 하였고 회고도 잘 작성되어있다.

