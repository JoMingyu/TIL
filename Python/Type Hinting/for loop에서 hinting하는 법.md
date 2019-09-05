# for loop에서 hinting하는 법
for loop에서는 variable hinting이 불가능하다.

```python
for i: int in range(10): # syntax error
    ...
```

블럭이 시작되기 전에 타입을 명시해두는 것으로 회피하곤 한다.

```python
i: int
for i in range(10):
    ...
```
