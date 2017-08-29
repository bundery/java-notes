# Spring声明式事务管理

## Spring-Hibernate事务
事务适合放在Service层，因为一个Service方法可能包括多个`xxDao`，如果事务放在Dao层的方法上面，则那个Service方法就无法进行单个事务管理了。


