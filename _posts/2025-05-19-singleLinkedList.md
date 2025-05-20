---
layout: single
title:  "자료구조 - 리스트5: Singlelist 정의"
categories: c++ 자료구조 (중단)
tag: [c++,data_structure]
toc: true
author_profile: false
classes: wide
typora-root-url: ../

---

# 목표

이전 List와 LinkedList의 인터페이스를 변경하지 않고도 정상 작동하는 LinkedList를 만드는 것이 목표이다.

SingleLinkedList에서는 데이터를 추가하고 제거하는 것, 조회와 get 등만 구현해 놓으면 된다.

Node를 동적할당하여 사용하니 추후 해제에 주의해야한다.

소멸자에서 자동으로 가지고 있는 모든 노드를 삭제 하게끔 한다.



```c++
//CSingleList.h
#pragma once

#include "ILinkedList.h"
#include "Exception.h"

template<typename T>
class CSingleList : public ILinkedList<T> {
public:
	explicit CSingleList() : ILinkedList<T>() {}
	virtual ~CSingleList() = default;

	virtual const std::vector<T> getElements() const override;
	virtual const T& get(int rank) override;

	virtual void append(int rank, const T& element) override;
	virtual const T& remove(int rank) override;

	virtual const size_t search(const T& element) override;
};

// ✅ 요소 전체 반환
template<typename T>
inline const std::vector<T> CSingleList<T>::getElements() const {
	std::vector<T> ret;
	std::shared_ptr<Node<T>> current = this->_head->_next;  // dummy node 이후부터

	while (current != nullptr) {
		ret.push_back(current->_data);
		current = current->_next;
	}

	return ret;
}

// ✅ 특정 위치 요소 반환
template<typename T>
inline const T& CSingleList<T>::get(int rank) {
	std::shared_ptr<Node<T>> ret = this->getNode(rank);
	return ret->_next->_data;
}

// ✅ 삽입
template<typename T>
inline void CSingleList<T>::append(int rank, const T& element) {
	std::shared_ptr<Node<T>> current = this->getNode(rank);
	std::shared_ptr<Node<T>> newNode = std::make_shared<Node<T>>(element);

	newNode->_next = current->_next;
	current->_next = newNode;
	this->_size++;
}

// ✅ 삭제
template<typename T>
inline const T& CSingleList<T>::remove(int rank) {
	if (rank < 0 || rank >= this->_size) {
		throw InvalidRankException(__func__, "Rank is out of bounds!");
	}

	std::shared_ptr<Node<T>> prev = this->getNode(rank);
	std::shared_ptr<Node<T>> target = prev->_next;
	T remove_element = target->_data;
	if (target == nullptr) {
		throw InvalidRankException(__func__, "No node to remove at given rank.");
	}

	prev->_next = target->_next; // 자동으로 target의 참조 count 감소
	this->_size--;
	return remove_element;
}

// ✅ 검색
template<typename T>
inline const size_t CSingleList<T>::search(const T& element) {
	std::shared_ptr<Node<T>> current = this->_head->_next;
	size_t index = 0;

	while (current != nullptr) {
		if (current->_data == element) {
			return index;
		}
		current = current->_next;
		index++;
	}
	throw NotFoundException(__func__, "Element not found.");
}

```



### 느낀점

처음에 인터페이스를 만들고 구성할때는 생각보다 귀찮고 손도 많이 갔다.

하지만 초반 작업만 해놓으니까 상속받은 클래스에서는 원하는 기능들만 override하거나 추가하면 되니 매우 편리한거 같다.

가장 신경써야 했던 부분은 LinkedList와 CArrayList에서 함수를 호출해야 하는 방식이 똑같아야 한다는 점이 어려웠다.

일관성을 가지고 기능들을 짜내야 한다는게 생각보다 불편하게 느껴졌지만 다 완성시키고 나서 테스트 코드를 별도의 변경없이 둘다 제대로 돌아가는 것이

매우 마음에 들었다.



물론 부족한게 많은 코드들이지만 그래도 뭔가 어설프게나마 했다는 성취감이 있는듯 하다.

다만 oop에 대한 개념이 아직 많이 약하고 코드를 구현하기는 어설프다는 생각이 든다.

내가 만든 커스텀 클래스를 기반으로 새로운 무언가를 만들어내려고 할때 꼬인다.



c++과 OOP에 대해서 다시 공부후 돌아오겠다.