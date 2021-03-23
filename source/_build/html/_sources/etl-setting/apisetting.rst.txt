===========================
启动前准备（API平台）
===========================

数据中心设置
===========================
维护对应的 :doc:`数据中心 <../scheduler-tasksetting/dbsetting>` ，维护的数据中心作为目标数据库，负责处理调度中心传过来的数据。

.. image:: ./setting/apidatacenter1.png
.. image:: ./setting/apidatacenter.png

维护路由
===========================

维护API的路由，与调度平台的数据源设置内设置的连接字符串的 ``apiurl`` 一致即可，只有互相匹配，调度拉取的数据才会准确的找到要调用的API

.. image:: ./setting/routing.png

如果我们要新增一个路由 ``/api/ordertransfer`` 我们可以这样做：

.. image:: ./setting/addapi1.png
.. image:: ./setting/addapi2.png
.. image:: ./setting/addapi3.png
.. image:: ./setting/addapi4.png

修改ETL配置文件
===========================

最后一步，修改 ``etjapi.json`` 

.. image:: ./setting/apietljson1.png

.. image:: ./setting/apiconfigsetting1.png
.. image:: ./setting/apiconfigsetting2.png

- ``备注``：体现方法含义即可
- ``apiTag``：方法， **对应调度平台数据源设置设置的连接字符串的method** ， ``etlapi.json`` 内配置的 ``apiTag``  **不能重复**
- ``uniqueCheckSql``：检测数据的唯一性，**如果考虑到效率问题无需设置** ，如 ``select count(*) num from users_server where userid='{$main.userid$}'``

.. note::
    如果设置了批量执行，如 ``insert into users_server (userid,username,usersex) values [$ 插入值 $]`` ，带有 ``[$ $]`` 这种格式的表示按照批量执行，设置的 ``uniqueCheckSql`` 无效

    **批量执行时，各个数据库的执行方式不一样** 

    MySql和SqlServer按照 ``insert into 列 values (值1),(值2),(值3)`` 执行；
    
    Oracle按照 ``insert into 列 select 值1 from dual union all select 值2 from dual``  

    当然在ETL中配置的方式是一样的，都是通过 ``[$ $]`` 这种格式设置

- ``mainSql``：执行 **主数据源** 中查出来的数据，配置多语句需要用 ``;`` 隔开
- ``detailSql``：执行 **明细数据源** 中查出来的数据，配置多语句需要用 ``;`` 隔开
.. note::
    如上图中我们看到的 ``{$main.字段$}`` 和图中没有展示到的 ``{detail.字段}`` ，分别对应的其实是在 **调度平台设置** 的 :doc:`数据源JSON格式设置 <../scheduler-tasksetting/jsonsetting>` 的 ``data层`` 和 ``items层`` 
    
    因为 **单表数据源** 只有 ``data层`` ，所以 ``mainsql`` 必填，我们可以在 ``mainsql`` 中使用 ``{main.data层对应的字段}`` 进行替换；
    
    而 **多表数据源** 既有 ``data层`` 也有 ``items层`` ，所以我们可以在 ``detailsql`` 中使用 ``{detail.items层对应的字段}`` 进行替换，值得注意的是，我们也可以在 ``detailsql`` 中使用 ``{main.data层对应的字段}`` 进行替换，反之， ``mainsql`` 中不能使用 ``items层`` 的字段

- ``callProc``：执行完上面语句后，可以紧跟着执行一个存储过程，参数也可以用传过来的 ``data层`` 和  ``items层`` 替换即可。
.. note::
    值得注意的是，如果 ``callProc`` 只能使用 ``data层`` 的字段 ``{main.data层对应的字段}`` 进行入参参数替换，执行存储过程的次数会等于 ``mainsql`` 执行成功的次数

- ``httpRequest``：该参数的作用主要是可以在上面的语句（语句需要执行成功或者部分成功才会执行）和过程执行完毕后来执行回调，目前回调只针对回调 **调度平台的任务启动和停止** 。 **启动的url** 为 ``http:地址:端口/api/executejob`` ，参数为：``{\"ID\":70,\"IsEnableCron\":false}`` ；**停止的url** 为 ``http:地址:端口/api/stopjob`` ，参数为：``{\"ID\":70,\"IsEnableCron\":false}``
- ``httpRequestParams``：回调任务启动还是停止的参数，按照json格式设置 
.. note::
    参数含义：

    ``ID`` ：对应调度任务的ID，如下图

    ``IsEnableCron``：是否按照任务计划设置的频率开始执行，``true`` 则 **相当于点击该任务的启动按钮** ，``false`` 指的是 **仅触发一次** （调用调用任务停止的接口 ``/api/stopjob`` 的 ``IsEnableCron`` 参数为 ``false`` 即可）

.. image:: ./setting/httprequest.png

验证ETL配置文件
===========================

最后我们需要对设置的JSON需要检测格式是否正确，可以去  `验证JSON <http://www.bejson.com/>`_，保证配置的正确性，如果没有什么问题，保存即可

.. image:: ./setting/configfile4.png
    


