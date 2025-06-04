---
layout: single
title: "[windows] char, wchar_t, string, wstring, tchar, tstring 내용 정리"
categories: windows
---

> char는 크기의 한계가 있음. wchar_t는 wide char 타입임.
> tchar는 설정에 따라, char 또는 wchar_t로 변환됨.
> 동일한 코유를 문자 집합이 다른 애플리케이션 모두에 대해 재사용이 가능해짐.

1. char : 1바이트 크기의 문자 데이터 타입, ASCII 문자 집합을 표현
2. wchart_t : 2바이트 또는 4바이트 크기의 넓은 문자 데이터 타입, unicode 문자 집합을 지원
3. string : cpp에서 사용하는 문자열 클래스, 내부적으로 char 배열 사용
4. wstring : cpp에서 넓은 문자열을 지원하기 위한 클래스, 내부적으로 wchar_t 배열 사용
5. tchar : Windows API에서 사용되는 데이터 타입, 문자 집합 속성에 따라 char 또는 wchart_t로 정의됨
6. tstring : tchar를 기반으로 하는 문자열 타입, tchar에 따라 string 또는 wstring으로 정의됨.

#### tchar, tstring의 타입 결정

- VisualStudio > 프로젝트 속성 > 구성속성 > 고급 > 문자 집합
  - 유니코드 문자 집합 사용
  - 멀티바이트 문자 집합 사용 (사용자 정의 문자 집합)
    - ANSI 계열, ASCII
    - setlocale()을 통해 코드 페이지를 설정하여 특정 문자 인코딩을 지정도 가능

#### tstring 문자열 상수

```cpp
_T("문자열")
```
