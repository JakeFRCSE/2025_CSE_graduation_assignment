# back_end/Dockerfile
FROM python:3.12-slim

# 기본 설정 (버퍼링 끄기, pyc 생성 억제)
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

WORKDIR /app

# 빌드에 필요한 최소 패키지
# (python-mecab-ko 는 manylinux wheel 이라 보통 추가 의존성 없이 설치됨.
#  그래도 빌드도구는 최소한만 둠)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential curl \
 && rm -rf /var/lib/apt/lists/*

# 의존성 먼저 설치 (레이어 캐시 활용)
COPY requirements.txt ./requirements.txt

# 🔴 핵심: CPU 전용 PyTorch 먼저 고정 설치
#  - 이후 -r requirements.txt 를 설치할 때 torch==2.5.1 요구사항은 이미 충족되어 재설치(=CUDA 빌드)되지 않음
RUN python -m pip install --upgrade pip \
 && pip install --no-cache-dir torch==2.5.1 --index-url https://download.pytorch.org/whl/cpu \
 && pip install --no-cache-dir -r requirements.txt

# 앱 소스 복사
COPY . .

# (선택) Transformers 캐시 디렉터리 지정하고 싶다면:
# ENV TRANSFORMERS_CACHE=/app/.cache/hf

# Uvicorn 단일 프로세스 실행
#  - dev 환경이 아니면 --reload 금지
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]