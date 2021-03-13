========================================
系统变量
========================================

通常有一些常量，我们会在 :doc:`数据源设置 <../scheduler-tasksetting/datasourcesetting>` 中大量使用，比如公司号之类的，为此，增加本模块，可以根据此处定义的变量来替换 :doc:`数据源设置 <../scheduler-tasksetting/datasourcesetting>` 中定义的SQL值

.. image:: ./systemimg/systemvariables.png

.. image:: ./systemimg/systemvariables1.png

:doc:`数据源设置 <../scheduler-tasksetting/datasourcesetting>` 中定义的SQL使用 ``[@系统变量的键@]`` 来替换即可