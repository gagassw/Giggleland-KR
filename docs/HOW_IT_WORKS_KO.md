# 동작 원리 (사실 스펙)

패치 구조를 사람이나 도구가 바로 검토할 수 있도록 사실만 적는다. 스포일러는 없다.

## 1. 게임 기본 정보

| 항목 | 값 |
|---|---|
| 게임 | Giggleland (Steam, AppID 3370490) |
| 엔진 | Unity 2022.3.62f2, IL2CPP 백엔드 |
| 데이터 폴더 | `Giggleland_Data` |
| 기본 언어 | 영어 (영어 단독 출시) |

## 2. 로컬라이즈 구조

- 번역 데이터는 `Giggleland_Data/resources.assets` 안의 **`LocalizationData`** ScriptableObject(MonoBehaviour)에 들어 있고, 게임의 `LocalizationManager` 가 이를 읽어 텍스트를 그린다.
- 항목 키: UI 문자열은 `&UI_*` 형태의 키, 대사/자막은 영어 원문 줄 자체가 키다. 각 항목은 `en`/`ja`/`zh` 등 언어별 값을 가진다.
- 게임에는 인게임 언어 선택 UI가 없다. 설정(`settings.json`)의 `language` 를 `en` 외의 값으로 바꾸면 게임이 다시 `en` 으로 되돌린다.
- 폰트는 TextMeshPro(TMP) 3.0.7을 쓴다. 게임에 포함된 폰트 에셋은 영문·기호 위주라 한글 글리프가 없어, 한국어를 넣으면 네모(□)로 표시된다.
- `StreamingAssets/*.csv` 는 UI/테스트 잔재로 보이며, 실제 화면 텍스트는 위 `LocalizationData` 에서 나온다.

## 3. 패치가 하는 일 (resources.assets 편집)

게임이 `en` 으로 구동되는 구조를 그대로 이용한다.

1. **텍스트**: `LocalizationData` 각 항목의 `en` 값을 한국어로 덮어쓴다(약 1616개). 번역이 없는 항목은 영어로 남는다.
2. **폰트**: 비어 있던 폰트 슬롯(`NotoSansJP`, 동적 폰트라 글리프 0개)을 한글 Static SDF 폰트(나눔고딕, 실사용 약 845자 + ASCII)로 교체하고 2048×2048 아틀라스를 넣는다. 주요 폰트들의 폴백 테이블(`m_FallbackFontAssetTable`)에 이 한글 폰트를 등록해, 영문은 원래 폰트로, 한글은 폴백으로 렌더되게 한다.

두 변경 모두 `resources.assets` 안에서 이뤄진다. 폰트 아틀라스 이미지도 이 파일에 인라인으로 들어가므로 `.resS` 등 다른 파일은 바뀌지 않는다.

## 4. 제작 방법

- [AssetsTools.NET](https://github.com/nesrak1/AssetsTools.NET)(오픈소스)으로 `resources.assets` 의 해당 애셋을 읽고 써서 패치본을 만든다.
- 한글 폰트 애셋은 게임과 동일한 Unity 2022.3.62f2 + TextMeshPro 3.0.7로 빌드한다(버전이 달라지면 폰트 애셋 레이아웃이 어긋난다).
- 배포물은 이렇게 만든 `resources.assets` 파일 하나다. 실행 코드는 포함하지 않는다.

## 5. 적용 / 복원

- 적용: 게임의 `resources.assets` 를 패치본으로 교체한다(기존 파일은 `resources.assets_old` 로 백업).
- 복원: `resources.assets_old` 를 되돌리거나 Steam 파일 무결성 검사를 돌린다.

## 6. 알려진 한계

- 이미지·영상에 그림으로 박힌 글자는 바뀌지 않는다(텍스트 레이어가 아니다).
- 번역표에 없는 항목은 영어로 남는다.
- 게임 업데이트가 `resources.assets` 를 되돌리면 다시 적용해야 한다.
- 한글 줄바꿈·글자수 넘침은 UI 폭에 따라 손볼 수 있다.

---

### English summary

The game uses its own localization system: strings live in a `LocalizationData` ScriptableObject inside
`Giggleland_Data/resources.assets`, read by `LocalizationManager`. There is no in-game language selector, and
setting `language` to anything other than `en` is reverted by the game. The patch therefore overwrites each
entry's `en` value with Korean (~1616 entries) and replaces an empty font slot (`NotoSansJP`) with a Korean
static SDF font (NanumGothic, ~845 glyphs) built on the same Unity 2022.3.62f2 + TextMeshPro 3.0.7, registering
it in the fallback tables of the main fonts. Everything is inside `resources.assets` (atlas inlined, no `.resS`
change). The distributed artifact is that single data file — no executable code. Built with AssetsTools.NET.
