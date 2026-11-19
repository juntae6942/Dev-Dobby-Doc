---
category: experience
type: undergraduate-research
title: 오토인코더 이미지 증강을 적용한 조류 200종 분류
organization: 충남대학교 DIPLab
role: 학부연구생 · 논문 제1저자
period: "2024년 5월 실험 기록 기준"
tech_stack:
  - Python
  - TensorFlow / Keras
  - Albumentations
  - NumPy
  - Matplotlib
  - Jupyter Notebook
keywords:
  - 컴퓨터 비전
  - 세립 조류 종 분류
  - 오토인코더
  - 이미지 증강
  - 전이 학습
  - CUB-200-2011
impact: "오토인코더 재구성 이미지 증강을 추가한 조류 분류 실험에서 기본 증강 대비 Top-1 정확도 1.23%p 향상(72.52% → 73.75%)을 보고"
---

# DIPLab 학부연구 · 오토인코더 이미지 증강을 적용한 조류 분류

충남대학교 DIPLab 학부연구생으로 오토인코더 재구성 이미지가 조류 종 분류 모델의 일반화에 도움이 되는지 연구했다. CUB-200-2011의 조류 200종 이미지 11,788장(훈련용 5,994장, 테스트용 5,794장)으로 기본 증강과 오토인코더 증강을 비교하고, 「오토인코더를 사용한 데이터 증강」에 제1저자로 결과를 정리했다. [연구 저장소](https://github.com/juntae6942/encoder_bird_classification)

## 실험 설계와 맡은 역할

- **비교 실험 설계** — 기본 증강으로 좌우 반전과 최대 20도 회전을 적용하고, 오토인코더 재구성 이미지를 추가로 활용한 조건과 비교했다. 논문에서는 원본을 그대로 복제하는 데 그치지 않도록 오토인코더 학습의 입력 전처리에 가우시안 블러를 사용했다.

- **학습 데이터 파이프라인** — TFRecord에서 JPEG와 클래스 라벨을 읽고, 이미지를 224×224로 조정·정규화했다. 라벨을 200개 클래스의 원-핫 벡터로 변환하고, `tf.data`의 병렬 매핑과 프리페치로 학습 입력을 구성했다.
- **오토인코더 기반 이미지 증강** — Albumentations로 무작위 좌우 반전과 회전을 적용한 뒤, 학습 이미지마다 50% 확률로 저장된 오토인코더 모델을 실행해 재구성 이미지로 교체했다. 검증 데이터에는 크기 조정과 정규화만 적용했고, 별도 샘플로 원본과 재구성 이미지를 나란히 시각화했다.
- **분류 모델 학습** — ImageNet 가중치로 초기화한 InceptionResNetV2에 Global Average Pooling과 200클래스 softmax 분류층을 붙였다. Adam, 지수형 학습률 감쇠, 라벨 스무딩을 적용해 15에폭 동안 학습하고, 학습·검증 정확도와 손실을 기록했다.

[실험 노트북](https://github.com/juntae6942/encoder_bird_classification/blob/64a1c52/encoder_bird_classification.ipynb)

## 실험 결과와 해석

논문 표 1에서 기본 증강의 Top-1 정확도는 **72.52%**, 오토인코더 재구성 이미지를 활용한 조건은 **73.75%**였다. CUB-200-2011에서 **1.23%p 향상**을 관찰했다. 다른 데이터셋에서의 효과는 추가 검증이 필요하다.

저장소의 노트북에는 오토인코더 증강 조건의 15에폭 실행이 남아 있으며, 마지막 에폭의 검증 정확도도 73.75%였다. 논문 표의 손실 값과 노트북의 마지막 에폭 검증 손실은 서로 달라, 비교 성과는 논문 표의 Top-1 정확도로 정리했다. [학습 로그와 그래프](https://github.com/juntae6942/encoder_bird_classification/blob/64a1c52/encoder_bird_classification.ipynb)

## 연구 자료

- 박준태·이광희·장경선, 「오토인코더를 사용한 데이터 증강」(Data Augmentation Using Autoencoder), 그림 1–2 및 표 1.
