FROM python:3.9

ENV TARGET_DIR /opt/katib
ENV SUGGESTION_DIR cmd/suggestion/hyperopt/v1beta1
ENV GRPC_HEALTH_PROBE_VERSION v0.3.1

RUN if [ "$(uname -m)" = "ppc64le" ] || [ "$(uname -m)" = "aarch64" ]; then \
    apt-get -y update && \
    apt-get -y install gfortran libopenblas-dev liblapack-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*; \
    fi

RUN if [ "$(uname -m)" = "ppc64le" ]; then \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-ppc64le; \
    elif [ "$(uname -m)" = "aarch64" ]; then \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-arm64; \
    else \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64; \
    fi && \
    chmod +x /bin/grpc_health_probe

ADD ./pkg/ ${TARGET_DIR}/pkg/
ADD ./${SUGGESTION_DIR}/ ${TARGET_DIR}/${SUGGESTION_DIR}/
WORKDIR  ${TARGET_DIR}/${SUGGESTION_DIR}
RUN pip install --no-cache-dir -r requirements.txt

RUN chgrp -R 0 ${TARGET_DIR} \
  && chmod -R g+rwX ${TARGET_DIR}

ENV PYTHONPATH ${TARGET_DIR}:${TARGET_DIR}/pkg/apis/manager/v1beta1/python:${TARGET_DIR}/pkg/apis/manager/health/python

ENTRYPOINT ["python", "main.py"]

