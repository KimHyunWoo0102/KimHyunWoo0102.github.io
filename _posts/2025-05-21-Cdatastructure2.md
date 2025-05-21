---
layout: single
title:  "c 자료구조 - single linked list"
categories: c 자료구조
tag: [c, data_structure]
toc: true
author_profile: false
classes: wide
---



# LinkedList

![3-8-1-1](https://i.namu.wiki/i/hFHqMeFbDOwzJpGkV86b0xaxkZoFHdYDbV5YtSFA9UIyFaMotwD5FkPZvyAKXFCfyAVR9gbv7a1eUXNwA3Yp0bnF8Fz-SSP4nvU5JRBMAgO1-gCYPWV_P1Za177z5K0quSz7-_ojxd7NW88HHBT9mg.webp)

> 추상적 자료형인 [리스트](https://namu.wiki/w/리스트(자료구조))를 구현한 자료 구조이다.



포인터를 통하여 사슬처럼 연결되어 있다.

새로운 데이터를 추가하기에 용이하며 가변하는 크기에 자유롭다.

다만 배열처럼 인덱스로써 접근하지 못하기에 탐색속도는 떨어진다.





### 이벤트 루프

사용자의 입력을 받아 이를 처리 하기 위해 이벤트 루프를 사용한다.

```c
typedef enum MY_MENU { EXIT, NEW, SEARCH, PRINT, REMOVE }MY_MENU;

MY_MENU PrintMenu() {
	MY_MENU input = 0;

	system("cls");
	printf("[1]New\t[2]Search\t[3]Print\t[4]Remove\t[0]Exit\n:");
	scanf_s("%d%*c", &input);
	return input;
}

void run() {
	MY_MENU menu = 0;

	while (menu = PrintMenu()) {
		switch (menu) {
		case NEW:
			//AppendList();
			break;
		case SEARCH:
			break;
		case PRINT:
			PrintList();
			break;
		case REMOVE:
			break;
		default:
			break;
		}
	}
}
```



### 전체 출력

LinkedList가 정상 작동하는지 쉽게 알아보기 위해 본인의 주소, 데이터, pNext의 주소를 출력해준다.

```c
void PrintList() {
	if (g_pHeadNode == NULL)
		return;

	USERDATA* iter = g_pHeadNode;

	while (iter != NULL)
	{
		printf("[%p] %d %s %s [%p]\n",
			iter,
			iter->age, iter->name, iter->phone,
			iter->pNext);
		iter = iter->pNext;
	}
}
```



### 노드 추가

데이터를 입력받아 새로운 노드를 만든다.

이후 제일 마지막 노드를 찾아 맨뒤에 저장한다.

추후에는 구조체를 받아서 사용하는것도 나쁘지 않아 보인다.

```c
void AppendList(int age, const char* name, const char* phone) {
	USERDATA* newNode = (USERDATA*)malloc(sizeof(USERDATA));

	
	newNode->age = age;
	strcpy_s(newNode->name, sizeof(newNode->name), name);
	strcpy_s(newNode->phone, sizeof(newNode->phone), phone);
	newNode->pNext = NULL;

	if (g_pHeadNode == NULL)
		g_pHeadNode = newNode;
	else {
		USERDATA* iter = g_pHeadNode;
		while (iter->pNext != NULL) {
			iter = iter->pNext;
		}

		iter->pNext = newNode;
	}
}
```



### 전체 노드 free

동적 할당으로 받은것이니 모든 노드를 free해주며 이때 어떤 노드가 free되는지 출력또한 해준다.

```c
void ReleaseList(){
	USERDATA* iter = g_pHeadNode;
	while (iter != NULL) {
		USERDATA* temp = iter;
		iter = iter->pNext;
		printf("Delete: [%p] %d %s %s [%p]\n",
			temp,
			temp->age, temp->name, temp->phone,
			temp->pNext);
		free(temp);
	}
}
```



### 배운점

1. 항상 가시성에 신경써라. 디버깅을 하기위해 테스트코드(출력문)을 먼저 작성한 이후 오류가 난 부분을 디버그 모드로 살피는 것이다.

2. 원하는 프로그램을 상세히 서술하라. 그 이후 모듈화를 시킨뒤 해당 모듈또한 기능에 대해 자세히 서술한다.

3. 동작 원리등을 그림으로 그리면서 살펴보아라.

4. main에는 가급적 함수들만 남겨둬라. 전체 흐름만 파악할수 있게끔 하여라.

   