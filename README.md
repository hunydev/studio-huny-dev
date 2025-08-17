# Huny Studio — apps & experiments

Huny가 만든 작고 재밌는 웹앱/실험을 한 곳에 모아 보여주는 단일 페이지 갤러리이다. 데이터는 GitHub에 두고 Netlify로 정적 배포한다. 로그인은 필요 없으며, 상단에 huny.dev, blog.huny.dev 등 대표 링크를 고정 노출한다.

---

## 데모

* **studio.huny.dev** (배포 후 접속)

## 주요 특징

* 썸네일 기반 갤러리 레이아웃
* 검색(f 키 포커스), 태그 필터, 정렬(추천/업데이트/이름)
* 외부 링크는 새 탭, 내부 경로(`/app/...`)는 동일 도메인에서 열기
* `apps.json`만 갱신하면 동적으로 목록이 반영되며 빌드 과정이 필요하지 않다
* 다크 테마 고정

## 구조

```
repo/
├─ index.html         # 단일 페이지 앱
├─ apps.json          # 갤러리 데이터(선택, 없으면 내장 FALLBACK 사용)
├─ thumbs/            # 썸네일 이미지(선택)
└─ app/               # 가벼운 내부 앱들(선택)
   ├─ nolure/
   ├─ prompt-keeper/
   └─ ...
```

## 데이터 관리: `apps.json`

루트에 `apps.json` 파일을 두면 `index.html`이 이를 불러와 갤러리를 구성한다. 파일이 없을 경우 내장된 FALLBACK 데이터가 사용된다.

### 스키마

```json
{
  "links": [
    { "title": "huny.dev", "url": "https://huny.dev" },
    { "title": "blog.huny.dev", "url": "https://blog.huny.dev" }
  ],
  "apps": [
    {
      "id": "prompt-keeper",
      "title": "Prompt Keeper",
      "description": "AI 프롬프트 저장/검색",
      "url": "/app/prompt-keeper",          
      "thumbnail": "/thumbs/prompt-keeper.webp",
      "tags": ["ai", "productivity"],
      "featured": true,
      "updatedAt": "2025-08-10"
    }
  ]
}
```

### 필드 설명

* **links**: 상단 고정 링크 배열이다. 생략하면 기본값을 사용한다.
* **apps**: 카드로 렌더링할 앱 목록이다.

  * **id**: 고유 식별자이다.
  * **title**: 카드 제목이다.
  * **description**: 카드 설명이다.
  * **url**: 이동 링크이다. `http(s)://`로 시작하면 새 탭에서 열리고, `/app/...` 형태면 내부 경로로 열린다.
  * **thumbnail**: 카드 썸네일 경로이다. 없으면 파비콘/도메인명으로 대체 UI가 표시된다.
  * **tags**: 문자열 배열 태그이다(필터링/검색에 사용된다).
  * **featured**: 추천 배지 및 정렬 가중치에 사용된다.
  * **updatedAt**: `YYYY-MM-DD` 형식의 날짜 문자열이다(정렬에 사용된다).

## 로컬 미리보기

빌드가 필요 없으므로 파일을 직접 열어도 동작한다. 그러나 CORS 문제를 피하려면 간단한 로컬 서버를 쓰는 편이 낫다.

### Python (선택)

```bash
# Python 3.x
python -m http.server 5173
# http://localhost:5173 접속
```

### Node (선택)

```bash
# npx serve 사용 예
npx serve -p 5173 .
```

## 배포(Netlify)

1. GitHub 저장소를 Netlify에 연결한다.
2. **Build command**는 비워두고, **Publish directory**를 저장소 루트로 지정한다.
3. 배포 후 `apps.json`을 수정해 커밋하면 자동으로 갤러리가 갱신된다.

### 캐싱 주의

`apps.json`은 `fetch('/apps.json', { cache: 'no-store' })`로 요청한다. Netlify 기본 설정에서 즉시 반영된다. CDN 캐시 정책을 커스터마이즈하려면 Netlify `_headers` 파일을 사용할 수 있다(선택).

## 썸네일 가이드(선택)

* 비율 16:10 권장(예: 1280×800)
* 포맷은 `webp` 권장
* `thumbs/` 디렉터리에 보관

## 커스터마이징

* 색상: `tailwind.config`에서 `brand` 팔레트를 조정하면 된다.
* 로고: 헤더의 SVG를 교체하면 된다.
* 레이아웃: 그리드 열 수는 `#grid` 클래스의 Tailwind 유틸리티로 제어한다.

## 기여

개인 실험 공간이므로 이슈/PR은 선택적으로 수용한다.

## 라이선스

MIT License

```
MIT License

Copyright (c) 2025 Hun Jang

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 변경 로그

* 2025-08-17: 다크 테마 고정, 테마 토글 제거, FALLBACK 데이터 렌더링 오류 수정
