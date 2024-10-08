FROM paddlepaddle/paddle:latest-dev-cuda11.6-cudnn8.4-trt8.4-gcc82

RUN echo "deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse" >> /etc/apt/sources.list

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y -o Acquire::Retries=10 --no-install-recommends \
    g++ gcc cmake wget xz-utils git make zlib1g-dev openssl libssl-dev libffi-dev libeigen3-dev sqlite3 libsqlite3-dev libx11-dev libgl1-mesa-dev bzip2 libbz2-dev liblzma-dev \
    # pymesh dependency
    libeigen3-dev libgmp-dev libgmpxx4ldbl libmpfr-dev libboost-dev libboost-thread-dev libtbb-dev && \
    apt-get clean autoclean && \
    rm -rf /var/lib/apt/lists/*

RUN wget https://mirrors.huaweicloud.com/python/3.9.7/Python-3.9.7.tgz && \
    tar -xf Python-3.9.7.tgz && \
    cd Python-3.9.7 && \
    ./configure --with-ssl && \
    make -j -notest && \
    make install && \
    cd .. && rm -rf Python-3.9.7 && rm Python-3.9.7.tgz && \
    ln -s /usr/local/bin/python3.9 /usr/local/bin/python

RUN python3 -m pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple && pip install pysdf open3d db-sqlite3 -i https://pypi.tuna.tsinghua.edu.cn/simple

# install pymesh and paddle
RUN python3 -m pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple && pip install "numpy>=1.20.0,<=1.23.1" -i https://pypi.tuna.tsinghua.edu.cn/simple
COPY ./pymesh.tar.xz /src/pymesh.tar.xz

RUN cd /src && \
    ls /src && \
    tar -xvf pymesh.tar.xz && \
    cd pymesh && \
    python3 -m pip install -r python/requirements.txt && \
    python3 setup.py install --user && \
    rm -rf /src

ENV PATH="$PATH:/usr/lib/x86_64-linux-gnu/"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64/:$LD_LIBRARY_PATH"

RUN python3 -m ensurepip --upgrade && \
    pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple && \
    # ensure paddle install normally
    python3 -m pip install paddlepaddle-gpu==0.0.0.post116 -f https://www.paddlepaddle.org.cn/whl/linux/gpu/develop.html

RUN apt-get update
RUN apt-get install -y -o Acquire::Retries=10 --no-install-recommends openssh-server

RUN echo 'ldconfig' >> /root/.bashrc
