# GonggoBoda Blog (Hugo + GitHub Pages)

매일 자동 발행되는 정적 블로그. Hermes 가 콘텐츠 큐레이션 → GitHub Content API 로 push → GitHub Actions 가 Hugo 빌드 + Pages 배포.

## 사용자 1회 셋업 (약 15분)

### 1. GitHub 에 새 저장소 만들기 (5분)

1. https://github.com/new
2. 저장소 이름: **`gonggoboda-blog`** (또는 임의)
3. **Public** 선택 (Private 이면 Pages 가 유료 플랜 필요)
4. README 체크 (필수 아님)
5. 저장소 생성

### 2. 본 디렉터리 (`site/`) 내용을 새 저장소에 푸시 (5분)

방법 A — 명령줄:
```bash
cd ~/ai-agent-lab/site
git init -b main
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git add .
git commit -m "Initial Hugo site"
git remote add origin git@github.com:<본인_username>/gonggoboda-blog.git
git push -u origin main
```

방법 B — GitHub 웹 UI 로 파일 직접 업로드 (PaperMod 테마는 별도 submodule 추가 필요)

### 3. GitHub Pages 활성화

저장소 → Settings → Pages →
- Source: **GitHub Actions** 선택
- 첫 push 후 GitHub Actions 가 자동 빌드 + 배포 (1~2분)

→ 사이트 주소: `https://<본인_username>.github.io/gonggoboda-blog/` (또는 본인 도메인)

### 4. Personal Access Token (PAT) 발급 (Hermes 자동 push 용) (3분)

1. https://github.com/settings/tokens?type=beta — **Fine-grained tokens** 권장
2. **Generate new token**:
   - Token name: `gonggoboda-hermes-push`
   - Repository access: **Only select repositories** → `gonggoboda-blog`
   - Permissions → Repository permissions:
     - **Contents**: Read and write
     - **Metadata**: Read-only (자동 포함)
3. 발급 후 토큰 값 복사 (페이지 닫으면 다시 못 봄)

### 5. `.env` 에 추가

```bash
# state/hermes/.env 에 추가
GITHUB_TOKEN=ghp_xxxxxxxxxxxx
GITHUB_REPO=<본인_username>/gonggoboda-blog
GITHUB_BRANCH=main
# blog repo 의 루트가 Hugo source 이면 비워두기. 하위 폴더면 그 폴더명
HUGO_SITE_DIR=
```

### 6. (선택) hugo.toml 의 baseURL 본인 username 으로 수정

`site/hugo.toml`:
```toml
baseURL = "https://<본인_username>.github.io/gonggoboda-blog/"
```

수정 후 커밋 + push.

### 7. (선택) 네이버 서치어드바이저 + Google Search Console 등록

- 네이버: https://searchadvisor.naver.com/
- Google: https://search.google.com/search-console
- 메타태그 인증 OR HTML 파일 인증
- 사이트맵 제출: `https://<도메인>/sitemap.xml`
- 한국어 SEO 효과 약하지만 0 보다는 나음

## 운영 (사용자 셋업 후)

Hermes daily_pipeline.sh 가 매일 08:00 KST:
1. 3 소스 수집 + 정규화 + 큐레이션 (기존)
2. ✨ **신규**: curate 결과 → Hugo post markdown 생성 → GitHub API 로 자동 push
3. GitHub Actions 가 자동 빌드 + Pages 배포 (1~2분)

사용자 운영 시간: **0 분/일**.

## 로컬 테스트

GitHub push 안 하고 markdown 만 생성:
```bash
python3 scripts/publish/hugo_post.py --no-push
```

## 폴더 구조

```
site/
├── hugo.toml                              # 사이트 설정
├── content/
│   ├── _index.md                          # 홈
│   ├── about.md                           # 소개
│   └── posts/
│       └── YYYY-MM-DD-daily.md            # 일일 자동 발행
├── .github/workflows/hugo.yml             # 자동 빌드/배포
├── .gitmodules                            # PaperMod 테마 submodule
└── themes/PaperMod/                       # (push 후 submodule 로 추가)
```
