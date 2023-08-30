---
title: '디버깅을 통해 배우는 리눅스 커널의 구조와 원리 -1-'
author: kmlee
date: 2023-08-28 18:00:00 +9
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
#p53 ~
> [Raspberry Pi kernel document](https://www.raspberrypi.com/documentation/computers/linux_kernel.html)
53p에서 제시하는 위 커널 빌드 관련 문서 링크에 들어가면, 아래와 같이 모델 등의 구분으로 4가지 선택지가 제시된다. 내가 사용중인 시스템에서는 3번째 설정이 적합하므로 이를 진행했다. (교재도 이와 같다.)<br>
![image](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/a2ba8dac-75e6-4286-adb1-fdba125cdf74)<br>
이후 빌드 과정도 32-bit 요건에 맞게 진행했다. 빌드 시간은 <br>
![image](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/59ca23e1-a4aa-4dc3-ad86-7a38b89c4122)<br>







참고 사이트
======================================
https://www.raspberrypi.com/documentation/computers/linux_kernel.html