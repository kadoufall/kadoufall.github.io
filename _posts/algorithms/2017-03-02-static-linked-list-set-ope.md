---
title: 静态链表--集合运算(A-B)∪(B-A)的实现
date: 2017-03-02 16:55:12
tags:
    - exercises
    - Algorithms
---
 给定A和B两个数组，将A中元素依次插入链表S中，扫描B中的元素，若S中已有该元素，则删除S中的该元素；若S中没有该元素，则添加入S中

* 初始化链表**注意的是应当维护两个指针，一个指针指向空闲位的下标，一个指针指向链表的起始位置**

* 插入B中元素的时候，**注意应当根据链表中元素的指针依次遍历链表，不能以顺序表的形式遍历**

<!-- more -->


```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define LENGTH 22

// 链表的单个元素
typedef struct element {
	int data;
	int cur;
}Element;

//找到空闲位
int getEmpty(Element* list) {
	return list->cur;
}

int main() {

	int A[10] = {111,223,3,2344,785,16,3247,6578,89,910};
	int B[10] = { 1,223,3,2344,6,16,45,111,89,90};

	// 初始化链表
	Element* list = (Element *)malloc(LENGTH*sizeof(Element));
	memset(list,0,sizeof(Element)*LENGTH);

	Element* flag = list;
	flag->cur = 2;
	flag++;
	flag->cur = 2;
	flag++;
	int i = 0;
	for (i = 2; i < LENGTH-1; i++)
	{
		(list+i)->cur = i+1;
	}

	(list+LENGTH-1)->cur = 0;
	int end = 2;

	// 加入A的元素
	for (i = 0; i < 10; i++)
	{
		int loc = getEmpty(list);

		list->cur = (list + loc)->cur;		// 更新空闲位的指针
		(list + loc)->data = A[i];			// 添加数据
		(list + loc)->cur = 0;				// 更新自己的指针
		(list + end)->cur = loc;			// 更新前一个的指针
		end = loc;							// 更新结尾标志，设置为自己
	}
	
	for (i = 0; i < 10; i++)
	{
		int front = (list+1)->cur;
		int loc = (list + 1)->cur;
		int j = (list + 1)->cur;

		// 遍历看是否链表中已有
		while ((list + j)->data != B[i] && (list + j)->cur != 0) {
			front = j;
			j = (list + j)->cur;
		}
		loc = j;
		
		if (loc != end)
		{
			if (loc == 2)
			{
				(list + 1)->cur = (list + loc)->cur;
			}
			(list + front)->cur = (list + loc)->cur;
			(list + loc)->data = 0;
			(list + loc)->cur = list->cur;
			list->cur = loc;

		}
		else {		// 若已经遍历到末尾，则没有
			loc = getEmpty(list);
			list->cur = (list + loc)->cur;
			(list + loc)->data = B[i];
			(list + end)->cur = loc;
			end = loc;
			(list + loc)->cur = 0;

		}
	
	}


	for ( i = 0; i < LENGTH; i++)
	{
		printf("%d  %d \n",(list+i)->data, (list+i)->cur);
	}
	

	free(list);
	return 1;
} 


```

