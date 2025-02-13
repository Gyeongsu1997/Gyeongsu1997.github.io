---
layout: single
title: "시스템 콜이란?"
category: OS
tags:
  - JavaScript
  - React
toc: true
---

## 1. 개요

운영체제를 공부해보신 분들은 리눅스에서 프로세스를 생성할 때 fork와 exec 시스템 콜을 사용한다는 것을 들어보신 적이 있으실 것입니다. 여기서 말하는 시스템 콜이란 무엇일까요? 그리고 그것은 일반적인 라이브러리 함수와 어떻게 다른 걸까요? 오늘의 포스팅에서는 시스템 콜의 개념과 시스템 콜이 등장하게 된 배경에 대해 알아보겠습니다.

## 2. 시스템 콜의 개념

시스템 콜은 응용 프로그램이 운영체제의 커널이 제공하는 서비스를 요청할 수 있도록 운영체제가 제공하는 인터페이스입니다. 커널이 제공하는 서비스에는 프로세스를 생성하거나 파일에 접근하는 것 등이 있습니다. 아래는 리눅스의 대표적인 시스템 콜인 [open](https://man7.org/linux/man-pages/man2/open.2.html){:target="_blank"}, [write](https://man7.org/linux/man-pages/man2/write.2.html){:target="_blank"}, [close](https://man7.org/linux/man-pages/man2/close.2.html){:target="_blank"}를 사용하는 예시입니다. 코드를 컴파일하고 실행하면 "hello world"라는 문자열이 담긴 파일이 생성됩니다.

```c
// io.c
#include <unistd.h>
#include <fcntl.h>

int main(void)
{
    int fd;

    fd = open("example", O_WRONLY|O_CREAT|O_TRUNC, S_IRWXU);
    write(fd, "hello world", 11);
    close(fd);
    return 0;
}
```

시스템 콜을 호출하는 것은 언뜻 보기에는 일반적인 라이브러리 함수를 호출하는 것과 다를 게 없어 보이지만 실제로는 중대한 차이가 있습니다. 그것은 시스템 콜을 호출하면 제어권을 운영체제에 넘김과 동시에 하드웨어의 특권 레벨(hardware privilege level)을 높인다는 것입니다. 이 말이 무엇인지 이해하기 위해 시스템 콜이 등장하게 된 배경부터.

## 3. 등장 배경

초기의 운영체제는 단순히 흔히 사용되는 함수들을 모아놓은 라이브러리에 불과했습니다. 개발자가 직접 저수준의 I/O 작업을 하는 코드를 작성하지않아도 되도록 운영체제가 API를 제공했습니다. 자원에 무분별하게 접근하는 문제.

the idea here was to add a special pair of hardware instructions and hardware state to make the transition into the OS a more formal, controlled process.

시스템 콜의 개념이 등장하게 됩니다. 시스템 콜의 핵심은 하드웨어 지원이다.

시스템 콜은 

CPU의 권한 수준을 높인다는 것.

## 4. 권한 수준

시스템 콜이 일반적인 함수 호출과 다른 핵심적인 차이는 시스템 콜을 호출하면 하드웨어의 특권 레벨(hardware privilege level)을 높인다는 것입니다.

Intel 64 아키텍처의 특권 레벨

![protection-rings]({{site.url}}/images/2025-02-12-introduction-of-system-call/protection-rings.png)

User applications run in what is referred to as user mode which means the hardware restricts what applications can do; for example, an application running in user mode can’t
typically initiate an I/O request to the disk, access any physical memory page, or send a packet on the network.

When the OS is done servicing the request, it passes control back to the user via a special return-from-trap instruction,
which reverts to user mode while simultaneously passing control back to where the application left off.

## Summary

[](https://man7.org/linux/man-pages/dir_section_2.html){:target="_blank"}

## Reference

- [임시](https://man7.org/linux/man-pages/dir_section_2.html){:target="_blank"}
