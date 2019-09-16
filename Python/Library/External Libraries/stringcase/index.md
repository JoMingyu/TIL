# stringcase
stringcase 라이브러리는 말 그대로 string case에 관한 헬퍼들을 제공한다.

```python
import stringcase
stringcase.camelcase('foo_bar_baz') # => "fooBarBaz"
stringcase.camelcase('FooBarBaz') # => "fooBarBaz"
stringcase.capitalcase('foo_bar_baz') # => "Foo_bar_baz"
stringcase.capitalcase('FooBarBaz') # => "FooBarBaz"
stringcase.constcase('foo_bar_baz') # => "FOO_BAR_BAZ"
stringcase.constcase('FooBarBaz') # => "_FOO_BAR_BAZ"
stringcase.lowercase('foo_bar_baz') # => "foo_bar_baz"
stringcase.lowercase('FooBarBaz') # => "foobarbaz"
stringcase.pascalcase('foo_bar_baz') # => "FooBarBaz"
stringcase.pascalcase('FooBarBaz') # => "FooBarBaz"
stringcase.pathcase('foo_bar_baz') # => "foo/bar/baz"
stringcase.pathcase('FooBarBaz') # => "/foo/bar/baz"
stringcase.sentencecase('foo_bar_baz') # => "Foo bar baz"
stringcase.sentencecase('FooBarBaz') # => "Foo bar baz"
stringcase.snakecase('foo_bar_baz') # => "foo_bar_baz"
stringcase.snakecase('FooBarBaz') # => "_foo_bar_baz"
stringcase.spinalcase('foo_bar_baz') # => "foo-bar-baz"
stringcase.spinalcase('FooBarBaz') # => "-foo-bar-baz"
stringcase.titlecase('foo_bar_baz') # => "Foo Bar Baz"
stringcase.titlecase('FooBarBaz') # => " Foo Bar Baz"
stringcase.trimcase('foo_bar_baz') # => "foo_bar_baz"
stringcase.trimcase('FooBarBaz') # => "FooBarBaz"
stringcase.uppercase('foo_bar_baz') # => "FOO_BAR_BAZ"
stringcase.uppercase('FooBarBaz') # => "FOOBARBAZ"
stringcase.alphanumcase('_Foo., Bar') # =>'FooBar'
stringcase.alphanumcase('Foo_123 Bar!') # =>'Foo123Bar'
```