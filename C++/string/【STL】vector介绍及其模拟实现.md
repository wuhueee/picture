[TOC]

# 一、vector的介绍

[vector文档介绍](https://cplusplus.com/reference/vector/vector/)

1.vector是表示可变大小数组的序列容器

2.就像数组一样，vector也采用的连续存储空间来存储元素。也就是意味着可以采用下标对vector的元素进行访问，和数组一样高效。但是又不像数组，它的大小是可以动态改变的，而且它的大小会被容器自动处理

3.本质讲，vector使用动态分配数组来存储它的元素。当新元素插入时候，这个数组需要被重新分配大小为了增加存储空间。其做法是，分配一个新的数组，然后将全部元素移到这个数组。就时间而言，这是一个相对代价高的任务，因为每当一个新的元素加入到容器的时候，vector并不会每次都重新分配大小

4.vector分配空间策略：vector会分配一些额外的空间以适应可能的增长，因为存储空间比实际需要的存储空间更大。不同的库采用不同的策略权衡空间的使用和重新分配。但是无论如何，重新分配都应该是对数增长的间隔大小，以至于在末尾插入一个元素的时候是在常数时间的复杂度完成的

5.vector占用了更多的存储空间，为了获得管理存储空间的能力，并且以一种有效的方式动态增长。

6.与其它动态序列容器相比（deque, list and forward_list）， vector在访问元素的时候更加高效，在末尾添加和删除元素相对高效。对于其它不在末尾的删除和插入操作，效率更低。比起list和forward_list统一的迭代器和引用更好

# 二、vector的使用

## 1.构造函数

vector提供了四种构造方式--无参构造，n个val构造，迭代区间构造和拷贝构造



| **构造函数声明**                                            | **接口说明**             |
| ----------------------------------------------------------- | ------------------------ |
| vector()                                                    | 无参构造                 |
| vector（size_type n, const value_type& val = value_type()） | 构造并初始化n个val       |
| vector (const vector& x)                                    | 拷贝构造                 |
| vector (InputIterator fifirst, InputIterator last)          | 使用迭代器进行初始化构造 |

【注意】

>1.对于构造函数的最后一个参数alloc是空间配置器，它和内存池有关，它的作用是提高空间分配的效率，我们一般情况下不需要管这个参数，使用它的缺省值即可，这个缺省值的原因是有极少数的人需要使用自己实现的空间配置器来代替STL库里的
>
>2.在n个val构造中，val的缺省值是T的匿名对象，该对象使用T的默认构造来初始化，而不是使用0来作为这个缺省值，这个是因为T不仅仅是内置类型，也可以是自定义类型，比如string,vector,list;当T为自定义类型的时候，0就不一定能够对val进行初始化，所以我们就用T的匿名对象去调用它的默认构造去进行初始化，当T为内置类型的时候，内置类型也会去调用它的构造函数，大家可能很惊讶，内置类型怎么可能有构造函数呢，这是编译器进行了特殊的处理，使得内置类型也具有了构造函数。

## 2.扩容机制

capacity的代码在vs和g++下分别运行会发现，vs下capacity是按1.5倍增长的，g++是按2倍增长的。这个问题经常会考察，不要固化的认为，vector增容都是2倍，具体增长多少是根据具体的需求定义的。vs是PJ版本STL，g++是SGI版本STL

测试用例如下：



## 3.三种遍历方式

vector和string一样，也支持三种遍历方式--下标+[]，迭代器遍历，范围for遍历

【注意】

vector和string支持下标+[]的方式进行遍历的原因是string和vector的底层都是通过数组实现的，而数组支持随机访问，但是对于不是通过数组实现的容器，比如list,set map等等，他们就不能通过下标+[]的方式进行遍历，此外，我们还需要注意，范围for只是一个外壳，它在调用的时候也是去调用迭代器，所以迭代器是所有容器通用的遍历方式

## 4.容量操作

| **容量空间**                                                 | **接口说明**         |
| ------------------------------------------------------------ | -------------------- |
| [size](https://cplusplus.com/reference/vector/vector/size/)  | 获取数据个数         |
| [capacity](https://cplusplus.com/reference/vector/vector/capacity/) | 获取容量大小         |
| [empty](https://cplusplus.com/reference/vector/vector/empty/) | 判断是否为空         |
| [resize](https://cplusplus.com/reference/vector/vector/resize/) | 改变vector的size     |
| [reserve](https://cplusplus.com/reference/vector/vector/reserve/) | 改变vector的capacity |

其中，最重要的两个函数为resize和reserve，resize的作用的扩容和初始化，既会改变size也会改变capacity，而reserve只用于扩容，不改变size

但是我们需要注意，resize,reserve以及clear函数都不会缩容，因为缩容需要重新开辟空间，拷贝数据和释放空间，而对于自定义类型又可能存在深拷贝的问题，时间消耗很大，在迫不得已的情况下，比如开辟了一块很大的空间，并且确定这块空间只需要一小部分，这个时候我们可以使用shrink_to_fit函数，如果capacity大于size,它会进行缩容，让size==capacity

【总结】

1.reserve只负责开辟空间，如果确定知道需要用多少空间，reserve可以缓解vector增容的代价缺陷问题

2.resize在开空间的同时还会进行初始化，影响size和capacity

## 5.元素访问

vector提供了以下接口来进行元素访问

其中，operator[]和at都是返回pos位置的元素的引用，且他们内部都会对pos进行合法性检查，二者不同的是，operator[]如果检查到pos位置非法的时候，那么它会直接终止程序，报断言错误，而at是抛异常

【注意】在release模式下检查不出断言错误

## 6.增删查改

| vector增删查改                                               | **接口说明**                                           |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| [push_back](https://cplusplus.com/reference/vector/vector/push_back/) | 尾插                                                   |
| [pop_back](https://cplusplus.com/reference/vector/vector/pop_back/) | 尾删                                                   |
| [find](https://cplusplus.com/reference/algorithm/find/?kw=find) | 查找。（注意这个是算法模块实现，不是vector的成员接口） |
| [insert](https://cplusplus.com/reference/vector/vector/insert/) | 在position之前插入val                                  |
| [erase](https://cplusplus.com/reference/vector/vector/erase/) | 删除position位置的数据                                 |
| [swap](https://cplusplus.com/reference/vector/vector/swap/)  | 交换两个vector的数据空间                               |
| [operator[]](https://cplusplus.com/reference/vector/vector/operator%5B%5D/) | 像数组一样访问                                         |

**assign**

assign函数用来替换vector对象中的数据，支持n个val替换和迭代区间替换

**push_back && pop_back**

push_back是进行尾插，pop_back是尾删，这两个函数比较简单，我们看一下示例即可：

**insert && erase**

和string不同的是，为了提高规范性，STL的容器都统一使用iterator作为pos的类型，并且插入和删除后会返回pos,所以我们如果在中间插入或者删除数据的时候，可以和算法库里的find配合起来使用

【注意】

在VS在，insert和erase之后会导致pos迭代器失效，如果需要再次使用，需要更新pos

不过，在Linux下不会出现这个问题：

造成这个问题的根本原因是VS使用的是PJ版本对iterator进行了封装，在每次insert和earse之后会对迭代器进行特殊处理，而g++使用的是SGI版本中的iterator原生指针，为了代码的可移植性，我们统一认为insert和earse使用之后迭代器会失效，所以需要再次使用迭代器，我们必须对其进行更新，我们以移除vector中的所有偶数为例：

**swap**

和string一样，由于算法库里的swap函数存在深拷贝的问题，vector自己提供了一个不需要深拷贝的swap函数，用于交换两个vector对象：

同时，为了避免我们不使用成员函数的swap，vector还将算法库中的vector进行了重载，然后该重载函数的内部又去调用成员函数swap:

# 三、vector深度剖析及模拟实现

## 1.核心框架

我们学习STL可以去阅读别人优秀的代码，然后理解其中的逻辑和细节后我们自己去独立实现几次，因此我们可以对阅读STL的源码，但是当前阶段我们只需要学习STL库的核心框架，并不需要逐行进行的阅读，其中有些语法我们也无法理解，我们也可以阅读优秀的书籍，我这里推荐侯捷老师的《STL源码剖析》,这本书使用的是SGI版本，这个版本适合阅读，《STL源码剖析》电子版和《stl30》源码我放在了下面，需要的自取：

```
STL源码剖析：https://www.aliyundrive.com/s/Nc4mpLC43kj
stl30：https://www.aliyundrive.com/s/pnwMuB9uwEN
```

我们就可以得到vector源码的核心框架：

```cpp
namespace hdp
{
	template<class T>
	class vector
	{
	public:
		typedef T* iterator;
		typedef const T* const_iterator;

		// 成员函数
        // ......
	private:
		T* _start;
		T* _finish;
		T* _endofstorage;
	};
}
```

我们可以看到，vector和string一样，都是一个指针指向一个动态开辟的数组，但是二者不同的是，string是用\_size和\_capacity两个size_t 的成员变量来维护这块空间，而vector是用\_finish和\_endofstorage两个指针来维护这块空间，其中二者在本质上是一样的，其中\_size=_finish-\_start;\_capacity=\_endofstorage -\_start;

## 2.使用memcpy拷贝问题

## 3.动态二维数组理解

## 4.构造函数错误调用问题

我们模拟实现构造函数中 的迭代区间构造和n格val构造后，会发现一个问题，我们使用n个val来构造其他类型的对象的时候没有问题，但是构造int类型的对象时会编译出错：

这是由于编译器在进行模板实例化和函数参数匹配时会调用最匹配的一个函数，当我们将T实例化为int的时候，由于两个参数都是int，所以对于迭代器构造函数来说，它会直接将InputIterator实例化为int;对于n个val的构造来说，它需要将T实例化为int，还需要将第一参数隐式转换为size_t，所以这个时候，编译器会调用迭代器构造，同时迭代器构造内部会对first进行解引用，所以这个报错"非法的间接寻址"

对于这个问题，我们有很多的解决方式：比如调用时将第一个参数强转为size_t,又或将n个val构造的第一个参数设置为int,我们这里和STL源码保持一致--提供第一个参数为int的n个val构造的重载函数：

## 5.insert和 erase迭代器失效问题

我们模拟实现的insert和earse函数如下：

我们在VS在insert和erase调用之后迭代器会失效，再次访问的时候会报错，这是因为PJ版本下的iterator不是原生指针，如下：

可以看到，VS中的迭代器是一个类，当我们进行insert和erase调用之后，iterator中的某个函数可能会将pos置为空或者其他操作，所以我们再次访问pos位置的数据就会报错，除非我们每次调用之后都更新pos:



在linux在，g++却不会出现这样的问题，这个是因为g++使用的是SGI版本，该版本的iterator是一个原生指针，同时它的insert和erase的内部实现和我们模拟实现的类似。





## 6.模拟实现完整代码

### 6.1 vector.h

```cpp
#pragma once

namespace hdp
{
	template<class T>
	class vector
	{
	public:
		typedef T* iterator;
		typedef const T* const_iterator;

		// 迭代器
		iterator begin()
		{
			return _start;
		}

		iterator end()
		{
			return _finish;
		}

		// const 迭代器
		const_iterator begin()  const
		{
			return _start;
		}

		const_iterator end()  const
		{
			return _finish;
		}

		// 重载[]
		T& operator[](size_t pos)
		{
			assert(pos < size());
			return *(_start + pos);
		}

		// const 重载[]
		const T& operator[](size_t pos) const
		{
			assert(pos < size());
			return *(_start + pos);
		}

		// 无参构造
		vector()
			:_start(nullptr)
			, _finish(nullptr)
			, _endofstorage(nullptr)
		{}

		// n个val初始化
		vector(size_t n, const T& val = T())
			:_start(nullptr)
			, _finish(nullptr)
			, _endofstorage(nullptr)
		{
			reserve(n);
			for (size_t i = 0; i < n; ++i)
			{
				push_back(val);
			}
		}

		vector(int n, const T& val = T())
			:_start(nullptr)
			, _finish(nullptr)
			, _endofstorage(nullptr)
		{
			reserve(n);
			for (int i = 0; i < n; ++i)
			{
				push_back(val);
			}
		}

		// 迭代器初始化
		template <class InputIterator>

		vector(InputIterator first, InputIterator last)
			:_start(nullptr)
			, _finish(nullptr)
			, _endofstorage(nullptr)
		{
			while (first != last)
			{
				push_back(*first);
				++first;
			}
		}

		// 拷贝构造
		// v1(v2)  写法1
		/*vector(const vector<T>& v)
			:_start(nullptr)
			, _finish(nullptr)
			, _endofstorage(nullptr)
		{
			reserve(v.capacity());
			for (const auto& e : v)
			{
				push_back(e);
			}
		}*/

		// 拷贝构造  写法2
		vector(const vector<T>& v)
			:_start(nullptr)
			, _finish(nullptr)
			, _endofstorage(nullptr)
		{
			vector<T> tmp(v.begin(), v.end());
			swap(tmp);
		}

		// v1 = v2
		// v1 = v1;  // 极少数情况，能保证正确性，所以这里就这样写没什么问题
		// 赋值重载
		vector<T>& operator=(vector<T> v)
		{
			swap(v);
			return *this;
		}

		// 析构
		~vector()
		{
			delete[] _start;
			_start = _finish = _endofstorage = nullptr;
		}

		// 预留空间
		void reserve(size_t n)
		{
			if (n > capacity())
			{
				size_t oldSize = size();
				T* tmp = new T[n];

				// 如果_start不为空则进行拷贝
				if (_start)
				{
					// memcpy(tmp, _start, sizeof(T) * oldSize);  // 深层次的深拷贝的时候会出错
					for (size_t i = 0; i < oldSize; ++i)
					{
						tmp[i] = _start[i];
					}

					delete[] _start;
				}
				_start = tmp;
				_finish = tmp + oldSize;
				_endofstorage = _start + n;
			}
		}

		//调整空间大小
		void resize(size_t n, T val = T())
		{
			if (n > capacity())
			{
				reserve(n);
			}
			if (n > size())
			{
				while (_finish < _start + n)
				{
					*_finish = val;
					++_finish;
				}
			}
			else
			{
				_finish = _start + n;
			}
		}

		// 在尾部插入数据
		void push_back(const T& val)
		{
			if (_finish == _endofstorage)
			{
				size_t newCapacity = capacity() == 0 ? 4 : capacity() * 2;
				reserve(newCapacity);
			}

			*_finish = val;
			++_finish;
		}

		// 删除尾部数据
		void pop_back()
		{
			// 为空时不进行删除
			assert(!empty());
			--_finish;
		}

		// 交换
		void swap(vector<T>& v)
		{
			std::swap(_start, v._start);
			std::swap(_finish, v._finish);
			std::swap(_endofstorage, v._endofstorage);
		}

		// 在pos位置插入数据
		// 迭代器失效 : 扩容引起，野指针问题
		iterator insert(iterator pos, const T& val)
		{
			// 断言pos的位置
			assert(pos >= _start);
			assert(pos < _finish);

			// 空间不够进行扩容
			if (_finish == _endofstorage)
			{
				size_t len = pos - _start;
				size_t newCapacity = capacity() == 0 ? 4 : capacity() * 2;
				reserve(newCapacity);

				// 扩容会导致pos迭代器失效，需要更新处理一下
				pos = _start + len;
			}

			// 挪动数据
			iterator end = _finish - 1;
			while (end >= pos)
			{
				*(end + 1) = *end;
				--end;
			}

			*pos = val;
			++_finish;

			return pos;
		}

		// 删除pos位置数据
		iterator erase(iterator pos)
		{
			// 断言pos的位置
			assert(pos >= _start);
			assert(pos < _finish);

			// 挪动数据
			iterator begin = pos + 1;
			while (begin < _finish)
			{
				*(begin - 1) = *begin;
				++begin;
			}

			--_finish;

			return pos;

		}

		// 清空
		void clear()
		{
			_start = _finish;
		}

		// 判空
		bool empty()
		{
			return _start == _finish;
		}

		// 获取数据个数
		size_t size()
		{
			return _finish - _start;
		}

		// 获取容量
		size_t capacity()
		{
			return _endofstorage - _start;
		}
	private:
		iterator _start;
		iterator _finish;
		iterator _endofstorage;
	};
}
```

### 6.2 test.cpp

```cpp
#include <iostream>
#include <vector>
#include <assert.h>
#include <algorithm>
using namespace std;
#include  "vector.h"

// 测试三种遍历
void test_vector1()
{
	hdp::vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);

	// 下标+[]遍历
	for (size_t i = 0; i < v.size(); ++i)
	{
		cout << v[i] << " ";
	}
	cout << endl;

	// 迭代器遍历
	hdp::vector<int>::iterator it = v.begin();
	while (it != v.end())
	{
		cout << *it << " ";
		++it;
	}
	cout << endl;

	// 范围for遍历
	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;
}

// 测试尾插和容量改变
void test_vector2()
{
	hdp::vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	v.push_back(5);
	cout << v.capacity() << endl;

	v.reserve(10);
	cout << v.capacity() << endl;

	// 比当前容量小时，不缩容
	v.reserve(4);
	cout << v.capacity() << endl;

	v.resize(8);
	v.resize(15, 1);
	v.resize(3);
}

// 测试插入
void test_vector3()
{
	hdp::vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);

	v.insert(v.begin(), 4);
	v.insert(v.begin() + 2, 4);

	// 没有find成员
	//vector<int>::iterator it = v.find(3);

	hdp::vector<int>::iterator it = find(v.begin(), v.end(), 3);
	if (it != v.end())
	{
		v.insert(it, 30);
	}

	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;

	hdp::vector<int> v1;
	v1.push_back(10);
	v1.push_back(20);
	v1.push_back(30);

	v1.swap(v);
	swap(v1, v);
	for (auto e : v1)
	{
		cout << e << " ";
	}
	cout << endl;
}
void test_vector4()
{
	hdp::vector<vector<int>> vv;
	vector<int> v(5, 1);
	vv.push_back(v);
	vv.push_back(v);
	vv.push_back(v);
	vv.push_back(v);
	vv.push_back(v);

	for (size_t i = 0; i < vv.size(); ++i)
	{
		for (size_t j = 0; j < vv[i].size(); ++j)
		{
			cout << vv[i][j] << " ";
		}
		cout << endl;
	}
	cout << endl;
}

// 测试删除
void test_vector5()
{
	// 要求删除所有偶数
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	v.push_back(5);


	vector<int>::iterator it = v.begin();
	while (it != v.end())
	{
		if (*it % 2 == 0)
		{
			it = v.erase(it);
		}
		else
		{
			++it;
		}
	}

	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;
}
int main()
{
	try
	{
		//test_vector1();
		//test_vector2();
		//TestVectorExpand();
		//test_vector3();
		//test_vector4();
		test_vector5();
	}
	catch (const exception& e)
	{
		cout << e.what() << endl;
	}
	return 0;
}


```

