# 폰으로 찍은 방을 3D 로

방을 십오 초쯤 걸어 다니며 찍은 영상 하나에서 3차원 장면을 만들고 웹에 올린 것입니다.

**보기** https://crystalmoon74.github.io/room-3d-demo/ (창가 화분 실습)
· 원본 데모 https://suncreation.github.io/room-3d-demo/

## 실습 기록: 창가 화분 (2026-11-13)

창가 선반의 화분을 옆으로 걸으며 찍은 영상(`3D_Plants.mp4`)으로 같은 과정을 밟았습니다.
Windows 에서 Colab MCP 로 브라우저의 코랩 노트북을 몰아 T4 GPU 에서 계산했습니다.

| 걸음 | 결과 |
|---|---|
| 영상 | 1280×720 가로, 24fps, 28.1초 (권장 15~20초보다 길어 사진을 32장으로 늘림) |
| 사진 뽑기 | 32장, 0.88초 간격. 흐림 의심 1장(#10), 그대로 사용 |
| 복원 | `facebook/map-anything-apache`, `fp16`, 14.9초, 최대 7.2GB |
| 점 | 3,472,855개 → 멀리 튄 점(축별 0.5~99.5 백분위 밖) 버리고 3,371,328개 |
| 장면 | 2.25 × 1.72 × 3.26 m, 대각선 4.32 m |
| 카메라 이동 | 0.57 m, 대각선 대비 0.132 (0.1 이상이라 정상) |

알갱이 크기를 세 가지로 만들어 견주었습니다. 주소 뒤 `?f=` 로 바꿔 볼 수 있습니다.

| 파일 | 설정 | 알갱이 | 크기 | 보기 |
|---|---|---|---|---|
| `plants.sog` | K=1.4 | 3,371,328 | 17.7MB | [열기](https://crystalmoon74.github.io/room-3d-demo/?f=plants.sog) |
| `plants_k09.sog` | K=0.9 | 3,371,328 | 17.7MB | [열기](https://crystalmoon74.github.io/room-3d-demo/?f=plants_k09.sog) |
| `plants_k09_conf.sog` (기본) | K=0.9, 신뢰도 하위 30% 제거 | 2,359,937 | 12.0MB | [열기](https://crystalmoon74.github.io/room-3d-demo/?f=plants_k09_conf.sog) |

배운 것

- K=1.4 는 이 장면에서 잎과 화분 경계를 뭉갰습니다. K 를 낮추고 신뢰도 낮은 점을 걸러 내면 여러 장의 점이 어긋나 겹친 층이 줄어듭니다
- 모델이 사진을 518×294 로 줄여 받아서 가는 잎의 세부는 처음부터 사라집니다. 원본만큼 선명하려면 이 점들에서 출발해 가우시안 스플랫 학습을 따로 돌려야 합니다
- 런타임 유형을 바꾸면 Colab MCP 연결이 끊겨 멈출 수 있습니다. 노트북 탭을 다시 열고 연결을 새로 걸면 됩니다
- 원본 영상은 저장소에 넣지 않았습니다 (`.gitignore`)

[![Colab 에서 열기](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/SunCreation/room-3d-demo/blob/main/room3d.ipynb)

## 들어 있는 것

| 파일 | 무엇 |
|---|---|
| `room3d.ipynb` | 영상에서 3차원 장면을 만드는 노트북. 코랩에서 T4 로 돕니다 |
| `index.html` | 만든 장면을 브라우저에 띄우는 페이지 |
| `scene.sog` | 원본 데모의 방 장면. 알갱이 320만 개, 15MB (`?f=scene.sog`) |
| `plants*.sog` | 창가 화분 실습 결과 세 판. 위 표 참조 |
| `.nojekyll` | 깃허브가 파일 이름을 손대지 않게 하는 표시 |
| `skills/room-3d/` | 코딩 에이전트에게 주는 지침. 아래 참조 |

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

## 코딩 에이전트에게 시키려면

`skills/room-3d` 폴더를 통째로 복사해 `.claude/skills/` 아래에 두면 된다.

```bash
git clone https://github.com/SunCreation/room-3d-demo.git
mkdir -p .claude/skills && cp -r room-3d-demo/skills/room-3d .claude/skills/
```

그다음 "이 영상으로 방을 3D 로 만들어 웹에 올려 줘" 처럼 말하면 된다.
지침에는 이런 것이 들어 있다.

- 이 기계의 사양을 보고 어디서 계산할지 정하는 방법. 필요하면 코랩 CLI 를 깔고 세션까지 잡는다
- 걸음마다 무엇을 보고 잘됐는지 판단하는 기준
- 조용히 실패하는 함정들. `fp16`, `SparkRenderer`, `pcsogszip`, 100MiB 관문
- 갈림길에서는 혼자 정하지 않고 물어보게 되어 있다

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
