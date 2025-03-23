---
title: 状态模式(C++版)
index_img: https://pic1.zhimg.com/70/v2-0599e9ba19dcdaeff6ca545d1bd6e199_1440w.avis?source=172ae18b&biz_tag=Post
category:
  - 设计模式
tags:
  - C++
abbrlink: '469'
date: 2024-01-22 18:52:20 
---







关于状态模式的一些概念可以查看 [状态模式 JAVA](http://luxi.natapp1.cc/2024/02/05/%E7%8A%B6%E6%80%81%E6%A8%A1%E5%BC%8F%EF%BC%88java%E7%89%88%EF%BC%89/)

## 1.类图

![https://subingwen.cn/design-patterns/state/image-20220925230648189.png](https://subingwen.cn/design-patterns/state/image-20220925230648189.png)

## 2.代码

### 2.1Sanji.h

```C++
// Sanji.h
#pragma once
#include "State.h"

class Sanji
{
public:
	Sanji()
	{
		m_state = new ForenoonState;
	}
	void working()
	{
		m_state->working(this);
	}
	void setState(AbstractState* state)
	{
		if (m_state != nullptr)
		{
			delete m_state;
		}
		m_state = state;
	}
	void setClock(int time)
	{
		m_clock = time;
	}
	int getClock()
	{
		return m_clock;
	}
	~Sanji()
	{
		delete m_state;
	}
private:
	int m_clock = 0;    // 时钟
	AbstractState* m_state = nullptr;
};
```



### 2.2 state.h

```c++
// State.h
// 抽象状态
#pragma once
class Sanji;

class AbstractState
{
public:
	virtual void working(Sanji* sanji) = 0;
	virtual ~AbstractState() {}
};

// 上午状态
class ForenoonState : public AbstractState
{
public:
	void working(Sanji* sanji) override;
};

// 中午状态
class NoonState : public AbstractState
{
public:
	void working(Sanji* sanji) override;
};

// 下午状态
class AfternoonState : public AbstractState
{
public:
	void working(Sanji* sanji) override;
};

// 晚上状态
class EveningState : public AbstractState
{
public:
	void working(Sanji* sanji) override;
};

```

### 2.3State.cpp

```C++
#include <iostream>
#include "State.h"
#pragma once
#include "Sanji.h"
using namespace std;

void ForenoonState::working(Sanji* sanji)
{
	int time = sanji->getClock();
	if (time < 8)
	{
		cout << "当前时间<" << time << ">点, 准备早餐, 布鲁克得多喝点牛奶..." << endl;
	}
	else if (time > 8 && time < 11)
	{
		cout << "当前时间<" << time << ">点, 去船头钓鱼, 储备食材..." << endl;
	}
	else
	{
		sanji->setState(new NoonState);
		sanji->working();
	}
}

void NoonState::working(Sanji* sanji)
{
	int time = sanji->getClock();
	if (time < 13)
	{
		cout << "当前时间<" << time << ">点, 去厨房做午饭, 给路飞多做点肉..." << endl;
	}
	else
	{
		sanji->setState(new AfternoonState);
		sanji->working();
	}
}

void AfternoonState::working(Sanji* sanji)
{
	int time = sanji->getClock();
	if (time < 15)
	{
		cout << "当前时间<" << time << ">点, 准备下午茶, 给罗宾和娜美制作爱心甜点..." << endl;
	}
	else if (time > 15 && time < 18)
	{
		cout << "当前时间<" << time << ">点, 和乔巴去船尾钓鱼, 储备食材..." << endl;
	}
	else
	{
		sanji->setState(new EveningState);
		sanji->working();
	}
}

void EveningState::working(Sanji* sanji)
{
	int time = sanji->getClock();
	if (time < 19)
	{
		cout << "当前时间<" << time << ">点, 去厨房做晚饭, 让索隆多喝点汤..." << endl;
	}
	else
	{
		cout << "当前时间<" << time << ">点, 今天过得很高兴, 累了睡觉了..." << endl;
	}
}
```

### 测试程序：main.cpp

```c++
#include <iostream>
#pragma once
#include "State.h"
#include "Sanji.h"
#include <vector>
using namespace std;

int main()
{
    Sanji* sanji = new Sanji;
    // 时间点
    vector<int> data{ 10,22 };
    for (const auto& item : data)
    {
        sanji->setClock(item);
        sanji->working();
    }
    delete sanji;

    return 0;
}
```

