# 阶段一：使用 NVIDIA CUDA 11.6 的开发镜像作为基础
# 包含完整的 CUDA Toolkit 和 cuDNN
FROM nvidia/cuda:11.6.2-cudnn8-devel-ubuntu20.04

# 设置工作目录
WORKDIR /workspace

# 安装系统依赖（wget, git, 等）
# 使用腾讯云镜像源加速下载（如果在国内环境）
RUN sed -i 's/archive.ubuntu.com/mirrors.cloud.tencent.com/g' /etc/apt/sources.list && \
    apt-get update && apt-get install -y --no-install-recommends \
    wget \
    git \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# 下载并安装 Miniconda
# 使用清华镜像源加速下载
RUN wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py38_23.10.0-1-Linux-x86_64.sh -O miniconda.sh && \
    bash miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh

# 配置 Conda 环境变量
ENV PATH="/opt/conda/bin:$PATH"

# 复制项目的 environment.yml 文件到容器中
# 确保你的项目根目录下有这个文件
COPY ../environment.yml .

# 使用 Conda 创建环境
# 这会自动处理 Python 版本和所有依赖（包括 PyTorch）
RUN conda env create -f environment.yml

# 初始化 Conda，确保在 bash 中能使用 conda 命令
RUN conda init bash

# 将 Conda 环境激活命令写入 .bashrc
# 这样每次进入容器终端时都会自动激活环境
RUN echo "conda activate gaussian_splatting" >> /root/.bashrc

# 验证步骤：确保环境创建成功并配置正确
RUN echo "=== 验证 Conda 环境 ===" && \
    conda info --envs && \
    echo "=== 验证 .bashrc 配置 ===" && \
    grep "conda activate" /root/.bashrc && \
    echo "=== 验证 CUDA 版本 ===" && \
    nvcc --version
