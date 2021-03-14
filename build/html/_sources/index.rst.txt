.. AutoDataProcessDocs documentation master file, created by
   sphinx-quickstart on Wed Mar  3 14:32:37 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

欢迎来到 数据自动处理工具 文档!
===============================================

数据自动处理工具大致分为 **调度平台** 和 **API平台** 

**作用及应用场景** ：
==========================

* **调度平台** ：主动处理业务逻辑。根据设置的调度任务作业来主动执行插件逻辑，不需要人为手动去操作，用于自定义作业
* **API平台** ：被动处理业务逻辑。调用方调用API平台中的api，被动执行插件逻辑，需要有外力来调用，用于api的开发

.. toctree::
   :maxdepth: 2
   :caption: 调度平台

   启动项目 <scheduler-start/index.rst>
   任务搭建及插件开发 <scheduler-tasksetting/index.rst>
   搭建一个任务示例 <scheduler-Instructions/index.rst>
   安装部署 <scheduler-deploy/index.rst>

.. toctree::
   :maxdepth: 2
   :caption: API平台

   启动项目 <api-start/index.rst>
   接口搭建 <api-setting/index.rst>
   使用说明 <api-Instructions/index.rst>
   安装部署 <api-deploy/index.rst>

.. toctree::
   :maxdepth: 2
   :caption: ETL工具

   简介 <etl-start/index.rst>
   安装 <etl-start/index.rst>
   使用说明 <etl-start/index.rst>

