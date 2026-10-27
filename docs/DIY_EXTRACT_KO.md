# 내 게임에서 직접 원문 추출·번역하기 (DIY)

본인이 **직접 구매·설치한** Giggleland 에서 원문 텍스트를 추출하고, 스스로 한국어로 번역해 보고 싶은 사람을 위한
**방법 개요**다. 이 저장소는 게임 원문/번역문을 **포함하지 않으므로**(저작권·스포일러), 텍스트는 각자 자기 게임에서 뽑아야 한다.

> 전제: **자신이 소유한 게임 파일**에 대해, **개인적 용도**로만 한다. 추출물(게임 원문)을 공개 재배포하지 않는다.

---

## 어디에 텍스트가 있나 (요약)

[`HOW_IT_WORKS_KO.md`](HOW_IT_WORKS_KO.md) 참고. 핵심만 다시 적으면:

- 실제 번역 데이터: `Giggleland_Data/resources.assets` 안의 **`LocalizationData`** (Unity ScriptableObject / MonoBehaviour).
- `StreamingAssets/en.csv`·`he.csv` 는 잔재로 보이며 런타임 로딩 근거가 없다 → 여길 봐도 실제 화면 텍스트가 아닐 수 있다.
- 항목 구조: UI 는 `&UI_*` 같은 `&` 키, 대사/자막은 영어 원문 줄 자체가 키, 카테고리 경로(`Dialogue/...`)·노드 id 가 붙고, 줄바꿈은 `|` 토큰.

## 추출 방법 개요

공개된 Unity 에셋 도구로 `resources.assets` 안의 `LocalizationData` MonoBehaviour 를 덤프하면 된다. 대표 도구:

- **AssetRipper** — 에셋을 사람이 읽을 수 있는 형태로 추출. MonoBehaviour/ScriptableObject 덤프에 적합.
- **UABEA (AssetsTools.NET GUI, 구 UABE)** — `resources.assets` 를 열어 특정 MonoBehaviour 를 JSON 으로 내보내기.
- **AssetsTools.NET** — 위 도구들의 기반 라이브러리. 직접 스크립트를 짜서 원하는 필드만 뽑고 싶을 때.

대략의 순서(도구마다 세부 UI 는 다름):

1. 게임을 끈다.
2. 위 도구로 `<게임 폴더>\Giggleland_Data\resources.assets` 를 연다. (원본은 복사해 두고 복사본으로 작업하길 권장.)
3. 타입이 `LocalizationData` 인 MonoBehaviour 를 찾는다. (IL2CPP 게임이라 타입 정보가 필요하면 도구의 IL2CPP/`global-metadata`
   지원 기능을 쓰거나, AssetRipper 처럼 자동 처리하는 도구를 쓴다.)
4. 그 MonoBehaviour 를 **JSON/텍스트로 내보내기(export)** 한다.
5. 내보낸 데이터에서 언어별 문자열 배열(영어 항목)과 키/카테고리/노드 id 를 확인한다.

> 이 저장소는 추출을 **대신 해 주는 도구를 포함하지 않는다.** 위는 공개 도구로 할 수 있는 일의 설명일 뿐이다.

## 추출한 영어 원문을 스스로 번역해 보기

- 추출한 **영어 문자열**을 ChatGPT·Claude 등 생성형 AI 에 붙여 넣고 "한국어로 번역해 줘" 라고 요청하면,
  개인적으로 쓸 초벌 번역을 만들 수 있다.
- 번역 품질 팁(사실):
  - 줄바꿈 토큰 `|` 와 `&` 키, 치환자(`%s`, `{0}`, `<...>` 같은 태그)는 **그대로 보존**해야 게임에서 깨지지 않는다.
  - 대사는 화자·맥락에 따라 톤이 달라지므로, 노드 id/카테고리 경로를 함께 참고한다.
  - UI 라벨은 짧게. 버튼·옵션은 화면 폭을 넘기지 않도록 길이를 맞춘다.
- 이렇게 만든 번역을 게임에 넣으려면 `resources.assets` 안의 `LocalizationData` 각 항목의 `en` 값을 한국어로 바꿔 쓰면 된다(게임에 인게임 언어 선택 UI가 없어 `en` 값을 직접 교체하는 방식이다). 한글이 네모(□)로 나오면 한글 폰트도 함께 넣어야 한다. 원리는 [`HOW_IT_WORKS_KO.md`](HOW_IT_WORKS_KO.md).

## 하지 말아야 할 것

- 추출한 **게임 원문이나 번역문을 공개 저장소·커뮤니티에 그대로 올리지 않기** (저작권·스포일러).
- 남의 게임 파일이나 Steam 계정으로 추출하지 않기. **자기 소유 게임에서만.**
- 개발사가 추출/배포 중단을 요청하면 따르기.

---

### English summary

Real translation strings are in a `LocalizationData` ScriptableObject inside
`Giggleland_Data/resources.assets` (the `StreamingAssets/*.csv` files look like leftovers). You can dump
that MonoBehaviour from **your own copy** of the game using public Unity asset tools (AssetRipper, UABEA /
AssetsTools.NET), then translate the extracted English strings yourself (e.g. with an AI assistant),
preserving tokens like `|`, `&`-keys and format placeholders. This repository ships **no** game text and
**no** extraction tool — only this factual overview. Keep extracted text private; do not redistribute.
