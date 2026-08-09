# 폰으로 찍은 방을 3D 로

방을 십오 초쯤 걸어 다니며 찍은 영상 하나에서 3차원 장면을 만들고 웹에 올린 것입니다.

**보기** https://suncreation.github.io/room-3d-demo/

[![Colab 에서 열기](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/SunCreation/room-3d-demo/blob/main/room3d.ipynb)

## 들어 있는 것

| 파일 | 무엇 |
|---|---|
| `room3d.ipynb` | 영상에서 3차원 장면을 만드는 노트북. 코랩에서 T4 로 돕니다 |
| `index.html` | 만든 장면을 브라우저에 띄우는 페이지 |
| `scene.sog` | 알갱이 320만 개, 15MB |
| `.nojekyll` | 깃허브가 파일 이름을 손대지 않게 하는 표시 |

## 어떻게 만드나

```
영상  →  사진 24장  →  점 320만 개  →  알갱이  →  15MB  →  이 주소
                            └─────→  메시  (게임에 쓰려면)
```

가운데 점을 만드는 일만 AI 가 하고 나머지는 파일을 바꾸는 일입니다.
쓰는 모델은 [MapAnything](https://github.com/facebookresearch/map-anything) 의
`facebook/map-anything-apache` 이고 저장소와 가중치 모두 Apache-2.0 입니다.

T4 에서 잰 시간입니다. **대부분이 모델 내려받기**이고 실제 계산은 십 초 남짓입니다.

| 걸음 | 시간 |
|---|---|
| 설치 | 약 25초 |
| 사진 뽑기 | 약 6초 |
| 모델 준비 | 약 180초 |
| 복원 사진 24장 | 약 10초, 최대 6.7GB |
| 알갱이 파일 쓰기 | 약 1초 |
| 줄이기 | 약 30초 |

## 찍는 요령

- **걸어 다니며** 찍습니다. 제자리에서 몸만 돌리면 거리를 못 잡습니다
- **가로로** 듭니다. 세로로 들면 천장과 바닥이 화면을 차지하는데 둘 다 밋밋합니다
- **천천히**, 십오 초에서 이십 초

## 띄울 때 걸리는 함정 둘

- `SparkRenderer` 를 장면에 넣지 않으면 알갱이가 다 올라가고도 화면이 까맣습니다. 오류도 안 납니다
- `.sog` 의 안쪽 이름은 `pcsogszip` 입니다. 확장자와 달라서 그대로 적으면 안 읽힙니다

## 라이선스

three.js MIT, [Spark](https://github.com/sparkjsdev/spark) MIT,
[splat-transform](https://github.com/playcanvas/splat-transform) MIT,
MapAnything Apache-2.0.
