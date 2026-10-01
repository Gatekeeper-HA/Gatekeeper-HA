ARG BUILD_FROM
FROM $BUILD_FROM

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    NNPACK_DISABLE=1

# Debian bookworm's python3 is 3.11, which requirements.lock targets.
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 \
    python3-venv \
    ffmpeg \
    libsndfile1 \
    libgomp1 \
    && rm -rf /var/lib/apt/lists/*

RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:${PATH}"

# Gatekeeper ref (release tag, branch or commit) to install from
# github.com/Gatekeeper-HA/Gatekeeper. Set in build.yaml. Declared here, not at
# the top, so changing it doesn't invalidate the apt and venv layers.
ARG GATEKEEPER_REF=main

# Pinned dependencies first, then the package itself.
RUN pip install -r "https://raw.githubusercontent.com/Gatekeeper-HA/Gatekeeper/${GATEKEEPER_REF}/requirements.lock" \
    && pip install --no-deps "https://github.com/Gatekeeper-HA/Gatekeeper/archive/${GATEKEEPER_REF}.tar.gz"

COPY rootfs /
RUN chmod a+x /etc/s6-overlay/s6-rc.d/gatekeeper/run
