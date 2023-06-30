会话结束的时机？

tcm.getObject()不是从暂存区中拿缓存信息，而是从二级缓存区中拿数据?

暂存区中只存变更了的数据



Mapper --> MapperProxy --> MapperMethod --> ParamNameResolver --> SqlSession --> Executor

​                  mapper动态代理  转换参数和结果       java参数转换成sql参数                          执行器

--> BaseStatementHandler --> PreparedStatementHandle --> ParameterHandle --> ResultHandle

​        基础Statement处理器              预处理处理器                          参数处理器            结果处理器















