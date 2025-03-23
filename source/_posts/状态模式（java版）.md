---
title: 状态模式(java版)
index_img: https://subingwen.cn/images/state.png
category:
  - 设计模式
tags:
  - Java
abbrlink: 6d9c
date: 2024-02-05 21:57:43
---

<meta name="referrer" content="no-referrer"/>









## 1. 定义

对有状态的对象，把复杂的“判断逻辑”提取到不同的状态对象中，允许状态对象在其内部状态发生改变 时改变其行为

## 2. 类型

行为型（behavioral）

## 3.介绍

**优点：** 1、封装了转换规则。 2、枚举可能的状态，在枚举状态之前需要确定状态种类。 3、将所有与某个状态有关的行为放到一个类中，并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为。 4、允许状态转换逻辑与状态对象合成一体，而不是某一个巨大的条件语句块。 5、可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数。

**缺点：** 1、状态模式的使用必然会增加系统类和对象的个数。 2、状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱。 3、状态模式对"开闭原则"的支持并不太好，对于可以切换状态的状态模式，增加新的状态类需要修改那些负责状态转换的源代码，否则无法切换到新增状态，而且修改某个状态类的行为也需修改对应类的源代码。

**使用场景：** 

- 行为随状态改变而改变的场景。
- 条件、分支语句的代替者。

**注意事项：**在行为受状态约束的时候使用状态模式，而且状态不超过 5 个。

##  4.结构 

 状态模式包含以下主要角色。

-  环境（Context）角色：也称为上下文，它定义了客户程序需要的接口，维护一个当前状态，并将与状态相关的操作委托给当前状态对象来处理。 
-  抽象状态（State）角色：定义一个接口，用以封装环境对象中的特定状态所对应的行为。
-  具体状态（Concrete State）角色：实现抽象状态所对应的行为。

<div align=center><img src="https://gitee.com/silent-learner/imgs/raw/master/imgs/%202023_Imgs/20240206015312.png"/></div>



## 5.代码

### 5.1  抽象状态类

```java
//抽象状态类
public abstract class LiftState {
    //定义一个环境角色，也就是封装状态的变化引起的功能变化
    protected Context context;
    public void setContext(Context context) {
        this.context = context;
    }
    //电梯开门动作
    public abstract void open();
    //电梯关门动作
    public abstract void close();
    //电梯运行动作
    public abstract void run();
    //电梯停止动作
    public abstract void stop();
}
```

### 5.2 状态机类

```java
package state;

public class StateMachine {


    public final OpeningState openingState;
    public final ClosingState closingState;
    public final RunningState runningState;
    public final StoppingState stoppingState;

    public StateMachine() {
        //开门状态，这时候电梯只能关闭
        openingState = new OpeningState();
        //关闭状态，这时候电梯可以运行、停止和开门
        closingState = new ClosingState();
        //运行状态，这时候电梯只能停止
        runningState = new RunningState();
        //停止状态，这时候电梯可以开门、运行
        stoppingState = new StoppingState();
    }

    public class OpeningState extends LiftState {
        //开启当然可以关闭了，我就想测试一下电梯门开关功能
        @Override
        public void open() {
            System.out.println("电梯门开启...");
        }

        @Override
        public void close() {
            //状态修改
            super.context.setLiftState(closingState);
            //动作委托为CloseState来执行，也就是委托给了ClosingState子类执行这个动作
            super.context.getLiftState().close();
        }

        //电梯门不能开着就跑，这里什么也不做
        @Override
        public void run() {
            //do nothing
        }

        //开门状态已经是停止的了
        @Override
        public void stop() {
            //do nothing
        }
    }

    //运行状态
    public class RunningState extends LiftState {
        //运行的时候开电梯门？你疯了！电梯不会给你开的
        @Override
        public void open() {
            //do nothing
        }

        //电梯门关闭？这是肯定了
        @Override
        public void close() {//虽然可以关门，但这个动作不归我执行
            //do nothing
        }

        //这是在运行状态下要实现的方法
        @Override
        public void run() {
            System.out.println("电梯正在运行...");
        }

        //这个事绝对是合理的，光运行不停止还有谁敢做这个电梯？！估计只有上帝了
        @Override
        public void stop() {
            super.context.setLiftState(stoppingState);
            super.context.stop();
        }
    }

    //停止状态
    public class StoppingState extends LiftState {
        //停止状态，开门，那是要的！
        @Override
        public void open() {
            //状态修改
            super.context.setLiftState(openingState);
            //动作委托为CloseState来执行，也就是委托给了ClosingState子类执行这个动作
            super.context.getLiftState().open();
        }

        @Override
        public void close() {//虽然可以关门，但这个动作不归我执行
            //状态修改
            super.context.setLiftState(closingState);
            //动作委托为CloseState来执行，也就是委托给了ClosingState子类执行这个动作
            super.context.getLiftState().close();
        }

        //停止状态再跑起来，正常的很
        @Override
        public void run() {
            //状态修改
            super.context.setLiftState(runningState);
            //动作委托为CloseState来执行，也就是委托给了ClosingState子类执行这个动作
            super.context.getLiftState().run();
        }

        //停止状态是怎么发生的呢？当然是停止方法执行了
        @Override
        public void stop() {
            System.out.println("电梯停止了...");
        }
    }

    //关闭状态
    public class ClosingState extends LiftState {
        @Override
        //电梯门关闭，这是关闭状态要实现的动作
        public void close() {
            System.out.println("电梯门关闭...");
        }

        //电梯门关了再打开，逗你玩呢，那这个允许呀
        @Override
        public void open() {
            super.context.setLiftState(openingState);
            super.context.open();
        }

        //电梯门关了就跑，这是再正常不过了
        @Override
        public void run() {
            super.context.setLiftState(runningState);
            super.context.run();
        }

        //电梯门关着，我就不按楼层
        @Override
        public void stop() {
            super.context.setLiftState(stoppingState);
            super.context.stop();
        }
    }
}

```

### 5.3 环境类

```java
package state;

//环境角色
public class Context {

    //定义一个当前电梯状态
    private LiftState liftState;

    public LiftState getLiftState() {
        return this.liftState;
    }

    public void setLiftState(LiftState liftState) {
        //当前环境改变
        this.liftState = liftState;
        //把当前的环境通知到各个实现类中
        this.liftState.setContext(this);
    }

    public void open() {
        this.liftState.open();
    }

    public void close() {
        this.liftState.close();
    }

    public void run() {
        this.liftState.run();
    }

    public void stop() {
        this.liftState.stop();
    }
}
```

### 5.4 测试类

```java
public class Main {
    public static void main(String[] args) {
        Context context = new Context();
        StateMachine stateMachine = new StateMachine();
        context.setLiftState(stateMachine.runningState);
        context.open();
        context.close();
        context.run();
        context.stop();
    }
}
```

## 6.技术要点总结

- 必须要有一个Context类，这个类持有State接口，负责保持并切换当前的状态。
- 状态模式没有定义在哪里进行状态转换，本例是在具体的State类中转换

**当使用Context类切换状态时**，状态类之间互相不认识，他们直接的依赖关系应该由客户端负责。

**当使用具体的State类切换时**，状态直接就可能互相认识，一个状态执行完就自动切换到了另一个状态去了
