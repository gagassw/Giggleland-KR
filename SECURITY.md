# 검증 (Verification)

배포물은 `resources.assets` 파일 하나다. Unity 엔진의 에셋 컨테이너(데이터 파일)이며, 게임 실행 코드는 이 파일이 아니라 `GameAssembly.dll` 에 있다.

## 바뀌는 것

- `resources.assets` 안의 `LocalizationData` 텍스트: 영어 → 한국어
- 폰트 에셋: 한글 글리프 추가(폰트 슬롯 1개 교체 + 폴백 등록)

그 외 파일은 바뀌지 않는다.

## 직접 확인하는 법

- [AssetRipper](https://github.com/AssetRipper/AssetRipper) 또는 [UABEA](https://github.com/nesrak1/UABEA) 로 `resources.assets` 를 열어 `LocalizationData` 와 폰트 에셋을 본다.
- Steam 파일 무결성 검사로 원본 `resources.assets` 를 복원해 패치본과 바이너리 비교(diff)한다.
- 바뀐 애셋의 종류와 제작 과정: [`docs/HOW_IT_WORKS_KO.md`](docs/HOW_IT_WORKS_KO.md).

## AI 검토 예시 프롬프트

> 이 파일은 Unity `resources.assets`(에셋 컨테이너)다. 데이터 파일이 맞는지 확인하고, UABEA/AssetRipper로 열었을 때 바뀐 애셋이 번역 텍스트와 폰트뿐인지 알려 줘.

---

## English summary

The distributed artifact is a single `resources.assets` — a Unity asset container (data file); the game's
executable code lives in `GameAssembly.dll`, not here. Changes are limited to `LocalizationData` text
(English → Korean) and font assets (Korean glyphs added). Verify by opening it in AssetRipper/UABEA, or by
restoring the original via Steam's file integrity check and diffing. See `docs/HOW_IT_WORKS_KO.md`.
