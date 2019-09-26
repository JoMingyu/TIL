# pad zeros on string formatting
숫자 1을 1 그대로가 아니라 '01'이나 '001'같은 걸로 포매팅해야 할 때가 있다.

```python
'%03d' % 3
'{:03}'.format(3)
v = 3
f'{v:03}'
```

[string#Format Specification Mini-Language](https://docs.python.org/3/library/string.html#format-specification-mini-language) 부분을 보면 더 상세히 알아볼 수 있다.

## 아예 string formatting을 안 쓰는 방식
formatting source가 애초에 문자열이라면 str 클래스의 메소드를 써먹을 수 있다.

```python
'abc'.rjust(10, '0') # '0000000abc'
'3'.zfill(3) # '003
```
