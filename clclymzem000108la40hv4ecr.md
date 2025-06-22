---
title: "Design Patterns – Basics"
datePublished: Sat Jan 07 2023 13:06:43 GMT+0000 (Coordinated Universal Time)
cuid: clclymzem000108la40hv4ecr
slug: design-patterns-basics
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1673072243716/68edea86-e1ba-4a7a-bbb9-4b1a275ea825.png
tags: design-patterns, software-development, patterns, software-architecture, design-and-architecture

---

# What to expect from this series?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673056275983/56694746-7a28-4279-9d0d-0b6d9dfac378.png align="center")

Whether you are a newbie or an experienced programmer, you might have come across the term – “Software Design Patterns” many times in your career and would be actively using them knowingly or unknowingly in your code.

What are design patterns? How does it help a developer? How do we implement them? Well, this series here – [Design patterns simplified](https://artofcoding.dev/series/design-patterns) aims at answering these questions along with explaining various design patterns with code samples and practical scenarios on when to use which pattern.

I will keep this series as simple as possible for the beginners and concise enough for the ace programmers out there. I am not venturing to explain the basic object-oriented concepts further. However, knowing them would help you understand this series much better.

All the code samples throughout this series will be added (As each new part is added to the series) here -[https://github.com/artofcoding-dev/design-patterns](https://github.com/artofcoding-dev/design-patterns). They are written in C#, but I hope programmers in any other language would be able to understand them and use the concepts.

# What are Software Design Patterns?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673057142726/4a642e3d-7d7c-45d3-972b-1b161f213148.png align="center")

Ever got stuck thinking about how to solve a problem effectively while writing code? If your answer is “Yes”, then please realise that you are not the first person who faced the same kind of problem. Those who have faced them earlier, have already solved it for you. All you need to do is reuse them instead of trying to reinvent the wheel.

Design patterns could be considered as a general purpose abstraction of a problem. In simple words, they are general, reusable, tested solutions for specific recurring problems a developer faces in their day to day programming. When I mention solution, it is not a code snippet which is ready for reuse or copy-paste. Instead, it provides a framework or template for achieving your requirements in the best possible way. Similar to how a recipe helps you cook a dish, it helps you with the measures, approaches, and ingredients to come up with a better software.

# How does it help a developer?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673088234028/49bb72f6-1290-4e12-bbea-af64afcb1a8a.png align="center")

Here are some of the advantages developers could have by using design patterns.

## Improves code quality

Understanding design patterns will provide you with the required insights that would help you design a robust, flexible, scalable, loosely coupled, modular and reusable software.

Along with the obvious advantage of having a proven solution, it is also helpful in maintaining your code relatively clean, and resourceful in improving the quality of your documentation.

But also keep in mind that a good design is not the only ingredient of a good software. However good the design is, the quality of a software greatly depends on how the design is implemented as well. Having a good design will equip you in solving countless issues that may come up during the course of development, and a poor implementation can spoil the soup.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673091518137/03851783-0ef8-407c-b6e7-90a8c3c2e546.png align="center")

## Reduces Risk

Since the patterns are tried, tested and improved by developers over time, it reduces the element of risk and effort required in testing.

## Ease of communication and documentation

Design patterns are language neutral and could be applied to any object-oriented programming language. This makes things easy for developers to communicate efficiently, ensuring everyone is on the same page and helps them to visualise the design in their mind.

## Impact on productivity

Using design patterns reduces the time spent on maintaining the code and provides better consistency. Even though they have a positive impact on the development timeline, don't expect a drastic reduction on your initial development timelines. Figuring out the optimum design usually consumes time as it is required to account for the time needed to analyse the requirements, finalising the right tools and training the team if needed. But it will be beneficial in the long run, when you need to introduce changes to it or add to it later.

# How to find the right design patterns?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673057848064/91ea2383-d081-4043-947e-d4dd12e5ddea.png align="center")

There are plenty of design patterns available, and choosing the appropriate one would be a tough call to make initially. Remember, the key here is your requirements. You are trying to solve your problems with design patterns, and not the other way around. It should specifically address your requirements and, at the same time, maintain a general openness to account for future problems. It is quite natural for us to stick to the methods familiar to us for solving problems. Every so often, it may not be the optimal solution. What experienced programmers and designers do is not to prematurely solve the problems without giving it enough thought. Furthermore, it takes a lot of patience and practice, mastering the art of designing good software.

Example: If the problem is to cross a river. The following could be considered the solutions:

* Swimming across the river
    
* Using a boat
    
* Constructing a bridge across a river
    

All these solve the same problem. Swimming would solve it for one person, a boat would solve it for a few people at the same time, and a bridge would solve the issue for everyone. Considering the risks involved and other factors, a bridge would seem to be the ideal solution. But it would take longer to build and would cost more to implement. This would restrict people from travelling until the construction is completed. So here, the optimum solution would not be just to build the bridge. Instead, it will be a hybrid solution that involves constructing a bridge and using a boat until the bridge is constructed.

Blindly following a pattern would not be the best thing under certain situations. As developers, our primary focus should always be on the “***WHAT***” part instead of the ***“HOW”*** part of the problem. It enables us to solve the issues efficiently. In the example above, the focus should be on facilitating the travel rather than building the bridge (i.e. Implementing the pattern) which will lead us to the hybrid approach.

Another word of caution for the developers would be to avoid the overuse of design patterns. It is always good to remember YAGNI principle([https://artofcoding.dev/14-principles-every-developer-should-know#heading-7-you-arent-gonna-need-it-yagni](https://artofcoding.dev/14-principles-every-developer-should-know#heading-7-you-arent-gonna-need-it-yagni)) and find the right balance.

# Types/Categories of Design Patterns

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673058310212/ef92320c-c23c-4ce0-b057-3ac40a808761.png align="center")

Design patterns could be broadly classified under 3 categories based on their purpose:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673092594521/c0d541f7-4725-4071-b884-16f9d5077dac.jpeg align="center")

## Behavioral patterns

These patterns focus more on the effective communication and interaction between various components and objects and the assignment of responsibilities.

Example: Consider a restaurant where there is a chef, a waiter, and clients. The responsibility of the chef is to cook, the waiter takes and executes the orders and clients places orders and makes payment. The client and the chef need not see or communicate directly. But there in an indirect, effective communication happening through the waiter.

Few of the prominent behavioral patterns are:

* Iterator pattern
    
* Observer pattern
    
* Chain of responsibility pattern
    
* Command pattern
    
* Visitor pattern
    
* Strategy pattern
    
* Interpreter pattern
    
* Mediator pattern
    

## Structural patterns

Structural design patterns focus more on the template for defining the structure and components of a class. It helps the programmers to organise the classes and objects and helps in ensuring efficiency and flexibility of the component/software.

Some popular structural patterns are:

* Adapter pattern
    
* Facade
    
* Decorator
    
* Proxy
    

## Creational patterns

Creational design patterns focus more on object creation and initialisation. It empowers the developer in deciding which object is to be created in a specified scenario.

Example: A developer wants to log errors in the application, and decides to use a single logger instance.

Well known creational design patterns are:

* Abstract Factory Patterns
    
* Builder Pattern
    
* Factory Method Pattern
    
* Prototype Pattern
    
* Singleton Pattern
    

There are more design patterns in these categories. We can dig more of them in the upcoming articles. Your support and love in the form of comments and feedback are highly appreciated. Thanks.

# **Appendix**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673081312914/8b82c233-7173-48a3-aafe-66fe956a59f7.png align="center")

It would be a crime if I don't mention a book – “**Design Patterns: Elements of Reusable Object-Oriented Software**” written by Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides in 1994, which could be considered “The Bible” of Design Patterns. If I am asked to pick only one book on software architecture and design, it would be this book. The authors of this book are often referred to as Gang of Four(GoF). If you are into C++ or love to go deeper, I highly recommend this book; not just as a one-time read, but as a reference material as well.

### **Shout-outs and courtesy notes**

A big shout-out to the following photographers for their pictures which I could use from Unsplash and for [https://miro.com/](https://miro.com/) for helping me draw the diagrams.

Photo by [Anita Denunzio](https://unsplash.com/@dronepilot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/AMIiKN-4oVE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Rhys Kentish](https://unsplash.com/@rhyskentish?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/TFgLjR9TAEM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Austin Kehmeier](https://unsplash.com/@a_kehmeier?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/lyiKExA4zQA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Matt Ridley](https://unsplash.com/@mattwridley?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/Lyl8RL7imrw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Nathan Dumlao](https://unsplash.com/@nate_dumlao?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/pMW4jzELQCw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Alexander Grey](https://unsplash.com/@sharonmccutcheon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/tn57JI3CewI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Enis Yavuz](https://unsplash.com/@enisyavuz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/tuuh4DrgMpU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  
Photo by [Shardar Tarikul Islam](https://unsplash.com/@tarikul_islam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/G1lNoSED98A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)[  
](https://unsplash.com/@mattwridley)

[  
](https://unsplash.com/@rhyskentish)