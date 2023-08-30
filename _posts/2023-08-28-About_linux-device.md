---
title: Linux Device
author: kmlee
date: 2023-08-28 18:00:00 +9
categories: [Concepts]
tags: [concept]
---

해당 포스트에는 리눅스 디바이스 관리에 기본적인 개념과 관련 지식들을 이해한 범위 내에서 작성하고자 한다. 최초에 작성한 내용과 별개로, 앞으로도 리눅스 디바이스 관리와 관련된 내용들을 정리할 일이 있으면 이 포스트에 지속적으로 추가하려고 한다.

A) 리눅스에서의 디바이스 유형 구분
==============================
리눅스 운영 체제 하에서는 모든 디바이스를 파일로 관리하는데, 이들을 주로 두 가지 유형으로 나누어서 관리한다고 한다. (네트워크 디바이스도 있다고 하는데, 성격이 조금 다르므로 다른 기회에 다뤄보고자 한다.)'block device'와 'character device'가 그들이다. 실제로 그러한지 확인하기 위해, 리눅스가 디바이스를 파일 형태로 관리하고 있는 '/dev'을 조회해보았다. 'ls -al' 명령을 통해 '/dev' 디렉토리를 살펴보면 아래와 같이 파일 유형이 b,c,d,l 4가지로 나타나는 것을 확인할 수 있다. <br>
![result_command_ls_dev](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/781405f9-8f59-4524-9e06-00813acbd232) <br>
l과 d에 대해서는 각각 symbolic link file과 directory라고 알고 있고, 남은 b와 c는 'block'과 'character'로 유추된다. 하지만 해당 부분이 맞는지 정확히 파악해보기 위해 메뉴얼을 참조하였다. 
![manual_ls_see-also](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/dc7a759c-bbe1-4fbc-943d-061e8776b004)
![manual_ls_filetype](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/a4624d5f-8a2f-4a56-9749-6f135a3000d3) <br>
'man ls' 상의 내용에서 'info 'ls invocation'을 통해 ls에 대한 full document를 확인할 수 있다고 안내해주고 있다. 따라서 해당 문서에 접근하여 'ls -al'시 나타나는 파일유형 표현에 대해 위 두 번째 그림에서와 같이 알아보았다. 따라서 각각 b는 'block special file', c는 'character special file'의 약자임을 정확히 알 수 있었는데, 이들이 정확히 무엇을 표현하는 지에 대해 여러 인터넷 자료를 참고하여 이해해보았다. <br>

1. character device
> character device는 키보드, 마우스 등의 I/O 장치를 포함하며, 이름 그대로 Character, 즉 byte 단위의 데이터를 처리하는 디바이스이다. byte to byte의 형식으로 데이터를 순차적으로 받아오며(FIFO, First-In_First-Out), 이를 따로 저장해두는 buffer가 존재하지 않는다. 이는 디바이스를 연결해서 다루는 시스템 상의 buffer를 의미하는 것으로, 이후에 나올 구조도에서 더욱 자세히 설명할 수 있을 것 같다. (다만, 일시적인 데이터를 담아두는 buffer는 존재한다고 한다.) 
2. block device
> block device의 종류로는 우리가 일반적으로 사용하는 디스크 드라이브 같은 저장 매체가 대표적이다. block device는 블록 단위로 데이터를 처리한다. 블록 단위라함은 미리 정의된 데이터 묶음의 사이즈를 말한다. 512byte, 1KB, 4KB 등이 이에 해당될 수 있다. 또한 내부 데이터를 관리하기 위해 file system(NTFS,FAT32 등)을 사용한다. 해당 내용을 보면서, file system은 운영체제에 종속적인데, file system의 사용이 block device의 특징이라는 내용이 잘 이해가 안 됐다. 결론부터 말하자면, 파일 시스템을 사용하는건 block device의 일반적인 특징이지만, 반드시 그래야만 하는 것은 아니라고 이해되었다. block device는 말 그대로 block 형태의 데이터를 다루기 때문에, 이를 효과적으로 관리하기 위해서 file system을 사용하는게 편리하다. 하지만 성능적 요소가 중요한 저장 장치에서는 file system을 이용하지 않고 raw data를 직접적으로 다루는 경우도 있다고 한다. <br>

![image_linux-device-structure](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/d2331ddc-bee3-4daa-810c-7d4298890270) <br>
위 그림은 리눅스에서 장치의 종류에 따라 어떻게 커널과 소통하는지를 나타낸 구조도이다. device driver가 커널과 하드웨어 사이의 중계 역할을 해주는건 장치를 불문하고 공통적인 사항이다. (device driver에 대한 세부적인 내용은 다른 항목에서 다뤄보고자 한다.) 또한 해당 구조에서 char device와 block device의 차이는 시스템 상의 buffer 존재 여부이다. 당연히 내부적으로는 훨씬 더 많은 차이가 있겠지만, 당장 구조를 보고 개념적으로 이해할 수 있는 차이는 이정도인 것 같다. <br>
결국 원론적으로 이야기하자면, 리눅스 상에서는 장치의 용도에 따라 장치 종류를 구분하고, 장치 종류에 따라 시스템(커널)에서 다루는 방법이 달라진다는 것으로 이해된다. <br>

B. 리눅스 디바이스 드라이버


작성일자
==============================
A. 리눅스에서의 디바이스 유형 구분 - 23/08/28



참고
==============================
https://tribal1012.tistory.com/154
https://karatus.tistory.com/206
https://wiki.kldp.org/Translations/html/The_Linux_Kernel-KLDP/tlk8.html
https://doosil87.github.io/linux/2019/07/29/rawdevicefilesystem.html