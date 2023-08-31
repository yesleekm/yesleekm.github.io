---
title: '디버깅을 통해 배우는 리눅스 커널의 구조와 원리 -1-'
author: kmlee
date: 2023-08-30 14:00:00 +9
categories: [Study,kernel]
tags: [study]
---

교재 학습 간 사용한 환경은 아래와 같다. Rasbian image와 Linux kernel에 대한 버전은 저자가 사용한 것과 일치한다. <br>
```
Raspberry Pi model: Raspberry Pi 4 Model B Rev 1.2 (32-bit)
Rasbian image version: 2019-08-10-raspbian-buster-full
Linux kernel version: 4.19.57
```
#p1 ~ p52
> 배경 지식 및 초기 환경설정과 관련된 내용이다. 
<br>
#2.3 라즈베리파이 커널빌드(p53 ~ p67)
> [Raspberry Pi kernel document](https://www.raspberrypi.com/documentation/computers/linux_kernel.html)
p53 에서 제시하는 위 커널 빌드 관련 문서 링크에 들어가면, 아래와 같이 모델 등의 구분으로 4가지 선택지가 제시된다. 내가 사용중인 시스템에서는 3번째 설정이 적합하므로 이를 진행했다. (교재도 이와 같다.)<br>
![image](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/a2ba8dac-75e6-4286-adb1-fdba125cdf74)<br>
이후 빌드 과정도 32-bit 요건에 맞게 진행했다. 빌드 시간은 1~2시간 가량 걸린 것 같다. 공식문서의 가이드를 따라 명령어를 작성했을 때에도 문제 없이 잘 완료되었고, 교재 p54 ~ p59까지 제공하는 스크립트를 작성해서 작업해도 마찬가지로 가능할 것 같다. 나는 아직 스크립트에 대한 이해가 조금 부족해서, 공식 가이드의 명령어를 통해 빌드를 완료했다.<br>
![image](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/59ca23e1-a4aa-4dc3-ad86-7a38b89c4122)<br>
p60 ~ p65 에서는 커널을 빌드하는 과정에서 전처리 코드가 생성될 수 있도록 'Makefile'을 설정하는 방법에 대해 설명해준다. 앞선 과정에서 빌드를 이미 해버렸기에, 순서에 대한 아쉬움이 있지만,,, 그래도 유용한 내용이라는 생각이 들었다. 'Makefile' 내에서 '-save-temps=obj \' 옵션을 추가함으로써 빌드 과정에서 전처리 코드가 생성될 수 있게 한다고 한다. 또한 전체 소스가 아닌, 특정 소스 빌드과정에서만 같은 일을 하도록 만들 수도 있다. 전체를 다시 빌드하기엔 시간소요가 있으므로, 소스 분석 중 필요시에 특정 전처리 파일을 생성하려고 한다.
p66 ~ p67 에서는 리눅스 커널 소스가 어떤 구조로 되어있는지 설명해준다. 






참고 사이트
======================================
https://www.raspberrypi.com/documentation/computers/linux_kernel.html