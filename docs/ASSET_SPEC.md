# 에셋 규격 (Blender → glb → Godot)

모든 3D 에셋은 Blender로 직접 제작하고, 텍스처는 CC0 라이선스 사이트에서 조달한다.
이 문서는 제작 규격과 파이프라인을 정한다. 출처 기록은 저장소 루트의 `ASSETS.md`가 담당한다.

## 1. 원칙

| 원칙 | 내용 |
|---|---|
| 직접 제작 | 모델·리깅·애니메이션은 직접 만든다. 외부 모델 사용 금지 |
| 텍스처 조달 | ambientCG(CC0) 우선. 다른 사이트는 라이선스 확인 후 `ASSETS.md`에 근거 링크를 적고 사용한다 |
| 기록 | 쓰는 텍스처·사운드·폰트는 전부 `ASSETS.md`에 출처/라이선스/URL을 적는다 |
| 스타일 | 로우폴리 스타일라이즈. 사실적 고폴리와 고해상도 스캔 모델은 쓰지 않는다 |

## 2. 단위와 좌표

- 1 Blender 유닛 = 1m. 씬 단위 시스템은 Metric, Unit Scale 1.0.
- 원점은 오브젝트가 놓이는 바닥 중심. 캐릭터는 발 사이 바닥.
- 적용(Apply): 내보내기 전 Location 제외 Rotation/Scale을 적용(Ctrl+A)해 스케일 1을 만든다.
- 앞쪽 방향은 -Y(Blender), glTF 내보내기에서 +Y Up을 켠다. Godot에서는 -Z가 앞이다.

## 3. 폴리곤 예산 (삼각형 기준, 기본값)

| 종류 | 예산 | 비고 |
|---|---|---|
| 소품(작은 오브젝트) | 500 이하 | |
| 중형 오브젝트/가구 | 2,000 이하 | |
| 건물/구조물 모듈 | 5,000 이하 | 모듈은 격자(1m 단위)에 맞춘다 |
| 주인공/주요 캐릭터 | 6,000 이하 | |
| 일반 캐릭터/적 | 3,000 이하 | |

예산을 넘기면 이유를 `ASSETS.md` 비고에 적고 테크니컬 디렉터 승인을 받는다.

## 4. 텍스처

| 항목 | 규칙 |
|---|---|
| 해상도 | 기본 1K. 주인공/클로즈업 자산만 2K. 4K 금지 |
| 맵 | Color(albedo), NormalGL, Roughness, (필요 시) AmbientOcclusion, Metalness |
| 노멀맵 | OpenGL(Y+) 형식. ambientCG는 `NormalGL`을 쓴다 |
| 형식 | PNG 또는 JPG. 컬러는 sRGB, 나머지는 선형 |
| 타일링 | 모듈형 자산은 타일링 텍스처, 소품은 UV 한 장 |

**ambientCG 가져오기**
- 검색 API: `https://ambientcg.com/api/v2/full_json?q=<검색어>&type=Material&include=downloadData`
- 다운로드: `https://ambientcg.com/get?file=<AssetId>_1K-JPG.zip` (예: `Rock030_1K-JPG.zip`)
- 라이선스: CC0. 크레딧은 필수가 아니지만 `ASSETS.md`에는 기록한다.
- 받은 파일은 `assets/textures/<에셋ID>/`에 풀고, 원본 zip은 커밋하지 않는다.

## 5. 이름과 폴더

```
assets/
  models/<분류>/<분류>_<이름>[_<변형>].glb     예: prop_crate_wood_01.glb
  textures/<에셋ID>/<에셋ID>_1K-JPG_Color.jpg
  audio/<분류>/...
  source/<분류>/<이름>.blend                    (원본 .blend, 1MB 초과 시 LFS)
```

- 소문자 영문과 숫자, 밑줄(`_`)만 쓴다. 공백·한글 금지.
- 분류 접두어: `prop_`, `env_`, `char_`, `veh_`, `ui_`, `fx_`.
- 버전은 파일명이 아니라 git 이력으로 관리한다(`_final`, `_v2` 금지).

## 6. 내보내기 (Blender → glb)

```python
import bpy
bpy.ops.export_scene.gltf(
    filepath=r"C:\git\reflepaperclip\testgamestudio\assets\models\prop\prop_crate_wood_01.glb",
    export_format='GLB',
    export_apply=True,        # 모디파이어 적용
    export_yup=True,
    use_selection=True,       # 선택한 오브젝트만
    export_image_format='AUTO',
)
```

점검 항목(내보내기 전, Blender에서 수치로 확인한다):
- 삼각형 수 ≤ 예산
- Dimensions가 실제 크기(m)와 맞다
- Scale 1,1,1 / Rotation 0
- 머티리얼 슬롯 수 ≤ 2(소품), 사용하지 않는 머티리얼 없음
- UV가 0~1 범위 안에 있다

## 7. Godot 임포트

- glb를 `res://assets/models/...`에 두면 자동 임포트된다.
- 머티리얼은 `StandardMaterial3D`로 들어온다. 텍스처 연결이 끊기면 `res://assets/textures/...` 경로를 수동 지정한다.
- 충돌은 Blender에서 `-col` 접미어 메시로 만들거나 Godot에서 `Create Trimesh/Convex Collision`을 쓴다.
- 임포트 후 `res://` 경로를 기획서/씬에 기록한다.

## 8. 검증 (눈으로 볼 수 없을 때)

에이전트는 화면을 볼 수 없다. 수치와 로그로 확인한다.
1. Blender `get_scene_info`로 오브젝트 목록과 치수를 읽는다.
2. 파이썬으로 삼각형 수와 바운딩 박스를 출력해 규격표와 대조한다.
3. Godot MCP `run_project`로 씬을 실행하고 `get_debug_output`에 오류가 없는지 본다.
4. 시각 품질은 사용자(또는 사람) 점검 게이트에서 확인한다.
