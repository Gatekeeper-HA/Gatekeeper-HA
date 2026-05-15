ARG BUILD_FROM
FROM $BUILD_FROM

ENV PYTHONUNBUFFERED=1 \
    NNPACK_DISABLE=1

RUN apt-get update && apt-get install -y --no-install-recommends \
    ffmpeg \
    libsndfile1 \
    libgomp1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt /app/requirements.txt
RUN pip install --no-cache-dir -r /app/requirements.txt

COPY app /app/app
COPY rootfs /
RUN chmod a+x /etc/s6-overlay/s6-rc.d/gatekeeper/run
