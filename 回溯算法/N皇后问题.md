# 问题描述
> 在一个N*N的棋盘中放置N个皇后，每一个皇后都不能与其它皇后在相同行、相同列或者处于一条斜线上，求有多少种摆放方案

# 解题思路
* 采用递归的方式，先假设该位置可以放置皇后，然后判断与已有的位置是否冲突，若不冲突则继续下一个皇后的摆放，若冲突则本位置无效，继续下一个位置的尝试
* 采用一维数组代替二维数组，可以达到清除以前无效数据的结果，简化实现难度

# 实现代码
## JS实现
```js
//全局变量计数
var count = 0;
//求绝对值的函数
function abs(value){
   return value > 0 ? value : -value;
}

function isValid(row,arr){
   for(var i=0;i<row;i++){
     if(arr[row]==arr[i]||abs(i-row)==abs(arr[i]-arr[row])){
        return false;
     }
   }
   return true;
}

function findQueue(row,arr){
   if(row==arr.length){
     console.log("------------"+(++count)+"------------");
     console.log(arr);
     return;
   }

   for(var i=0;i<arr.length;i++){
     arr[row]=i;
     if(isValid(row,arr)){
        findQueue(row + 1,arr);
     }
   }
}

//测试代码
findQueue(0,new Array(8));
``` 
## Java实现
