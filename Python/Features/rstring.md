# rstring
'r'이나 'R'이 문자열의 시작점에 있으면, 문자열의 escape sequence가 단순 문자열로 해석된다.

```python
print(r'\abc')
# '\\abc'
print(r'\abc' == '\\abc')
# True
```
