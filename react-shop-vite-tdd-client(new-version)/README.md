## 여행 상품 판매 서비스 강의 교안
React 18과 Vite로 여행 상품 판매 서비스를 구현하며 Context API, API 연동, 테스트, 배포까지 실습한다.
백엔드 목업 서버는 Express로 동작하며 프런트는 Vite 개발 서버를 사용한다.
문장마다 줄바꿈하고, 장면 전환에는 --- 구분선을 둔다.

---
### 저장소 위치
- 프런트: `react-shop-vite-tdd-client(new-version)` (현재 파일 위치)
- 서버: `react-shop-server` (포트 4000)

---
### 1. 프로젝트 개요
목표는 상품 조회부터 옵션 선택, 주문 요약, 완료까지 하나의 흐름을 구축하는 것이다.
Context API로 전역 상태를 관리하고 Axios로 REST API를 호출한다.
테스트는 Vitest와 React Testing Library, MSW로 작성한다.

---
### 2. 실행 방법
백엔드 실행  
```
cd react-shop-server
npm install
npm start
```
서버는 기본 포트 4000에서 실행된다.
이미지와 API가 모두 4000을 기준으로 제공된다.

프런트 실행  
```
cd react-shop-vite-tdd-client(new-version)
npm install
npm run dev
```
Vite 기본 포트는 5173이다.
브라우저에서 http://localhost:5173 로 접속한다.

포트 불일치 시 프런트 코드의 `localhost:4000` 부분을 일관되게 수정해야 한다.

---
### 3. 주요 폴더와 파일
`src/main.jsx`  
- ReactDOM 엔트리.
- `#root`에 `<App />`를 마운트한다.

`src/App.jsx`  
- 단계 전환: OrderPage → SummaryPage → CompletePage.
- `OrderContextProvider`로 상태를 감싼다.

`src/contexts/OrderContext.jsx`  
- 상품, 옵션, 합계를 Map 기반으로 관리한다.
- `updateItemCount`로 수량을 갱신하고 subtotal을 계산한다.

`src/pages/OrderPage`  
- `Type.jsx`: orderType별 데이터 로드와 렌더.
- `Products.jsx`: 이미지 + 수량 입력.
- `Options.jsx`: 체크박스 입력.

`src/pages/SummaryPage`  
- 선택한 항목과 총액을 요약해 보여주고 다음 단계로 이동시킨다.

`src/pages/CompletePage`  
- `/order` POST 후 주문 내역 테이블을 표시한다.
- 초기화 버튼으로 첫 단계로 복귀한다.

`src/components/ErrorBanner.jsx`  
- 비동기 오류 시 배너 출력.

`src/mocks/*`  
- MSW 핸들러와 서버 설정.
- 테스트 시 네트워크 요청을 가로챈다.

---
### 4. API 사양 (목업 서버)
베이스 URL: http://localhost:4000

`GET /products`  
- 국가별 상품 리스트와 `imagePath`를 반환한다.

`GET /options`  
- 옵션 이름 목록을 반환한다.

`POST /order`  
- 본문에 totals 정보를 담아 보내면 주문번호를 생성해 누적 리스트를 반환한다.

정적 자원  
- `public/images`에서 이미지 파일을 서빙한다.

---
### 5. 화면 흐름
OrderPage  
- `/products`, `/options`를 불러와 카드와 체크박스를 렌더한다.
- 입력 변경 시 Context 합계가 즉시 갱신된다.
- `주문하기` 클릭 시 SummaryPage로 이동한다.

SummaryPage  
- 선택 상품, 옵션, 총액을 확인한다.
- 확인 후 CompletePage로 넘어간다.

CompletePage  
- 주문을 전송하고 응답받은 주문 내역을 표로 보여준다.
- “첫페이지로”로 상태를 리셋하고 초기 단계로 돌아간다.

---
### 6. 테스트 가이드
사전 설치  
```
cd react-shop-vite-tdd-client(new-version)
npm install
```

실행  
```
npm run test       # 단발
npm run test:watch # 워치
npm run test:ui    # Vitest UI
```

주요 테스트 포인트  
- 상품 이미지 렌더링, 옵션 체크박스 렌더링 (`Type.test.jsx`).
- 합계 계산과 에러 배너 노출.
- 요약, 완료 단계 흐름 (`SummaryPage.test.jsx`).

MSW 설정  
- `src/mocks/handlers.js`에 엔드포인트 응답 정의.
- `src/setupTests.js`에서 서버 lifecycle 관리.

---
### 7. 트러블슈팅 체크리스트
빈 화면 또는 데이터 미표시  
- 백엔드가 4000 포트에서 실행 중인지 확인한다.
- Network 탭에서 `/products`, `/options` 응답 코드가 200인지 확인한다.
- 프런트가 `http://localhost:5173`로 접속 중인지 확인한다.

이미지 미표시  
- 이미지 URL이 `http://localhost:4000/<imagePath>`로 로드되는지 확인한다.
- `public/images` 경로에 파일이 존재하는지 확인한다.

CORS 오류  
- 서버 CORS 설정에 사용하는 프런트 포트(5173 또는 3000)가 포함되어 있는지 확인한다.

포트 변경 시  
- 백엔드 포트와 프런트 코드 내 axios 및 이미지 요청 베이스 URL을 모두 동일하게 맞춘다.
- MSW 핸들러의 URL도 함께 수정한다.

---
### 8. 빌드와 배포 메모
로컬 빌드  
```
npm run build
```
산출물은 `dist/`에 생성된다.

정적 호스팅  
- S3와 같은 정적 호스팅 서비스에 `dist/`를 업로드한다.
- API가 별도 도메인일 경우 베이스 URL을 환경 변수로 분리하는 것을 권장한다.

---
### 9. 확장 과제
필터 상태를 URL 쿼리로 동기화해 공유 가능한 링크 만들기.  
최근 주문 내역을 로컬스토리지에 저장해 재방문 시 복원하기.  
접근성 개선: 키보드 내비게이션, ARIA 레맨더, 대비 점검.  
이미지 지연 로딩과 코드 스플리팅으로 초기 로드 최적화.  
CI 파이프라인에 테스트-빌드-배포 자동화를 추가하기.

