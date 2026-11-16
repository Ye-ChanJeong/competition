# 기상·기후 환경분야 빅데이터 경진대회 플랫폼

부경대학교 **극한기후연구실(Climate Extremes Research Laboratory, CERL)** 에서 주최 및 운영하는 빅데이터 경진대회의 웹 플랫폼입니다.  
참가자의 예측 데이터(`NetCDF`, `.nc`) 제출, 참가 자격 검증, 채점(MAE 평가) 및 실시간 리더보드/통계 시각화를 제공합니다.

---

## 📌 주요 기능

1. **Leaderboard (리더보드)**
   - 참가 팀들의 최고 점수(Best Score), 최근 제출 점수, 제출 일자 및 총 제출 횟수 집계
   - `streamlit-aggrid`를 활용하여 상위 순위(1~5위) 강조 스타일 및 반응형 레이아웃 제공
   - 점수가 낮을수록 우수한 MAE 기준 정렬

2. **Statistics (통계 및 추이 분석)**
   - 팀별/일자별 예측 오차(MAE) 추이 인터랙티브 라인 차트 (`Altair`)
   - 날짜 슬라이더 및 다중 팀 선택 필터
   - Linear / Log 스케일 변환 및 y축 반전 지원

3. **Submit (답안 제출 및 참가자 검증)**
   - `pred_Y_팀명.nc` 파일 포맷 검증
   - 참가자 학번/성명 입력을 통한 Supabase DB 내 팀 및 참가 자격 실시간 검증
   - 한글 팀명을 영문 스토리지 호환명으로 매핑 처리
   - 당일 마감 기준(오후 10시)에 따른 날짜별 디렉토리 자동 분기 업로드

4. **Review (관리자 채점 시스템)**
   - 관리자 인증 후 제출 파일 및 정답 파일 자동 다운로드
   - `xarray` 기반 NetCDF 구조/차원/좌표 검증 (`time`, `lat`, `lon`, `Vcmax25`)
   - 평가 대상 기간(2016~2019년 홀수 달) 및 유효 격자 마스킹 필터링
   - MAE(Mean Absolute Error) 계산 및 999 패널티 처리
   - Supabase RPC(`upsert_leaderboard`) 및 통계 테이블 자동 갱신

5. **Rules & Info (대회 규정 및 안내)**
   - 대회 규칙, 제출 규정, 최종 결과 검증 절차 및 운영진/연구실 정보 안내

---

## 🛠 기술 스택

- **Frontend & Web Framework**: [Streamlit](https://streamlit.io/)
- **Data & Scientific Processing**: `xarray`, `netCDF4`, `h5netcdf`, `h5py`, `pandas`, `numpy`
- **Visualization**: `Altair`, `streamlit-aggrid`
- **Backend & Storage**: [Supabase](https://supabase.com/) (PostgreSQL Database, Storage, RPC Functions)
- **Deployment & Config**: `st-supabase-connection`, Python 3.10+

---

## 📂 디렉토리 구조

```plaintext
pknu-climate-competition/
├── app.py                # Streamlit 메인 진입점 (UI 레이아웃 및 탭 라우팅)
├── submit.py             # 참가자 인증 및 NetCDF 파일 업로드 모듈
├── review.py             # 관리자 채점, NetCDF 검증 및 MAE 계산 모듈
├── requirements.txt      # 프로젝트 의존성 라이브러리 목록
├── public/               # 로고 및 정적 이미지 에셋
└── .streamlit/
    └── config.toml       # 웹사이트 테마 설정
```

---

## 🚀 시작하기

### 1. 환경 설정 및 의존성 설치

가상환경을 생성하고 필요한 패키지를 설치합니다:

```bash
# 가상환경 생성 (선택 사항)
python -m venv .venv

# 가상환경 활성화 (Windows PowerShell 기준)
.venv\Scripts\Activate.ps1

# 패키지 설치
pip install -r requirements.txt
```

### 2. Secrets 설정

`.streamlit/secrets.toml` 파일을 생성하거나 수정하여 필수 환경변수를 설정합니다:

```toml
ADMIN_PASSWORD = "your_admin_password"

[connections.supabase]
SUPABASE_URL = "https://<your-project>.supabase.co"
SUPABASE_KEY = "your_supabase_service_role_key"
```

### 3. 애플리케이션 실행

```bash
streamlit run app.py
```

브라우저에서 `http://localhost:8501`로 접속하여 확인할 수 있습니다.

---

## 📊 데이터베이스 테이블 구조 (Supabase)

- `team_member`: 참가자 학번, 이름, 소속 팀 매핑 테이블
- `team_name_lookup`: 한글 팀명 ↔ Storage 안전 영문 팀명 매핑 테이블
- `leaderboard`: 팀별 최고 점수, 최근 점수, 제출 횟수 저장 테이블 (RPC를 통해 갱신)
- `statistics`: 일자별 채점 결과 기록 테이블 (시계열 차트 렌더링용)
- Storage Bucket (`pknu_climate_big_data_contest_2026`):
  - `answer/`: 평가용 정답 NetCDF 파일
  - `submissions/MM-DD/`: 참가자들이 제출한 NetCDF 파일

---

## 📄 라이선스 및 문의

- **주관**: 부경대학교 기후극한연구실 (Climate Extremes Research Laboratory)
- **홈페이지**: [https://sites.google.com/view/cerl](https://sites.google.com/view/cerl)
- **프로젝트 운영 관련 문의**: ycj1219@pukyong.ac.kr
- **웹 플랫폼 유지·보수 관련 문의**: jihojeong.dev@gmail.com
- **저작권**: © 2026. CERL. All rights reserved.
