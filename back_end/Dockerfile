# back_end/Dockerfile
FROM python:3.12-slim

# Python 기본 설정
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

WORKDIR /app

# 최소 빌드 도구
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential curl \
 && rm -rf /var/lib/apt/lists/*

# 의존성 설치 (CPU 전용 torch를 먼저 고정 설치)
COPY requirements.txt ./requirements.txt
RUN python -m pip install --upgrade pip \
 && pip install --no-cache-dir torch==2.5.1 --index-url https://download.pytorch.org/whl/cpu \
 && pip install --no-cache-dir -r requirements.txt

# 앱 소스 복사
# - app 코드
# - (중요) NLP 리소스(모델 파일)도 이미지에 포함
COPY app/ /app/app/
# 프로젝트 루트에 nlp 디렉터리가 있고 코드에서 app/nlp 경로를 보니
# app 내부에 nlp를 둔 레이아웃을 권장. 이미 app/nlp라면 아래 줄은 불필요.
# COPY nlp/ /app/app/nlp/

# (선택) HF 캐시 폴더 지정하고 싶으면
# ENV TRANSFORMERS_CACHE=/app/.cache/hf

# FastAPI 실행
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]