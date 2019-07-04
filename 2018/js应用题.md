1. 千分位
```js
    number.toString().replace(/(\d)(?=(\d{3})+\.)/g, '$1,');
```

2. 将‘abcd’转换成‘a-b-c-d’
```js
    ‘abcd’.split('').join('-');
```

3. 排序（冒泡和快排）
```js
    var arr = [...];
    // 冒泡
    for(var i = 0; i < arr.length; i ++){
        for(var j = 0; i < arr.length-i-1; j ++){
            if(arr[j] < arr[j+1]){
                var temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
            }
        }
    }

    // 快排
    function quickSort(arr) {
        if(arr.length <= 1) return arr;
        var halfIndex = arr.length/2;
        var pivot = arr.splice(halfIndex, 1)[0];    // 中间的基准
        var left = [],
            right = [];
        // 将比基准小的放在left，大的放在right
        for(var i = 0; i < arr.length; i++){
            if(arr[i] <= pivot){
                left.push(arr[i]);
            }else{
                right.push(arr[i]);
            }
        }
        // 递归
        return quickSort(left).concat([pivot], quickSort(right));
    }
```

4. 正则匹配邮箱
```js
    var reg = /^[A-Za-z0-9_-]+@[A-Za-z0-9_-]+(\.[A-Za-z0-9_-]+)+$/g
```

5. 完成下列要求
```js
    var str = "1, 3, 4, 5, 7, 8, 0";
    // 要求得到结果 "1, 345, 78, 0", 并求出得到的分组后的几个值的分别的最大值和最小值
    // 例如 最大值  最小值
    //        1   1
    //        3   5
    //        7   8
    //        0   0
```

6. 完成下列要求`accmulate(4)(4, 5)(3);  输出 ==>  4 * (4 + 5) * 3`
```js
    var accmulate = (function () {
        var nums = [];
        var calc = function (){
            var item = [].reduce.call(arguments, (prev, next) => prev + next);
            nums = nums.concat(item);
            return calc
        }
        calc.valueOf = () => {
            var temp = nums.slice();
            nums = [];
            return temp.reduce((prev, next) => prev * next)
        }
        return calc
    })()
    console.log(accmulate(2)(4, 5)(2));
```

7. 实现 `a == 1 && a == 2 && a == 3`
```js
const a = {  
    i: 1,
    toString() {    
        return a.i++
    }
}
if(a == 1 && a == 2 && a == 3) {  
    console.log('Hello World!')
}
```

8. 用js实现`JSON.Stringify`

9. js实现多继承

