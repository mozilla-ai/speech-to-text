FROM python:3.12-slim

ENV HOME=/home/user
ENV PATH=$HOME/.local/bin:$PATH

RUN apt-get update &&  \
    apt-get install -y  git &&  \
    apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN git clone https://github.com/speaches-ai/speaches.git .

RUN pip install --upgrade pip && \
    pip install uv

RUN uv pip install ".[ui]" --system

EXPOSE 8000

CMD ["uvicorn", "--factory", "--host", "0.0.0.0", "--port", "8000", "speaches.main:create_app"]
