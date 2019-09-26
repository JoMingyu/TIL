# pad zeros on string formatting
숫자 1을 1 그대로가 아니라 '01'이나 '001'같은 걸로 포매팅해야 할 때가 있다.

```python
'%03d' % 3
'{:03}'.format(3)
v = 3
f'{v:03}'
```
