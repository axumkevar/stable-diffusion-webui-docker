FROM pytorch/pytorch:2.3.0-cuda12.1-cudnn8-devel

ENV PUTHONDONTWRITEBYCODE 1
ENV PYTHONUNBUFFERED 1

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

RUN apt-get update && apt-get install -y git gcc g++ build-essential
RUN pip install imageio imageio-ffmpeg pyspng numpy ninja
RUN apt-get clean
RUN conda install -c conda-forge gxx_linux-64

ENV ROOT=/stable-diffusion
RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
  cd ${ROOT} && \
  git checkout master && \
  pip install -r requirements.txt

RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/Stability-AI/stable-fast-3d.git ${ROOT}/custom_nodes/stable-fast-3d && \
  cd ${ROOT}/custom_nodes/stable-fast-3d && \
  git checkout master && \
  pip install -r requirements.txt

RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/axumkevar/ComfyUI-stylegan3-editing.git ${ROOT}/custom_nodes/ComfyUI-stylegan3-editing && \
  cd ${ROOT}/custom_nodes/ComfyUI-stylegan3-editing && \
  git checkout master && \
  pip install -r requirements.txt

WORKDIR ${ROOT}
COPY . /docker/
RUN chmod u+x /docker/entrypoint.sh && cp /docker/extra_model_paths.yaml ${ROOT}

ENV NVIDIA_VISIBLE_DEVICES=all PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""
EXPOSE 7860
ENTRYPOINT ["/docker/entrypoint.sh"]
CMD python -u main.py --listen --port 7860 ${CLI_ARGS}
