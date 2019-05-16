# Iterate With enumerate Instead of range

## Using Range
```python
   numbers = [45, 22, 14, 65, 97, 72]
   for i in range(len(numbers)):
       print(numbers[i])
```


## Using enumerate
```python
   numbers = [45, 22, 14, 65, 97, 72]
   for i, num in enumerate(numbers):
     print(num)
```