# 重复提交

此处重点指前端的重复提交

## 

## 前端减少重复操作

我反对使用Aborted,如果跟后台连接时aborted到还好,如果后台已经收到了请求,并且正在运算,此时只是前端单方面中断连接,后台依然需要计算,其实还是重复请求,前端不接受而已

## 后台重复验证

提前提供id,将创建,改写为"修改",防止一对一的关系转换为一对多

ssh系列的token防重复提交,指的是该系列

缓存系列与数据库平级,不讨论

其他情况并不处理...


