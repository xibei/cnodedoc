## Assert
## 断言

This module is used for writing unit tests for your applications, you can
access it with `require('assert')`.

断言（Assert）模块用于为应用编写单元测试，可以通过`require('assert')`对该模块进行调用。

### assert.fail(actual, expected, message, operator)

Tests if `actual` is equal to `expected` using the operator provided.

使用指定操作符测试`actual`（真实值）是否和`expected`（期望值）一致。

### assert.ok(value, [message])

Tests if value is a `true` value, it is equivalent to `assert.equal(true, value, message);`

测试实际值是否为`true`，和`assert.equal(true, value, message);`作用一致

### assert.equal(actual, expected, [message])

Tests shallow, coercive equality with the equal comparison operator ( `==` ).

使用等值比较操作符( `==` )测试真实值是否浅层地（shallow），强制性地（coercive）和预期值相等。

### assert.notEqual(actual, expected, [message])

Tests shallow, coercive non-equality with the not equal comparison operator ( `!=` ).

使用不等比较操作符( `!=` )测试真实值是否浅层地（shallow），强制性地（coercive）和预期值不相等。

### assert.deepEqual(actual, expected, [message])

Tests for deep equality.

测试真实值是否深层次地和预期值相等。

### assert.notDeepEqual(actual, expected, [message])

Tests for any deep inequality.

测试真实值是否深层次地和预期值不相等。

### assert.strictEqual(actual, expected, [message])

Tests strict equality, as determined by the strict equality operator ( `===` )

使用严格相等操作符 ( `===` )测试真实值是否严格地（strict）和预期值相等。

### assert.notStrictEqual(actual, expected, [message])

Tests strict non-equality, as determined by the strict not equal operator ( `!==` )

使用严格不相等操作符 ( `!==` )测试真实值是否严格地（strict）和预期值不相等。

### assert.throws(block, [error], [message])

Expects `block` to throw an error. `error` can be constructor, regexp or 
validation function.

预期`block`时抛出一个错误（error）， `error`可以为构造函数，正则表达式或者其他验证器。

Validate instanceof using constructor:

使用构造函数验证实例：

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      Error
    );

Validate error message using RegExp:

使用正则表达式验证错误信息：

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      /value/
    );

Custom error validation:

用户自定义的错误验证器：

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      function(err) {
        if ( (err instanceof Error) && /value/.test(err) ) {
          return true;
        }
      },
      "unexpected error"
    );

### assert.doesNotThrow(block, [error], [message])

Expects `block` not to throw an error, see assert.throws for details.

预期`block`时不抛出错误，详细信息请见assert.throws。

### assert.ifError(value)

Tests if value is not a false value, throws if it is a true value. Useful when
testing the first argument, `error` in callbacks.

测试实际值是出错，当没有出错时抛出信息。常用于第一个参数（the first argument），回调中的`error`的测试。