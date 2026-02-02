FROM pytorch/pytorch:2.8.0-cuda12.8-cudnn9-runtime
ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /opt/CosyVoice

RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
RUN apt-get update -y
RUN apt-get -y install git unzip git-lfs g++
RUN git lfs install
RUN git clone --recursive https://github.com/icradP/CosyVoice.git
# here we use python==3.10 because we cannot find an image which have both python3.8 and torch2.0.1-cu118 installed
RUN cd CosyVoice && pip3 install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/ --trusted-host=mirrors.aliyun.com --no-cache-dir 
# 50系显卡
RUN pip uninstall torch torchvision torchaudio -y
RUN pip install torch==2.7.1 torchvision==0.22.1 torchaudio==2.7.1 --index-url https://download.pytorch.org/whl/cu128
#升级 openai-whisper==20250625
RUN pip install openai-whisper==20250625 -i https://mirrors.aliyun.com/pypi/simple/ --trusted-host=mirrors.aliyun.com --no-cache-dir
RUN cd CosyVoice/runtime/python/grpc && python3 -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. cosyvoice.proto
# 卸载 deepspeed 容器内不需要
RUN pip uninstall deepspeed -y 

ENTRYPOINT [ "sh","-c", "cd CosyVoice/runtime/python/grpc &&  python3 server.py --repo_id iic/CosyVoice2-0.5B --model_dir /models/iic/CosyVoice2-0.5B" ]

