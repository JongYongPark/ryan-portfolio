# command list
``` bash
# Push contents
gggg
# Check contents in local
hugo server -D
```
# githujb auth login
```bash
(base) PS C:\workspace\my-proj\hugo-demo> gh auth login
? Where do you use GitHub? GitHub.com
? What is your preferred protocol for Git operations on this host? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Paste an authentication token
Tip: you can generate a Personal Access Token here https://github.com/settings/tokens
The minimum required scopes are 'repo', 'read:org', 'workflow'.
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as JongYongPark
```
# git config
```bash
  gh api user

  # 확인
  git config user.email
  git config user.name
  # global -회사의 계정도 있으므로 이것 사용금지지
  git config --global user.email "lovemoneyjoy@gmail"
  git config --global user.name "JongYong Park"

  # local 
  git config --local user.email "lovemoneyjoy@gmail"
  git config --local user.name "JongYong Park"

```
# github repository create
```bash
git init
git add .
git commit -m "🎉 First commit"
gh repo create ryan-portfolio --public --source=. --remote=origin --push

```
# contents/private
네 Ryan, 정확하게 잘 확인하셨어요! 🙌  
지금 겪고 계신 상황은 **Hugo의 기본 동작 방식과 `hugo server -D` 옵션의 역할**, 그리고 **`content/private/` 폴더를 숨기는 올바른 방법**을 구분할 필요가 있어요.

---

## ✅ 1. `hugo server -D`는 어떤 기능인가?

```bash
hugo server -D
```

- `-D`는 `--buildDrafts`의 줄임말입니다
- **`draft: true`**인 문서도 서버에 포함해서 보여줍니다

📌 즉:
- `my-private-in-posts.md` → `draft: true` → `-D` 옵션 덕분에 보임 (정상 동작)
- `my-secret.md` → `draft: false`, 위치가 `content/private/` → **별도 예외 처리 없으면 노출됨**

---

## ✅ 2. `/content/private/` 문서가 보이는 이유

`Hugo`는 **`content/` 디렉토리 아래 있는 모든 `.md` 파일**을 기본적으로 라우팅합니다.  
`content/private/`도 예외가 아닙니다.

> 즉, **경로가 "private"이라고 해서 자동으로 숨겨지지 않습니다.**

---

## ✅ 해결책: `ignoreFiles` 설정을 사용해야 함

📄 `config.toml`에 아래를 추가해 주세요:

```toml
ignoreFiles = ["^content/private/"]
```

이렇게 하면 Hugo는 `content/private/` 경로의 파일을 **빌드 및 서버 렌더링 대상에서 완전히 제외**합니다.

---

## ✅ 적용 후 동작

| 항목 | `draft: true` | `ignoreFiles` 사용 |
|------|---------------|---------------------|
| `hugo` 기본 | 숨김 | 포함됨 |
| `hugo -D` | 포함됨 | 포함됨 |
| `hugo` + `ignoreFiles` | 숨김 | ❌ 완전히 제외됨 |
| `hugo server -D` + `ignoreFiles` | 포함되지 않음 (❗ 기대한 대로!) |

---

## ✅ 추가로 추천되는 구조

```
content/
├── posts/
│   └── my-public.md
├── private/
│   └── my-secret.md  ← ignoreFiles 처리됨
```

---

## 🧪 확인 방법

1. `config.toml`에 `ignoreFiles` 추가
2. `hugo server -D` 실행
3. `/private/`, `/private/my-secret/` 접속 시 **404** 나야 정상

---

## ✅ 결론

> ❗ `content/private/`에 저장한다고 자동으로 숨겨지지 않음  
> ✅ 반드시 `config.toml`에서 `ignoreFiles`로 빌드 제외 처리해야 함!

---

원하시면 draft + private 두 조건을 조합해서 "임시 숨김"과 "완전 비공개" 전략도 짜드릴게요 😊  
지금 `ignoreFiles` 적용하고 `hugo server -D`로 다시 테스트해보시겠어요?

# hugo 명령어 리스트

## ✨ 요약

| 목적 | 명령어 |
|------|--------|
| 빠른 개발용 | `hugo server -D` |
| GitHub Pages 결과와 같게 보기 | ✅ `hugo server --disableFastRender --buildDrafts=false` |
| 최종 배포용 정적 파일 생성 | `hugo --minify` |


---
# hugo 명령어 : GitHub Pages와 동일하게 보려면

아주 정확한 질문이에요 Ryan! 💯  
**GitHub Pages에 배포될 최종 사이트 결과물과 완전히 동일하게 보이도록 로컬에서 Hugo를 실행**하려면, 아래 옵션을 정확히 알고 써야 해요.

---

## ✅ 정답: 실제 GitHub Pages와 동일하게 보려면

```bash
hugo server --disableFastRender
```

또는 더 명확하게:

```bash
hugo server --disableFastRender --buildDrafts=false --buildFuture=false --buildExpired=false
```

### 📌 이 명령은 다음을 의미해요:

| 옵션 | 설명 |
|------|------|
| `--disableFastRender` | 일부 콘텐츠 캐시 렌더링 막고, 정확히 빌드 |
| `--buildDrafts=false` | `draft: true` 문서 제외 (기본값이지만 명시적으로 사용 권장) |
| `--buildFuture=false` | 미래 날짜 문서 제외 |
| `--buildExpired=false` | 만료된 문서 제외 |

✅ 이 옵션들은 GitHub Actions에서 `hugo` 빌드 시와 **거의 동일한 환경을 로컬에서 재현**합니다.

---

## 🔄 GitHub Actions가 사용하는 빌드 명령은?

```yaml
run: hugo --minify
```

이거예요! 즉,

```bash
hugo --minify
```

으로 정적 파일을 생성해도 실제 결과를 확인할 수 있지만 **로컬 미리보기는 `hugo server`로 해야 하므로**, 아래처럼 실행하는 것이 가장 정확합니다:

---

## ✅ 추천 명령어 (Ryan 전용)

```bash
hugo server --disableFastRender --buildDrafts=false --minify
```

- `--buildDrafts=false`: `draft: true` 문서 안 보임
- `--disableFastRender`: 캐시로 인한 화면 누락 방지
- `--minify`: GitHub Pages와 렌더 결과 일치

---

## ✨ 요약

| 목적 | 명령어 |
|------|--------|
| 빠른 개발용 | `hugo server -D` |
| GitHub Pages 결과와 같게 보기 | ✅ `hugo server --disableFastRender --buildDrafts=false` |
| 최종 배포용 정적 파일 생성 | `hugo --minify` |

---

원하시면 이 명령어를 `npm run preview` 같은 커맨드로 스크립트화해드릴 수도 있어요 😄  
지금 로컬에서 이 명령 실행해보시고, 결과가 GitHub Pages와 같게 나오는지 확인해보세요!

