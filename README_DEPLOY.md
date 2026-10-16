# IEUM Aesthetics & Cosmeceutical Monthly — 웹 아카이브 + 이메일 발송 구축 가이드
대상 도메인: **monthly.ieumvc.com** · 번들 생성일 2026-10-16

## 0. 이 번들에 들어 있는 것
```
site/
├─ index.html            랜딩 (최신호 · 구독 폼 자리 · 아카이브 · 소개) — EN/KR 토글
├─ 2026-10/index.html    제2호 전문 (EN/KR 토글, OG 태그, PDF 링크)
├─ 2026-10/*.pdf         PDF EN · KO
├─ email/2026-10.html    스티비/beehiiv용 이메일 본문 (테이블 기반, 요약 + 전문 보기 버튼)
├─ privacy.html          개인정보처리방침 (뉴스레터용)
├─ og-cover.png          링크드인/카톡 공유 썸네일 (1200×630)
├─ sitemap.xml · robots.txt · CNAME · .nojekyll
```
매달 추가되는 것: `/YYYY-MM/` 폴더 하나 + `index.html` 아카이브 표에 한 줄 + `sitemap.xml`에 한 줄.

## 1. 호스팅 — GitHub Pages (추천, 무료, HTTPS 자동)
1. github.com → New repository → 이름 `ieumvc-monthly`, Public (Private은 유료 플랜에서만 Pages 가능).
2. 이 `site/` 폴더의 **내용물**을 저장소 루트에 올린다 (폴더째가 아니라 안의 파일들). 웹 업로드: "Add file → Upload files"에 드래그. `.nojekyll`과 `CNAME`도 반드시 포함.
3. Settings → Pages → Build and deployment: Source = **Deploy from a branch**, Branch = `main` / `(root)` → Save.
4. 같은 화면 Custom domain에 `monthly.ieumvc.com` 입력 → Save → DNS 설정 후 "Enforce HTTPS" 체크.
5. 1~10분 뒤 `https://monthly.ieumvc.com` 확인.

대안: Cloudflare Pages (Workers & Pages → Create → Upload assets → `site/` 드래그 → Custom domains에 `monthly.ieumvc.com`). ieumvc.com DNS가 이미 Cloudflare에 있으면 이쪽이 클릭 두 번으로 끝난다.

## 2. DNS — CNAME 한 줄
ieumvc.com을 관리하는 곳에서 레코드 추가:
| 유형 | 호스트(이름) | 값 | TTL |
|---|---|---|---|
| CNAME | `monthly` | `<GitHub계정>.github.io` (GitHub Pages) 또는 Pages가 알려주는 `*.pages.dev` (Cloudflare) | 자동/3600 |

- **가비아**: My가비아 → 도메인 관리 → DNS 관리 → 레코드 추가 (호스트 `monthly`, 타입 CNAME, 값에 끝의 점 없이 입력).
- **카페24**: 나의 서비스 관리 → 도메인 관리 → DNS 관리 → CNAME 추가.
- **Cloudflare**: DNS → Add record → CNAME, Proxy status는 GitHub Pages면 **DNS only(회색)**, Cloudflare Pages면 Proxied 가능.
전파 후 GitHub Pages 설정 화면에서 "DNS check successful"이 뜨면 Enforce HTTPS 체크.

## 3. 매달 발행 루틴 (Master Prompt 9단계에 추가할 한 줄)
1. 새 호 산출물(HTML + PDF 2종)을 `/YYYY-MM/`에 넣고 HTML 파일명을 `index.html`로.
2. `index.html`(랜딩)의 "최신호" 블록 5줄과 아카이브 표를 갱신, `sitemap.xml`에 URL 추가.
3. 저장소에 커밋(웹 업로드로 덮어쓰기 가능) → 1~2분 후 반영.
4. `email/YYYY-MM.html`을 발송 도구에 붙여 넣고 테스트 발송 → 발송.
→ 3번까지는 Master Prompt에 "Stage 9.5 Web bundle: write `/site/YYYY-MM/`, update landing + sitemap, output `email/YYYY-MM.html`"로 넣으면 자동 생성된다.

## 4. 이메일 발송 도구
### 스티비 (국내 고객 중심이면 추천)
- 가입 → 주소록 생성 → **구독 폼** 만들기 → "임베드 코드" 복사 → `index.html`의 `<!-- SUBSCRIBE-FORM -->` 자리에 붙여넣기.
- 발신자 인증: 발신 이메일 `jayjay@ieumvc.com` 인증 + ieumvc.com DNS에 스티비가 주는 **SPF/DKIM** 레코드 추가(스팸함 방지 필수).
- 이메일 만들기 → "HTML 직접 입력" → `email/2026-10.html` 붙여넣기 → 수신거부 링크는 스티비 치환 태그로 교체(템플릿의 `{{unsubscribe_link}}` 자리).
- 제목 예: `[IEUM Monthly] 제2호 — 갈더마 ZO 15억달러·K-뷰티 수출 111억달러·'재생' 마케팅의 심판`. 서비스 홍보 문구가 포함되면 제목 앞에 `(광고)`.
### beehiiv (해외 리드 비중이 크면)
- Publication 생성 → Settings → **Custom domain**은 웹 아카이브를 beehiiv로 옮길 때만 필요; 지금 구조에서는 Email sending domain만 `ieumvc.com`으로 인증(SPF/DKIM/DMARC).
- Grow → Subscribe forms → Embed 코드 → 같은 자리에 붙여넣기.
- Posts → New → HTML import로 `email/2026-10.html` 넣기; unsubscribe는 beehiiv 머지태그 `{{unsubscribe_url}}`로 교체.
### 둘 다 공통
- 첫 발송은 **기존 명함·거래처 리스트를 임포트하지 말고** 웹 구독 폼 링크를 개별 메일/링크드인으로 보내 동의를 받는 편이 정보통신망법상 깨끗하다 (거래관계 기반 발송도 가능하지만 수신동의 기록이 남는 쪽이 안전).
- 발송 후 1주: 오픈율·클릭률과 "전문 보기" 클릭 수를 다음 호 QA 원장에 기록.

## 5. 리드젠 연결
- 랜딩과 각 호 하단 footer의 `jayjay@ieumvc.com`은 그대로 두고, 구독 폼에 "회사명/직책" 선택 항목을 넣으면 구독자 리스트가 곧 프로스펙트 리스트가 된다.
- 링크드인(IEUMVC 페이지)에는 `https://monthly.ieumvc.com/2026-10/` 링크만 올리면 og-cover.png 썸네일이 붙는다.

## 6. 체크리스트 (오픈 전)
- [ ] `https://monthly.ieumvc.com` 접속, 자물쇠(HTTPS) 확인
- [ ] EN/KR 토글, PDF 2종 다운로드, 구독 폼 제출 테스트
- [ ] privacy.html의 "[발송 도구 확정 후 업체명 기재]" 채우기
- [ ] 발신 도메인 SPF/DKIM 통과 (스티비/beehiiv 대시보드에서 녹색)
- [ ] 테스트 메일을 Gmail·네이버·회사 메일 3곳에서 열어 표시 확인
- [ ] 1호(9월) HTML을 `/2026-09/index.html`로 넣고 아카이브 표 링크 활성화
