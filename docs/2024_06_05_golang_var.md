---
layout: page
title: Golang 변수
description: >
 Go 언어 변수 정리
hide_description: true
sitemap: false
---

1. this unordered seed list will be replaced by the toc
{:toc}

# 변수란?

값을 저장하는 메모리 공간을 가리키는 이름

## 변수가 왜 중요한가?

프로그램이란 결국 데이터를 연산/조작 하는 일

## 변수의 4가지 속성

- 이름: **프로그래머**는 이름을 통해 값이 저장된 메모리 공간에 손쉽게 접근할 수 있음
- 값: 변수가 가리키는 메모리 공간에 저장된 값
- 주소: **컴퓨터**가 알아들을 수 있는 변수가 저장된 메모리 공간의 시작 주소
- 타입: 변숫값의 형태. 정수, 실수, 문자열 등의 다양한 타입들이 있으며 자료형, 데이터 타입이라고도 함. 데이터 타입을 알면 사이즈를 알 수 있음
    - `int 32`: integer 32 bit(4 byte)

### 숫자 타입

- uint 8 = unsigned integer 8 bit(1 byte)
- byte(uint 8): aliasing을 부여
- rune: 문자 1개 -> UTF-8
  - 1~3 byte이므로 적어도 3 byte 이상이어야 함
  - 컴퓨터에서 byte는 2의 지수로 증가하므로 4byte 부여, int32
- uint, int: 컴퓨터의 bit 수에 따라 다름
  - 컴퓨터가 64 bit일 때, int64

### 그 외 타입

- bool
- string
- 배열
- 슬라이스
- 구조체
- 포인터
- 함수타입
- 맵
- 인터페이스
- 채널

## 변수 선언의 다른 형태

```go
package main

import "fmt"

func main() {
    var a int = 3   // 기본 형태
    var b int       // 초깃값 생략, 초깃값은 타입별 기본값으로 대체
    var c = 4       // 타입 생략, 변수 타입은 우변 값의 타입이 됨
    d := 5          // 선언 대입문 :=을 사용해 var 키워드와 타입 생략(변수 선언이 번거로워 가장 많이 쓰이는 형태)

    fmt.Println(a, b, c, d)
}
```

## 타입 변환

연산의 각 항목의 타입은 **반드시** 같아야 함

*[FLIP]: First Last Invert Play
