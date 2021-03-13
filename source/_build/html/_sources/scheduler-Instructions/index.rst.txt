======================
搭建一个任务
======================

**了解完上面的内容后，让我们搭建一个任务吧** 

搭建人物的基本流程如下:

- 设置 **数据源设置** ，设置要操作的数据设置SQL.
- 设置 **数据中心** ，设置要操作的数据库.
-  **插件开发** ，根据 **数据源设置** 设置的单据类型，来实现 ``IMyJob`` 或者 ``IMyMultiJob`` 接口.
- 设置 **数据源JSON格式** .
- 设置 **任务计划设置** ，配置任务调度的调度周期、数据源、插件.
- 去 **系统参数** 开启日志，启动任务，查看任务的运行结果.

本示例通过一个简单的单表应用来介绍上述步骤. 一旦你学会了这些基础，你可以查看 :doc:`任务计划设置 <../scheduler-tasksetting/tasksetting>` 来了解更多用法.

该应用主要通过插件打印 ``users`` 表重的数据

创建调度计划
===========================

1、 **数据源设置** ，详情请查看 :doc:`数据源设置 <../scheduler-tasksetting/datasourcesetting>` 来了解更多用法

.. image:: ./demo/dssetting-demo.png
.. image:: ./demo/dssetting-demo1.png
.. image:: ./demo/tableresult-demo.png
.. image:: ./demo/tableresult-demo1.png

.. note::
    注意如果想要调用插件后，自动执行 **成功后执行的sql** 和 **失败后执行的sql** 的SQL语句，需要去  :doc:`系统参数 <../scheduler-tasksetting/systemprofile>` 将 ``IsPlugExecSql`` 设置成 ``0``


2、创建 ``.Net Core 3.1`` 类库，添加如下引用：

* ``AdminWeb.Core.Common`` ：提供了公共方法（比如：加密、http请求、文件处理等）
* ``AdminWeb.Core.DbHelper`` ：提供了数据库访问的帮助类
* ``AdminWeb.Core.Model``：提供了部分Model模型
* ``AdminWeb.Core.TaskJob`` ：任务作业（含打印日志、任务接口等）
* ``Microsoft.Extensions.Configuration.Abstractions`` ：该组件抽象了.NET Core的配置功能,并对自定义扩展制定了新的标准
* ``Microsoft.Extensions.Configuration.Binder`` ：可以使用选项模式将文件配置绑定到相关实体类
* ``Newtonsoft.Json`` ：.Net中开源的Json序列化和反序列化工具

.. image:: ./demo/createdemo.png
.. image:: ./demo/createdemo1.png



3、插件代码：

详情请查看 :doc:`插件开发 <../scheduler-tasksetting/tasksetting>` 来了解更多用法

.. sourcecode:: csharp

    using AdminWeb.Core.Common.BaseConvert;
    using AdminWeb.Core.Model.ParaModels;
    using AdminWeb.Core.TaskJob;
    using AdminWeb.Core.TaskJob.Interface;
    using System;
    using System.Data;
    using System.Threading.Tasks;

    namespace JobPlugDemo
    {
        public class ERPTransfer: IMyJob
        {
            private static readonly LogHelper _logHelper = new LogHelper();

            /// <summary>
            /// 通过Api发送
            /// </summary>
            /// <param name="paras">作业参数</param>
            /// <param name="dsData">数据</param>
            /// <param name="errorMsg">错误信息</param>
            /// <returns></returns>
            public async Task<Tuple<bool, string>> ExecuteJob(JobPara jobpara, DataSet ds)
            {
                string guid = jobpara.JobDetailId;
                string key = string.Empty;
                string jsonErrorMsg = string.Empty;
                try
                {
                    _logHelper.Debug(jobpara, "调用主任务：{jobpara.JobId}.{jobpara.JobName}，子任务：{jobpara.JobDetailId}.{jobpara.JobDetailName}");
                    DataTable dt = ds.Tables["Head"];
                    _logHelper.Debug(jobpara, "数据条数：" + dt.Rows.Count);
                    string resultJson = string.Empty;
                    //根据JSON格式配置中已经配置好的格式来转化json
                    resultJson = DataColumnRelationToJson.GetJson(guid, dt, key, ref jsonErrorMsg, isTimestamp: false);
                    _logHelper.Debug(jobpara, "转化后的json：" + resultJson);
                    if (jsonErrorMsg.Length == 0)
                    {
                        #region 业务逻辑
                        _logHelper.Debug(jobpara, "开始处理业务逻辑");
                        await Task.Delay(1000);
                        _logHelper.Debug(jobpara, "处理完毕");
                        #endregion
                        return new Tuple<bool, string>(true, "");
                    }
                    else
                    {
                        return new Tuple<bool, string>(false, "ConvertJsonError:" + jsonErrorMsg);
                    }
                }
                catch (Exception ex)
                {
                    return new Tuple<bool, string>(false, ex.Message);
                }
            }
        }
    }

4、编译插件并拷贝到 ``Handlers`` 文件夹下

.. image:: ./demo/plugin-handlers-demo.png

5、设置 **数据中心** 

以一个 ``MySql`` 的数据库为例，详情请查看 :doc:`数据中心设置 <../scheduler-tasksetting/dbsetting>` 来了解更多用法

.. image:: ./demo/dbsetting-demo.png

6、设置 **数据源JSON格式**

选择要生成json格式的数据源，点击 **一键生成数据列** ，修改完后，点击 **保存** 即可，详情请查看 :doc:`数据源JSON格式设置 <../scheduler-tasksetting/jsonsetting>` 来了解更多用法

.. image:: ./demo/jsonsetting-demo.png
.. image:: ./demo/jsonsetting-demo1.png

7、 **任务计划设置** ，详情请查看 :doc:`任务计划设置 <../scheduler-tasksetting/tasksetting>` 来了解更多用法

.. image:: ./demo/tasksetting-demo.png
.. image:: ./demo/tasksetting-demo1.png

.. note::
    尤其需要注意 **数据中心的选择** 、 **选择数据源** 以及 **插件的配置**

8、 **启动任务** 

.. image:: ./demo/taskstart-demo.png
.. image:: ./demo/taskstart-demo1.png

9、查看 **日志** ，查看任务的运行状态

.. image:: ./demo/tasklog-demo.png

.. note::
    前提是需要开启打印日志的参数，详情请看 :doc:`系统参数中的IsDebugLog参数 <../scheduler-tasksetting/systemprofile>` 来了解开启日志的方法

10、查看执行后的数据

.. image:: ./demo/tableresult-demo2.png

.. note::
    如果日志显示已成功或者失败，但是 **成功后执行的sql** 和 **失败后执行的sql** 的SQL语句没有执行，需要去  :doc:`系统参数 <../scheduler-tasksetting/systemprofile>` 将 ``IsPlugExecSql`` 是否设置成了 ``1``，设置成 ``0`` 才可以





  