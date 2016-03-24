기본 코딩 표준
=====================

이 섹션에서는 PHP 코드들 사이에서 고수준의 기술적인 상호운용성을 보장하기
위해서 고려되어야 할 코딩 표준을 다룹니다.

이 문서에서 사용되는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 라는 용어는 
[RFC 2119](http://tools.ietf.org/html/rfc2119) 에 설명되어 있는대로 해석되어야 합니다.


1. 개요
-----------

- 파일에서는 반드시(MUST) `<?php` 와 `<?=` 태그만 사용해야 합니다.

- 파일에서 PHP 코드는 반드시(MUST) BOM이 없는 UTF-8 인코딩만을 사용해야 합니다.

- 파일에서는 심볼(클래스, 함수, 상수 등)을 정의하는 일
  *혹은* 부수효과를 일으키는 일(출력하기, .ini 설정 변경하기 등) 중에 *하나만* 해야 
  합니다(SHOULD). 두 가지를 모두 하면 안됩니다(SHOULD NOT).

- 네임스페이스와 클래스는 반드시(MUST) "자동 로딩" PSR([PSR-0], [PSR-4])에 따라야 합니다.

- 클래스 이름은 반드시(MUST) `StudlyCaps` 스타일로 대소문자를 사용하여 작성되어야 합니다.

- 클래스 상수는 반드시(MUST) 밑줄(언더스코어)로 구분된 대문자를 사용하여 작성되어야 합니다.

- 메소드 이름은 반드시(MUST) `camelCase` 스타일로 대소문자를 사용하여 작성되어야 합니다.


2. 파일
--------

### 2.1. PHP 태그

PHP 코드는 반드시(MUST) 긴 형식의 `<?php ?>` 혹은 echo 축약형 `<?= ?>` 태그만을
사용해서 작성되어야 합니다. 이전에 사용되었던 다른 형태의 태그를 절대 사용하면
안됩니다(MUST NOT).

### 2.2. 문자 인코딩

PHP 코드는 반드시(MUST) BOM이 없는 UTF-8 만을 사용해야 합니다.

### 2.3. 부수 효과(Side Effects)

파일은 부수 효과를 일으키지 않는 새로운 심볼(클래스, 함수, 상수 등) 정의 코드만
포함해야 합니다(SHOULD). 혹은 심볼 정의는 없이 부수 효과를 일으키는 코드만을
포함해야 합니다(SHOULD). 한 파일에 두 가지를 모두 넣으면 안됩니다(SHOULD NOT).

"부수 효과"란 클래스, 함수, 상수 등을 정의하는 것과 직접 관련이 없는 로직이
실행되는 것을 의미하는데, *단순히 다른 파일을 불러오는(include) 코드* 조차도
부수 효과를 일으키는 코드입니다.

"부수 효과"의 예시는 다음과 같지만, 이 예시외에도 더 있습니다: 출력하기, 
`require` 나 `include`, 다른 서비스에 연결하기, ini 설정 변경하기, 에러나
예외 발생시키기, 전역 변수 혹은 정적 변수를 변경하기, 파일을 읽고 쓰기 등.

아래 예제 파일은 심볼 정의와 부수 효과 일으키기를 같이 가지고 있는 것으로서,
피해야만 하는 파일의 한 형태입니다.

```php
<?php
// side effect: ini 설정 변경하기
ini_set('error_reporting', E_ALL);

// side effect: 파일 include 하기
include "file.php";

// side effect: 출력하기
echo "<html>\n";

// 심볼 정의
function foo()
{
    // 함수 내용
}
```

아래 파일 예시는 부수 효과는 없이 정의만을 담고 있는 파일로서, PSR을 잘 따르는
예시입니다.

```php
<?php
// 심볼 정의
function foo()
{
    // 함수 내용
}

// *조건에 따른 심볼 정의는 부수 효과가 아님*
if (! function_exists('bar')) {
    function bar()
    {
        // 함수 내용
    }
}
```


3. 네임스페이스와 클래스 이름
----------------------------

네임스페이스와 클래스는 반드시(MUST) "자동 로딩" PSR([PSR-0], [PSR-4])에 따라야 합니다.

즉, 각각의 클래스는 파일 하나당 클래스 하나만 단독으로 정의되어야 하고
최소 한 레벨의 네임스페이스(제작사 이름을 의미하는 최상위 네임스페이스) 안에
들어가 있어야 한다는 말입니다.

클래스 이름은 반드시(MUST) `StudlyCaps` 스타일로 작성되어야 합니다.

PHP 5.3과 그 이후의 버전을 대상으로 작성된 코드는 반드시(MUST)
정식으로 네임스페이스 구분을 사용해야 합니다.

아래의 예시를 참고하십시오.

```php
<?php
// PHP 5.3 and later:
namespace Vendor\Model;

class Foo
{
}
```

위 예시와 동일한 의미의 코드를 PHP 5.2.x와 그 이전의 버전을 대상으로 
작성한다면 네임스페이스 흉내 규칙 (pseudo-namespacing convention)에 
따라서 아래와 같이 클래스 이름을 작성해야 합니다(SHOULD).

```php
<?php
// PHP 5.2.x and earlier:
class Vendor_Model_Foo
{
}
```

4. Class Constants, Properties, and Methods
-------------------------------------------

The term "class" refers to all classes, interfaces, and traits.

### 4.1. Constants

Class constants MUST be declared in all upper case with underscore separators.
For example:

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. Properties

This guide intentionally avoids any recommendation regarding the use of
`$StudlyCaps`, `$camelCase`, or `$under_score` property names.

Whatever naming convention is used SHOULD be applied consistently within a
reasonable scope. That scope may be vendor-level, package-level, class-level,
or method-level.

### 4.3. Methods

Method names MUST be declared in `camelCase()`.

[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[PSR-4]: https://github.com/ModernPUG/php-the-right-way/blob/gh-pages/more/Psr-4-Autoloader.md
