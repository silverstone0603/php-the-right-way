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


2. Files
--------

### 2.1. PHP Tags

PHP code MUST use the long `<?php ?>` tags or the short-echo `<?= ?>` tags; it
MUST NOT use the other tag variations.

### 2.2. Character Encoding

PHP code MUST use only UTF-8 without BOM.

### 2.3. Side Effects

A file SHOULD declare new symbols (classes, functions, constants,
etc.) and cause no other side effects, or it SHOULD execute logic with side
effects, but SHOULD NOT do both.

The phrase "side effects" means execution of logic not directly related to
declaring classes, functions, constants, etc., *merely from including the
file*.

"Side effects" include but are not limited to: generating output, explicit
use of `require` or `include`, connecting to external services, modifying ini
settings, emitting errors or exceptions, modifying global or static variables,
reading from or writing to a file, and so on.

The following is an example of a file with both declarations and side effects;
i.e, an example of what to avoid:

```php
<?php
// side effect: change ini settings
ini_set('error_reporting', E_ALL);

// side effect: loads a file
include "file.php";

// side effect: generates output
echo "<html>\n";

// declaration
function foo()
{
    // function body
}
```

The following example is of a file that contains declarations without side
effects; i.e., an example of what to emulate:

```php
<?php
// declaration
function foo()
{
    // function body
}

// conditional declaration is *not* a side effect
if (! function_exists('bar')) {
    function bar()
    {
        // function body
    }
}
```


3. Namespace and Class Names
----------------------------

Namespaces and classes MUST follow an "autoloading" PSR: [[PSR-0], [PSR-4]].

This means each class is in a file by itself, and is in a namespace of at
least one level: a top-level vendor name.

Class names MUST be declared in `StudlyCaps`.

Code written for PHP 5.3 and after MUST use formal namespaces.

For example:

```php
<?php
// PHP 5.3 and later:
namespace Vendor\Model;

class Foo
{
}
```

Code written for 5.2.x and before SHOULD use the pseudo-namespacing convention
of `Vendor_` prefixes on class names.

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
