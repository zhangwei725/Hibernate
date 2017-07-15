我知道Jpa是一种规范，而Hibernate是它的一种实现。除了Hibernate，还有EclipseLink(曾经的toplink)，OpenJPA等可供选择，所以使用Jpa的一个好处是，可以更换实现而不必改动太多代码。

在play中定义Model时，使用的是jpa的annotations，比如javax.persistence.Entity, Table, Column, OneToMany等等。但它们提供的功能基础，有时候想定义的更细一些，难免会用到Hibernate本身的annotation。我当时想，jpa这 么弱还要用它干什么，为什么不直接使用hibernate的？反正我又不会换成别的实现。

因为我很快决定不再使用hibernate，这个问题就一直放下了。直到我现在在新公司，做项目要用到Hibernate。

我想抛开jpa，直接使用hibernate的注解来定义Model，很快发现了几个问题：

1. jpa中有Entity, Table，hibernate中也有，但是内容不同
2. jpa中有Column,OneToMany等，Hibernate中没有，也没有替代品

我原以为hibernate对jpa的支持，是另提供了一套专用于jpa的注解，但现在看起来似乎不是。一些重要的注解如Column, OneToMany等，hibernate没有提供，这说明jpa的注解已经是hibernate的核心，hibernate只提供了一些补充，而不是两 套注解。要是这样，hibernate对jpa的支持还真够足量，我们要使用hibernate注解就必定要使用jpa。

第一个是问如果想用hibernate注解，是不是一定会用到jpa的。网友的回答：“是。如果hibernate认为jpa的注解够用，就直接用。否则会弄一个自己的出来作为补充”

第二个是问，jpa和hibernate都提供了Entity，我们应该用哪个，还是说可以两个一起用？网友回答说“Hibernate的Entity是继承了jpa的，所以如果觉得jpa的不够用，直接使用hibernate的即可”。