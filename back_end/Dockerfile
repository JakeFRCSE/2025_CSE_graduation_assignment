FROM python:3.12

ENV TORCH_CUDA_ARCH_LIST=""

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt --break-system-packages

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]