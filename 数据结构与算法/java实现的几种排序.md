---
title: java实现的几种排序
tags: [数据结构,排序]
categories: 数据结构与算法
date: 2019-04-16
---

<div align='center' ><font size='70'>java实现的几种排序</font></div>

# 插入排序
实现代码：

```java
public class InsertSort{
	public void insert(int[] arr){
		int i,temp;
		//从位置1开始，位置0的数字相当于已排好序的
		for(i = 1;i < arr.length; i++) {
			//如果定位i位置的数小于前面已排好序的最后的数（最大），就进去进行下一步循环插入。否则直接下一步外循环，也就以为i位置的数时前面已排好的最大数，i后移继续判断
			if(arr[i] < arr[i-1]){
				//先将i位置的数存入temp，因为这个树如果要在前面已排好序的几个数中调适当位置插入
				temp = arr[i];
				//temp该数与前面数依次从右到左(从大数到小数)依次比较(大于退出循环)
				for(int j = i-1;j>=0 &&temp < arr[j];j--){
					//后移一位
					arr[j+1] = arr[j];
				}
				//已退出内层循环,temp数放在正确的位置
				//退出内循环的条件是j>=0 &&temp < arr[j]，
                // j>=0不成立表名已经前面已经没有数了，所以需要j+1放在0位置，temp < arr[j]不成立表明此时j位置的数比它小，所以需要放在j位置的后一个位置
				arr[j+1] = temp;
			}
			//此时内存循环结束一轮，意味着已排好的数数加1，即又排好一个数，准备进行下一个数的插入。
		}
	}
}

```

# 希尔排序
插入排序的升级版
实现代码：

```java
public class ShellSort {

    public static void shell(int[] arr){
        int gap = arr.length;
        int i,j,temp;
        do {
			//插入排序
            gap = gap/2;
            for (i = gap;i < arr.length;i++){
                if(arr[i] < arr[i-gap]){
                    temp = arr[i];
                    for (j=i-gap;j>=0 && temp < arr[j];j-=gap){
                        arr[j+gap] = arr[j];
                    }
                        arr[j+gap] = temp;
                }
            }
        }while (gap > 0);
    }
}
```


