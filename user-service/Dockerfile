# Python 3.10 베이스 이미지 사용
FROM python:3.10-slim

# 작업 디렉토리 설정
WORKDIR /app

# FastAPI와 Uvicorn 설치
RUN pip install fastapi uvicorn

# 앱 복사
COPY app /app

# 서버 실행 명령
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
