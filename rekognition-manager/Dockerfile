FROM python:3.13-slim AS builder

WORKDIR /app

RUN apt-get update && apt-get install -y git && \
    git clone --depth 1 https://github.com/JaredaTaylor/amazon-rekognition-face-collection-manager .

FROM python:3.13-slim

# 作業ディレクトリを設定
WORKDIR /app

# ソースコードをコピー
COPY --from=builder /app /app

# バケットとコレクションIDを環境変数にする
RUN sed -i \
    's/rekognition.bucket_name = .*/rekognition.bucket_name = os.environ.get("BUCKET_NAME")/' \
    rekognition_manager/faces/views.py && \
    sed -i \
    's/rekognition.collection_id = .*/rekognition.collection_id = os.environ.get("COLLECTION_ID")/' \
    rekognition_manager/faces/views.py && \
    sed -i '1i import os' rekognition_manager/faces/views.py

# imgタグの行を削除する
RUN grep -v "<img" rekognition_manager/faces/templates/faces/list_faces.html > /tmp/list_faces_filtered.html \
    && mv /tmp/list_faces_filtered.html rekognition_manager/faces/templates/faces/list_faces.html

# 必要なパッケージをインストール
RUN pip install boto3 django Pillow

# エントリーポイント用のシェルスクリプトを作成
RUN echo "#!/bin/sh" > /app/entrypoint.sh && \
    echo "PORT=\${PORT:-8000}" >> /app/entrypoint.sh && \
    echo "python rekognition_manager/manage.py migrate" >> /app/entrypoint.sh && \
    echo "exec python rekognition_manager/manage.py runserver 0.0.0.0:\$PORT" >> /app/entrypoint.sh && \
    chmod +x /app/entrypoint.sh

# エントリーポイントを設定
CMD ["/app/entrypoint.sh"]
