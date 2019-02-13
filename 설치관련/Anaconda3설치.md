# 목차

- [Anaconda3 설치](#user-content-anaconda3-설치)

# Anaconda3 설치

```shell
# 설치 파일 다운로드
~$ wget https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh

# 실행 권한 추가
~$ chmod +x Anaconda3-5.2.0-Linux-x86_64.sh

# 설치
~$ ./Anaconda3-5.2.0-Linux-x86_64.sh

# 가상환경 생성
~$ anaconda3/bin/conda create -n elasticsearch_control python=3.5 anaconda

# 디렉토리 이동
~$ cd anaconda3/envs/elasticsearch_control

# source 디렉토리 생성
elasticsearch_control$ mkdir script
```
