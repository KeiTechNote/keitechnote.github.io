---
layout: single
title: Python 설치하기
date: 2024-02-18 00:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, Python, Anaconda, 파이썬, 가상환경, 주피터 노트북, jupyter notebook]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

파이썬을 설치하는 방법은 크게 파이썬 프로그램을 직접 설치하는 방법과 아나콘다(Anaconda) 프로그램을 설치하는 방법입니다. 
그 중 아나콘다는 빅데이터 분석을 위한 패키지로, 파이썬과 R 언어를 지원합니다. 아나콘다가 갖는 편의성과 빅데이터 분석에 대한 관심으로 최근 파이썬 설치는 아나콘다를 많이 사용합니다. 
파이썬 프로그램은 보통 윈도우 프로그램 설치와 크게 다르지 않으므로 본 Post 에서는 아나콘다 설치 및 기본 활용에 대해 다룹니다. 

### 다운로드

아나콘다 사이트에 접속해 OS 별 설치파일을 다운로드 합니다. 

- 아나콘다 : [다운로드](https://www.anaconda.com/download)

> OS별 설치 방법은 [Anaconda Official Docs](https://docs.anaconda.com/free/anaconda/install/index.html) 를 참고하기 바랍니다.
{: .notice--info}

### 설치하기

보통의 프로그램 설치 방식과 동일하게 설치합니다. 

![install_anaconda](/assets/images/2024-02-18-install-anaconda.png){: .align-center}
<p style="text-align: center;">Anaconda 설치</p>

### 기본 사용법

아나콘다는 많은 기능을 제공하지만 그 중 "가상 환경 설정" 과 "주피터(Jupyter) 노트북" 에 대해 설명합니다.

#### 가상환경 설정

가상 환경은 하나의 PC 에서 다양한 개발환경을 구성하기 위해 파이썬에서 제공하는 기능입니다. 
개발 특성상 다양한 3rd Party 라이브러리를 설치하고, 변경하며, 테스트합니다. 
이때, 라이브러리간 의존성이나 버전 문제로 환경 구성이 용이하지 않을 수 있습니다. 
예를 들어, 개발 프로젝트1은 ZIP 라이브러리 버전 1.0 을 쓰는데 개발 프로젝트2는 ZIP 라이브러리 버전 2.0 을 쓴다고 가정했을 때, 서로 다른 버전을 같은 환경에 설치할 경우, 설치시 또는 실행시 이전에 없던 오류가 발생할 수 있습니다.

이러한 이유로 파이썬은 `venv` 명령어를 통한 개발환경을 분리할 수 있는 기능을 제공했고, 아나콘다도 `conda` 명령어를 통해 동일한 기능을 이용할 수 있습니다. 여기 아나콘다는 conda 를 통해 라이브러리 설치, 삭제와 같은 관리 기능도 제공해 편의성을 높였습니다. 

##### 가상환경 생성

- 명령어 : `conda create -n <가상환경 이름> [python=<버전>]`

##### 가상환경 목록

- 명령어 : `conda env list`

##### 가상환경 실행

- 명령어 : `conda activate <가상환경 이름>`

##### 가상환경 내 라이브러리 목록

- 명령어 : `conda list -n <가상환경 이름>`

##### 가상환경 종료

- 명령어 : `conda deactivate`

##### 가상환경 제거

- 명령어 : `conda remove -n <가상환경 이름>`

#### 주피터 노트북 

주피터 노트북은 웹 브라우저에서 파이썬 코드를 작성하고, 실행할 수 있는 웹 기반 IDE 입니다. 
일정한 코드 블록 단위로 작성해 실행하고 결과를 볼 수 있기 때문에, 처음 파이썬을 접할 때 많이 사용하는 IDE 입니다. 
아나콘다를 설치하면 함께 설치됩니다.

- 명령어 : `jupyter notebook`

터미널에서 명령어를 실행하면 다음과 같은 화면이 출력됩니다. 

![execute_jupyter_notebook](/assets/images/2024-02-18-execute-jupyter-notebook.png){: .align-center}
<p style="text-align: center;">주피터 노트북 실행</p>

설정된 기본 브라우저가 실행되면서 아나콘다가 설치된 `C:\Users\<사용자 계정>` 디렉토리가 나타납니다.

![execute_jupyter_nobook_ide](/assets/images/2024-02-18-execute-jupyter-notebook-ide.png){: .align-center}
<p style="text-align: center;">주피터 노트북 IDE</p>

##### 주피터 노트북 만들기

파이썬 코드를 작성하기 위해 원하는 디렉토리에 파이썬 노트북 파일을 만듭니다. 
생성된 주피터 노트북 파일의 확장자는 `.ipynb` 입니다.

- 위치 : New > Python3 

![create_jupyter_notebook](/assets/images/2024-02-18-create-jupyter-notebook.png){: .align-center}
<p style="text-align: center;">주피터 노트북 생성</p>

##### 파이썬 코드 작성하기

빈 텍스트 박스는 코드를 입력하는 부분으로 다음과 같은 코드를 입력한 후 `Ctrl + Enter` 또는 상단의 `Run` 버튼으로 코드를 실행합니다.

![execute_python_code_jupyter_notebook](/assets/images/2024-02-18-execute-python-code-jupyter-notebook.png){: .align-center}
<p style="text-align: center;">파이썬 코드 실행</p>

#### 참고

주피터 노트북은 Markdown 을 제공하고, 그래프를 그리는 등 많은 기능을 제공합니다. 자세한 내용은 Official Docs 를 참고하기 바랍니다.

- Jupyter Project Official Docs : [https://docs.jupyter.org/en/latest/](https://docs.jupyter.org/en/latest/)

