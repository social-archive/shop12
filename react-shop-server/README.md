## 여행 상품 목업 서버 교안
Express 기반의 목업 서버로 프런트엔드가 사용할 상품·옵션·주문 API를 제공합니다.
정적 이미지도 함께 서빙합니다.

---
### 실행
```
cd react-shop-server
npm install
npm start
```
기본 포트: 4000  
접속 예: http://localhost:4000/products

---
### 주요 엔드포인트
- `GET /products` : 여행 상품 목록 반환 (이미지 경로 포함)
- `GET /options` : 옵션 목록 반환
- `POST /order` : 주문 합계를 받아 주문번호를 생성하고 누적 내역을 반환

정적 자원  
- `/images/...` 경로로 `public/images` 폴더를 서빙

---
### CORS
허용 origin: `http://localhost:3000`, `http://localhost:5173`  
포트 변경 시 `server.js`의 CORS 설정과 프런트 API 베이스 URL을 함께 수정하세요.

---
### 데이터 소스
- `travel.json`에 국가별 상품과 옵션 정의
- 서버 기동 시 파일을 읽어 메모리에 로드

---
### 트러블슈팅
- 404 `/` : 정상입니다. 실제 사용 경로는 `/products`, `/options`, `/order`입니다.
- CORS 에러 : 허용 origin에 현재 프런트 포트가 포함됐는지 확인
- 이미지 미표시 : 정적 파일 경로(`/public/images`)와 URL(`http://localhost:4000/<path>`) 일치 여부 확인

