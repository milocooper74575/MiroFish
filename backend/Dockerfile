# ===========================================
# MiroFish Backend - 阿里云 FC 自定义容器镜像
# ===========================================

FROM python:3.11-slim

WORKDIR /code

# 配置 pip 增加超时时间
RUN pip config set global.timeout 600

# 先复制依赖文件
COPY backend/requirements.txt ./

# 安装 torch（CPU 版本）
RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cpu || \
    pip install --no-cache-dir torch

# 分批安装依赖，避免单次下载过大文件
# 第一批：核心框架
RUN pip install --no-cache-dir --default-timeout=600 \
    flask>=3.0.0 \
    flask-cors>=6.0.0 \
    openai>=1.0.0 \
    python-dotenv>=1.0.0 \
    pydantic>=2.0.0

# 第二批：文件处理
RUN pip install --no-cache-dir --default-timeout=600 \
    PyMuPDF>=1.24.0 \
    charset-normalizer>=3.0.0 \
    chardet>=5.0.0

# 第三批：Zep Cloud
RUN pip install --no-cache-dir --default-timeout=600 \
    zep-cloud==3.13.0

# 第四批：Camel 相关（包含 torch 依赖）
RUN pip install --no-cache-dir --default-timeout=600 \
    camel-ai==0.2.78 \
    sentence-transformers \
    huggingface-hub \
    accelerate

# 第五批：camel-oasis
RUN pip install --no-cache-dir --default-timeout=600 camel-oasis==0.2.5 || \
    echo "camel-oasis will be installed separately"

# 复制后端应用代码
COPY backend/run.py ./
COPY backend/app/ ./app/
COPY backend/.env ./

# 设置环境变量
ENV PYTHONIOENCODING=utf-8
ENV FLASK_HOST=0.0.0.0
ENV FLASK_PORT=5001
ENV PYTHONUNBUFFERED=1
ENV PYTHONPATH=/code

EXPOSE 5001

CMD ["python", "run.py"]
