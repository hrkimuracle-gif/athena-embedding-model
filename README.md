# Athena 임베딩 모델 마켓플레이스

Athena Code 의 코드베이스 인덱싱용 로컬 임베딩 모델을 플러그인 형태로 배포하는 마켓플레이스 저장소입니다.

- 저장소: https://github.com/hrkimuracle-gif/athena-embedding-model (임시. 이후 사내 git 으로 이전 예정)
- 마켓플레이스 이름: `athena-embedding-model`
- 플러그인: `local-embedding-potion-multilingual-128m` (설치 ID `local-embedding-potion-multilingual-128m@athena-embedding-model`)

## 사용 방법

1. Athena Code 플러그인 설정에서 마켓플레이스 소스를 추가합니다.
   - 주소: `https://github.com/hrkimuracle-gif/athena-embedding-model.git`
   - 인증: 없음 (공개 저장소)
2. 마켓플레이스 목록에서 "로컬 임베딩 모델 (potion-multilingual-128M)" 을 설치합니다.
3. 설정 → 코드베이스 인덱싱 → 유형을 "기본 (로컬 모델)" 로 선택하면 설치된 모델로 인덱싱합니다.

Athena Code 는 모델을 다음 순서로 찾습니다: 확장에 동봉된 모델 → 설치된 마켓플레이스 플러그인 → 이전에 내려받은 캐시 → 설정한 다운로드 주소.
동봉 모델이 없는 빌드에서 이 플러그인을 설치하면 서버나 추가 설정 없이 로컬 인덱싱이 됩니다.

## 저장소 구성

```
marketplace.json                                      마켓플레이스 정의
plugins/local-embedding-potion-multilingual-128m/
  .athena-plugin/plugin.json                          플러그인 정의 (이름, 버전, 설명)
  manifest.json                                       모델 파일 목록과 sha256 (Athena Code 가 로드 시 검증)
  embeddings.int8                                     단어 조각 벡터표 (int8, Git LFS)
  scales.f32                                          int8 복원 배율
  tokenizer.json, tokenizer_config.json               토크나이저
```

`embeddings.int8` 은 128 MB 라 GitHub 의 100 MB 단일 파일 제한 때문에 Git LFS 로 추적합니다.
받는 쪽에는 git-lfs 가 설치되어 있어야 실제 파일이 내려옵니다. 사내 git 으로 옮기면 일반 파일로 두어도 됩니다.

## 모델 갱신

1. `plugins/<이름>/` 의 파일을 새 변환본으로 바꿉니다. 변환은 athena-code 의 `src/scripts/convert-local-embedding-model.mjs` 로 합니다.
2. `.athena-plugin/plugin.json` 의 `version` 을 올립니다.
3. 커밋하고 푸시하면 마켓플레이스에서 새 버전으로 보입니다.

## 포함 모델

- potion-multilingual-128M (minishlab, Apache-2.0) — int8 변환본, 256차원, 약 148 MB
