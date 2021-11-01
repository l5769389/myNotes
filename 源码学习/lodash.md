

# array

## chunk 





## difference

###  baseFindIndex,带方向的遍历

1. index初始值的设置
2. `while ((fromRight ? index-- : ++index < length))`的执行顺序。
   1. `while (fromRight ? index-- : (++index < length)) {`

```js
function baseFindIndex(array, predicate, fromIndex, fromRight) {
  var length = array.length,
      index = fromIndex + (fromRight ? 1 : -1);

  while ((fromRight ? index-- : ++index < length)) {
    if (predicate(array[index], index, array)) {
      return index;
    }
  }
  return -1;
}

```

