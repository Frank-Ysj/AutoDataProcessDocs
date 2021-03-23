启动前准备（调度平台）
===========================

定义SQL，如下图定义取数据和回填状态的SQL：

.. image:: ./setting/etl-dssetting.png

.. note::
    对于一些常量，可以使用 :doc:`关键字 <../scheduler-tasksetting/systemvariables>` 的形式来替换数据源的SQL语句，如上图主数据源的 **[OWNERID]** 