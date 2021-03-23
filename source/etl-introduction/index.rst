======================
ETL安装
======================

ETL，是英文 **Extract-Transform-Load** 的缩写，用来描述将数据从来源端经过 **抽取（extract）、转换（transform）、加载（load）** 至目的端的过程。

ETL能够通过 *客户端* 调度作业将数据从 **源数据库** 抽取出来，通过将数据通过插件 ``RomensCloudETLScheduler.dll`` 内部转换为 ``JSON`` ，而后基于http协议传递数据给 *服务端* API平台，服务端再将数据写入 **目标数据库**，主要流程如下：

- 设置 ``RomensCloudETLScheduler`` **调度服务** 的 :doc:`数据中心 <../scheduler-tasksetting/dbsetting>` 
- 设置 ``RomensCloudETLScheduler`` **调度平台** 的 :doc:`数据源 <../scheduler-tasksetting/datasourcesetting>` ，维护对应的SQL
- 设置 ``RomensCloudETLScheduler`` **调度平台** 的 :doc:`数据源的JSON格式 <../scheduler-tasksetting/jsonsetting>` ，控制传输格式
- 创建 ``RomensCloudETLScheduler`` **调度平台** 的 :doc:`任务计划 <../scheduler-tasksetting/tasksetting>` 
.. note::
    注意 ``IJOB实现类`` ：

    绑定的 **数据源设置** 都为单表，则 ``IJOB实现类`` 设置为 ``RomensCloudETLScheduler.ETLSingleTableJob`` ；
    绑定的 **数据源设置** 都为多表，则 ``IJOB实现类`` 设置为 ``RomensCloudETLScheduler.ETLMultiTableJob`` 。

- 设置 ``RomensCloudETLService`` **API服务** 的方法、配置执行SQL语句
- 启动 ``RomensCloudETLScheduler`` **调度服务** 的任务，并 :doc:`查看调度日志 <../scheduler-tasksetting/tasklog>` 

 **工具逻辑如下：**

.. image:: ./introductionimg/ETL-Process.png


