---
title: Memory
author: kmlee
date: 2023-08-22 18:00:00 +9
categories: []
tags: [concept]
---

해당 포스트에는 메모리의 기본적인 개념과 관련 지식들을 작성하고자 한다. 최초에 작성한 내용과 별개로, 앞으로도 메모리 관련 내용들을 정리할 일이 있으면 이 포스트에 지속적으로 추가하려고 한다.

A) 메모리 사용 현황 구분
==============================
리눅스 상에서 free 명령은 메모리 사용 현황을 출력하는 명령어이다. 이를 사용하면 아래와 같이 출력 결과가 나타난다. <br>  
![result_command_free](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/28764f9b-52e1-42bc-9024-90a6f3353e2b) <br> 
 
현재 내가 사용중인 시스템의 메모리 전체 영역이 어떻게 사용되고 있는지 해당 내용을 통해 파악할 수 있다. 하지만 각 칼럼의 영역이 어떤 용도인지, 그리고 swap이 무엇을 의미하는지에 대한 이해가 선행되어야 하기에 이를 찾아보고 나름대로 정리해보고자 한다. <br>

우선, 'free' 명령의 출력에서는 위 사진에서 나타나듯 6가지 컬럼으로 용도를 구분하는데, 각 내용이 무엇을 의미하는지 알아보았다. <br>

1. used (=total-free-buff/cache)
> used는 쉽게 말하자면 프로세스에서 사용중인 메모리 영역이다. 전체 메모리 영역에서 사용되지 않고 있는 free 영역과 프로세스(혹은 서비스) 용도 외에 다른 특정 목적으로 사용되는 buff/cache 영역을 제외하면 이를 계산할 수 있다. 후술하겠지만, buff/cache 영역은 재사용 가능한 메모리 영역으로 간주되기에 used 영역이 실질적으로 메모리 상에서 점유되고 있는 구역이라고 볼 수 있을 것 같다. <br>
> ![result_command_free_2](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/cae9f447-47d1-480d-9034-b603a7fa9c9c) <br>
> 해당 내용에 따르자면 total에서 free와 buff/cache 영역의 크기를 제외하면 used 영역의 크기와 일치해야 하는데, 이것이 맞는지 위 사진의 결과를 토대로 실제로 계산해보면 아래와 같이 일치함을 확인할 수 있다. <br>
<div style="text-align: center;">
 > ```16402208 - (12992192 + 2555340) = 854676 (kb)```
</div>

2. free (=total-used-buff/cache)
> free는 아직 사용되지 않고 남아있는 메모리 영역을 의미한다. man page - free 상에서는 unused 메모리라고 표현하고 있다. 앞서 말한 used 영역이야 말할 필요도 없고, buff/cache 영역 또한 어찌되었든 특정 목적으로 사용하고 있는 구간이다. 따라서 이 두 영역을 제외하고 남은 영역을 free 영역으로 정의한다. 하지만 buff/cache 영역은 말한 바와 같이 재사용 가능한 영역이 포함되어 있으므로, free 영역을 현재 사용 가능한 영역 전체라고 해석하는 것은 잘못되었다고 볼 수 있다. free 영역에 대한 계산 식은 위 1번의 used에 대한 식을 이항한 것에 불과하므로 굳이 다시 검산하지 않아도 될 것 같다. <br>

3. buff/cache
> man page 상에서는 buffers와 cache 영역에 대해 아래와 같이 표현한다. <br>
> ![manual_free_buffcache](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/44cf8bd7-ae30-4cc6-8570-15efc0669db1) <br>
> 위 두 내용을 토대로 나름대로 알아보고 이해해본 바는 아래와 같다. 우선 buff 영역에 대한 내용은 아래와 같다. <br>
> 우선 buff 영역은 커널 버퍼에서 사용되고 있다고 정의되어있다. 저장장치가 연결되었을때, 커널은 이들과 입출력 통신을 해야한다. 여기에서 주고 받는 데이터들이 임시로 그룹화되어 저장되는 곳이라고 이해하였다. 예를 들어, 특정 어플리케이션이 다른 디스크에 데이터를 입력할 때, 해당 데이터는 임시로 메모리의 buff 영역에 저장된 이후 주어진 스케줄링에 따라 디스크로 플러시할 것이다.<br>
> 다음은 cashe 영역에 대해 정리해보았다. cache 영역은 page cache와 slabs로 사용된다고 표현되어 있다. CPU에서 사용되는 cache 장치와 개념이 잠시 혼동되었는데, page cache는 디스크의 있는 메모리 중  메모리에 임시로 저장한 데이터를 말한다. 프로세스는 메모리에서 실행되기 때문에 자주 사용되는 데이터가 있다면 저장장치가 아닌 메모리에서 가져오는게 성능적으로 효율적이다. 다만 메모리의 원래 용도가 저장이 아니기 때문에 편의상 임시로 저장하는 용도라고 볼 수 있다. 저장 장치를 읽을 때, 시스템은 page cache를 확인하여 해당 데이터가 존재하는지 확인하고, 그렇다면 굳이 디스크에서 읽지 않고 해당 데이터를 빠르게 가져온다. 그 다음, slabs 영역에 대해서 이해하기 위해서 아래 그림을 먼저 참고하였다. <br> 
> ![image_slab](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/211b23db-06a1-453f-a5f0-b5c4b4f033b9) <br>
> 커널이 데이터를 처리할 때, 다양한 data structure가 사용된다. slab 에서는 위 그림에서 나타난 바와 같이, 미리 다양한 size의 cache를 만들어놓고 kernel에서 여러 크기의 data structure 할당을 요청할 때 곧 바로 처리해준다. 정리하자면, 커널의 데이터 처리를 위해 임시로 할당된 메모리 영역이라고 볼 수 있다. <br>
> 결국 정리하자면, buff/cache 영역은 kernel buffer, page cache, slabs에 사용되는 영역으로 이루어져 있다고 이해했다. 또한 알아본 바에 따르면, 위 3가지 영역들이 사용하는 메모리 현황을 '/proc/meminfo' 상에서 확인할 수 있었다. 해당 내용과 free에서 출력한 buff/cache 영역의 크기를 계산하여 비교해보면 아래와 같다. <br>
> ![result_command_free,meminfo](https://github.com/yesleekm/yesleekm.github.io/assets/54760524/58df53e8-3f64-4981-babd-e072089bfad3) <br>
> 파란 박스 내의 Cached는 page cache에 사용되는 영역이라고 이해할 수 있을 것이다. 또한 kernel buffer와 slabs에서 사용하는 영역의 크기도 마찬가지로 표시되어 있다. 이 세 값들의 합을 빨간 박스 내의 buff/cache 크기(free명령 상 출력)와 비교해서 계산해보면 아래와 같이 일치함을 확인할 수 있다. <br>
> <center>```462972 + 1199664 + 947932 = 2610568 (kb)```</center>

4. shared
5. available

스왑메모리






작성일자
==============================
A. 메모리 사용 현황 구분 - 23/08/22



참고
==============================
* https://jaynamm.tistory.com/entry/%EB%A9%94%EB%AA%A8%EB%A6%AC-Buffer-%EC%99%80-Cache-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0 <br>
* https://jw910911.tistory.com/122 <br>
* https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235927 <br>
* https://youngswooyoung.tistory.com/65 <br>
* https://wpaud16.tistory.com/293 <br>