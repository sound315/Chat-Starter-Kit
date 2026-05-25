# LLM Chatbot Starter Kit

Azure OpenAI 기반의 완성형 LLM 채팅 스타터킷입니다.
Streamlit UI + FastAPI 백엔드 + 임베딩 위젯으로 구성됩니다.

---

## 빠른 시작

### 1. 의존성 설치

```bash
# poetry 사용 (권장)
pip install poetry
poetry install

# 또는 pip 직접 설치
pip install -r requirements.txt
```

### 2. 환경 변수 설정

```bash
cp .env.example .env
```

`.env` 파일을 열고 Azure OpenAI 정보를 입력합니다:

```env
LLM_PROVIDER=azure_openai
AZURE_OPENAI_API_KEY=your-api-key
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o
```

### 3. 서버 실행

**Windows:**
```powershell
.\scripts\dev.ps1
```

**Linux/Mac:**
```bash
bash scripts/dev.sh
```

**수동 실행:**
```bash
# 터미널 1 - FastAPI
uvicorn backend.main:app --host 0.0.0.0 --port 8000 --reload

# 터미널 2 - Streamlit
streamlit run streamlit_app/app.py
```

### 4. 접속

| 서비스 | URL |
|--------|-----|
| Streamlit 채팅 UI | http://localhost:8501 |
| FastAPI 문서 | http://localhost:8000/docs |
| 임베딩 채팅 UI | http://localhost:8000/embed |

---

## LLM 프로바이더 전환

`.env` 파일의 `LLM_PROVIDER` 값만 변경하면 됩니다:

```env
# Azure OpenAI (기본값)
LLM_PROVIDER=azure_openai

# OpenAI
LLM_PROVIDER=openai
OPENAI_API_KEY=sk-...

# Anthropic Claude
LLM_PROVIDER=anthropic
ANTHROPIC_API_KEY=sk-ant-...

# Ollama (로컬 모델)
LLM_PROVIDER=ollama
OLLAMA_MODEL=llama3.2
```

---

## 임베딩 위젯 사용법

외부 웹사이트에 챗봇을 삽입하려면 `</body>` 태그 앞에 한 줄 추가:

```html
<script
  src="https://your-server/widget/chatbot.js"
  data-api-url="https://your-api-server"
  data-title="AI 챗봇"
  data-color="#10a37f"
  data-position="bottom-right"
></script>
```

### 위젯 옵션

| 속성 | 기본값 | 설명 |
|------|--------|------|
| `data-api-url` | `http://localhost:8000` | FastAPI 서버 주소 |
| `data-title` | `AI 챗봇` | 위젯 헤더 제목 |
| `data-color` | `#10a37f` | 주 색상 |
| `data-position` | `bottom-right` | 위치 (`bottom-right`, `bottom-left`) |

---

## 프로젝트 구조

```
starter-kit/
├── backend/
│   ├── api/v1/
│   │   ├── chat.py        # 채팅 API 라우터
│   │   ├── embed.py       # 임베딩 UI 서빙
│   │   └── health.py      # 헬스체크
│   ├── core/
│   │   ├── config.py      # 환경 설정 (Pydantic Settings)
│   │   └── dependencies.py # 의존성 주입
│   ├── services/llm/
│   │   ├── base.py                # LLM 추상 인터페이스
│   │   ├── factory.py             # 프로바이더 팩토리
│   │   ├── azure_openai_service.py
│   │   ├── openai_service.py
│   │   ├── anthropic_service.py
│   │   └── ollama_service.py
│   ├── schemas/
│   │   ├── chat.py        # 채팅 DTO
│   │   └── common.py      # 공통 API 응답 DTO
│   ├── repositories/
│   │   └── session_repository.py  # Redis 대화 이력
│   └── main.py            # FastAPI 앱 진입점
├── streamlit_app/
│   ├── app.py             # Streamlit 메인
│   ├── api_client.py      # FastAPI 클라이언트
│   ├── components/
│   │   ├── sidebar.py     # 사이드바 컴포넌트
│   │   └── chat_message.py
│   └── .streamlit/config.toml
├── widget/
│   ├── chatbot.js         # 임베딩 위젯 JS
│   └── embed.html         # 경량 채팅 UI
├── scripts/
│   ├── dev.ps1            # Windows 실행 스크립트
│   └── dev.sh             # Linux/Mac 실행 스크립트
├── .env.example
├── pyproject.toml
├── requirements.txt
└── docker-compose.yml
```

---

## Docker로 실행

```bash
cp .env.example .env
# .env 파일 편집 후

docker-compose up -d
```

---

## API 엔드포인트

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `POST` | `/api/v1/chat/sessions` | 세션 생성 |
| `GET` | `/api/v1/chat/sessions/{id}/messages` | 대화 이력 조회 |
| `DELETE` | `/api/v1/chat/sessions/{id}` | 세션 삭제 |
| `POST` | `/api/v1/chat/completions` | 단일 응답 |
| `POST` | `/api/v1/chat/stream` | SSE 스트리밍 응답 |
| `GET` | `/api/v1/chat/models` | 모델 목록 |
| `GET` | `/api/v1/chat/provider` | 현재 프로바이더 확인 |
| `GET` | `/embed` | 임베딩 채팅 UI |
| `GET` | `/widget/chatbot.js` | 위젯 JS |
