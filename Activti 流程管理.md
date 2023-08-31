# Activti流程管理





```java
Activti提供的主要的接口服务，其中主要用到
  
存储库服务（repositoryService）：用于管理和操作流程定义和流程部署相关的数据。它提供了一系列方法来查询、创建、更新和删除流程定义、流程部署以及相关的资源文件。

运行时服务（runtimeService）：用于管理和操作正在执行的流程实例和任务。它允许启动新的流程实例、对流程实例进行操作（如激活、挂起、删除等）、获取当前正在运行的流程实例信息等操作。

任务服务（taskService）：用于管理和操作与用户任务相关的数据和操作。它允许查询、创建、更新和删除任务，以及对任务进行分配、完成、指派等操作。

历史服务（historyService）：用于访问和查询与历史数据相关的信息。它提供了查询已完成流程实例、任务、变量等历史数据的能力。通过历史服务，可以获取对流程的审计跟踪和回顾，以及生成历史报告和统计数据。
  
//存储库服务
<bean id="repositoryService" factory-bean="processEngine" factory-method="getRepositoryService" />
//运行时服务
<bean id="runtimeService" factory-bean="processEngine" factory-method="getRuntimeService" />
//表单服务
<bean id="formService" factory-bean="processEngine" factory-method="getFormService" />
//身份服务
<bean id="identityService" factory-bean="processEngine" factory-method="getIdentityService" />
//任务服务
<bean id="taskService" factory-bean="processEngine" factory-method="getTaskService" />
//历史服务
<bean id="historyService" factory-bean="processEngine" factory-method="getHistoryService" />
//管理服务
<bean id="managementService" factory-bean="processEngine" factory-method="getManagementService" />
```

## 1、存储库服务 repositoryService

### 1.全部流程实例激活挂起

激活：

```java
activateProcessDefinitionById(,,)
```

根据流程定义的ID激活流程定义。
第一个参数  是要激活的流程定义的唯一标识符。
第二个参数 `true` 表示级联激活，即同时激活与该流程定义关联的所有挂起的流程实例。
第三个参数 `null` 表示没有要激活的特定日期。

挂起：

```java
suspendProcessDefinitionById(,,)
```

根据流程定义的ID挂起流程定义。
第一个参数  是要挂起的流程定义的唯一标识符。
第二个参数 `true` 表示级联挂起，即同时挂起与该流程定义关联的所有正在执行的流程实例。
第三个参数 `null` 表示没有要挂起的特定日期。

示例：

```java
    /**
     * 全部流程实例挂起
     */
    @Test
    public void suspendProcessInstance() {
        ProcessDefinition leave = repositoryService.createProcessDefinitionQuery().processDefinitionKey("leave").singleResult();
        // 获取到当前流程定义是否为暂停状态 suspended方法为true是暂停的，suspended方法为false是运行的
        boolean suspended = leave.isSuspended();
        if (suspended) {
            // 暂定,那就可以激活
            // 参数1:流程定义的id  参数2:是否激活    参数3:时间点
            //多个流程激活
            repositoryService.activateProcessDefinitionById(leave.getId(), true, null);
            System.out.println("流程定义:" + leave.getId() + "激活");
        } else {
            //多个流程挂起
            repositoryService.suspendProcessDefinitionById(leave.getId(), true, null);
            System.out.println("流程定义:" + leave.getId() + "挂起");
        }
    }
```

### 2.删除流程实例

单个删除


```java
deleteDeployment()
```

根据流程部署的ID删除相应的流程定义和资源。如果该流程定义已经有流程实例启动，则会删除失败。
第一个参数  是流程ID

级联删除

```java
deleteDeployment(,true)
```

设置为级联删除方式，即使流程定义已经有流程实例启动，也会强制删除流程定义及其相关的流程实例和历史数据。
第一个参数  是流程ID
第二个参数  true  表示开启级联删除

示例：

```java
  /**
     * 删除流程定义
     */
    public void deleteDeployment() {
        //部署id
        String deploymentId = "ce1f3cc0-08cf-11ee-b8cb-e645a9a03302";
        //删除流程定义，如果该流程定义已有流程实例启动则删除时出错
        repositoryService.deleteDeployment(deploymentId);
        //设置true 级联删除流程定义，即使该流程有流程实例启动也可以删除，设置为false非级别删除方式
        repositoryService.deleteDeployment(deploymentId, true);
    }
```

### 3.查询流程实例

```java
createProcessDefinitionQuery()
```

创建一个流程定义查询对象，用于构建查询条件。

```java
orderByProcessDefinitionVersion()
```
按照流程定义的版本进行排序。

```java
desc()
```

降序排列，即最新的版本排在前面。

```java
list()
```

执行查询，并返回符合条件的流程定义结果列表。

示例：

```java
    /**
     * 查询流程定义
     */
    @Test
    public void findProcessDefinitionList() {
        List<ProcessDefinition> definitionList = repositoryService.createProcessDefinitionQuery()
                .orderByProcessDefinitionVersion()
                .desc()
                .list();
        //输出流程定义信息
        for (ProcessDefinition processDefinition : definitionList) {
            System.out.println("流程定义 id= " + processDefinition.getId());
            System.out.println("流程定义 name= " + processDefinition.getName());
            System.out.println("流程定义 key= " + processDefinition.getKey());
            System.out.println("流程定义 Version= " + processDefinition.getVersion());
            System.out.println("流程部署ID = " + processDefinition.getDeploymentId());
        }
    }
```

### 4.单个文件部署

```java
createDeployment()
```
创建一个流程部署对象，用于部署流程定义和相关资源。
```java
addClasspathResource("")
```
从类路径中添加一个 BPMN 文件资源到部署中，路径为 ""。该文件包含了请假申请流程的流程定义。
参数  是bpmn文件路径

```java
addClasspathResource("")
```
从类路径中添加一个 PNG 文件资源到部署中，路径为 ""。该文件用于流程定义的可视化展示。
参数  是png文件路径

```java
name("")
```
设置流程部署的名称为 ""。
参数  流程部署名称

```java
deploy()
```
执行部署操作，将流程定义和资源文件部署到流程引擎中。

示例：

```java
    /**
     * 单个文件部署
     */
    @Test
    public void deployProcess() {
        // 流程部署
        Deployment deploy = repositoryService.createDeployment().addClasspathResource("process/leave.bpmn20.xml").addClasspathResource("process/leave.png").name("请假申请流程").deploy();
        System.out.println(deploy.getId());
        System.out.println(deploy.getName());
    }
```

## 2、运行时服务 runtimeService

### 1.启动流程实例

```java
startProcessInstanceByKey("")
```

按照流程定义的名称（key）启动一个流程实例。流程定义的键通常是在部署流程时设置的唯一标识符。
参数  是流程的名称

示例：

```java
/**
     * 启动流程实例
     */
    @Test
    public void startUpProcess() {
        //创建流程实例,我们需要知道流程定义的key
        ProcessInstance processInstance = runtimeService.startProcessInstanceByKey("leave");
        //输出实例的相关信息
        System.out.println("流程定义id：" + processInstance.getProcessDefinitionId());
        System.out.println("流程实例id：" + processInstance.getId());
        System.out.println("当前活动Id：" + processInstance.getActivityId());
    }
```

### 2.单个流程实例激活挂起

激活实例流程：

```java
activateProcessInstanceById()
```

挂起实例流程：

```java
suspendProcessInstanceById()
```

示例：

```java
    /**
     * 单个流程挂起
     */
    @Test
    public void SingleSuspendProcessInstance() {
        String processInstanceId = "ce1f3cc0-08cf-11ee-b8cb-e645a9a03302";
        ProcessInstance processInstance = runtimeService.createProcessInstanceQuery().processInstanceId(processInstanceId).singleResult();
        //获取到当前流程定义是否为暂停状态   suspended方法为true代表为暂停   false就是运行的
        boolean suspended = processInstance.isSuspended();
        if (suspended) {
            //单个流程激活
            runtimeService.activateProcessInstanceById(processInstanceId);
            System.out.println("流程实例:" + processInstanceId + "激活");
        } else {
            //单个流程挂起
            runtimeService.suspendProcessInstanceById(processInstanceId);
            System.out.println("流程实例:" + processInstanceId + "挂起");
        }
    }
```

### 3.启动实例流程

```
startProcessInstanceByKey("", )
```

启动一个流程实例
第一个参数  是启动的流程定义的键（流程名称）
第二个参数  可选参数，表示与流程实例相关联的业务关键字

示例：

```java
    /**
     * 启动流程实例，添加businessKey
     */
    @Test
    public void startUpProcessAddBusinessKey() {
        String businessKey = "1";
        // 启动流程实例，指定业务标识businessKey，也就是请假申请单id
        ProcessInstance processInstance = runtimeService.
                startProcessInstanceByKey("leave", businessKey);
        // 输出
        System.out.println("业务id:" + processInstance.getBusinessKey());
    }
```

### 4.查询流程定义



```java
runtimeService.createProcessInstanceQuery()
```

创建流程实例查询对象

```java
processInstanceId()
```
设置流程实例ID为查询条件
参数  是流程实例ID

```java
singleResult()
```

执行查询并返回单个结果，即符合条件的唯一流程实例




```java
    /**
     * 单个流程挂起
     */
    @Test
    public void SingleSuspendProcessInstance() {
        String processInstanceId = "ce1f3cc0-08cf-11ee-b8cb-e645a9a03302";
        ProcessInstance processInstance = runtimeService.createProcessInstanceQuery().processInstanceId(processInstanceId).singleResult();
        //获取到当前流程定义是否为暂停状态   suspended方法为true代表为暂停   false就是运行的
        boolean suspended = processInstance.isSuspended();
        if (suspended) {
            //单个流程激活
            runtimeService.activateProcessInstanceById(processInstanceId);
            System.out.println("流程实例:" + processInstanceId + "激活");
        } else {
            //单个流程挂起
            runtimeService.suspendProcessInstanceById(processInstanceId);
            System.out.println("流程实例:" + processInstanceId + "挂起");
        }
    }
```



## 3、任务服务 taskService

### 1.完成任务

```java
complete()
```

用于完成当前用户任务的方法
参数  任务ID

示例：

```java
    /**
     * 完成任务
     */
    @Test
    public void completTask() {
        //要查询的负责人
        Task task = taskService.createTaskQuery().taskAssignee("zhangsan").singleResult();//返回一条

        //完成任务,参数：任务id
        taskService.complete(task.getId());
    }
```

### 2.查询当前个人待执行任务

```java
createTaskQuery
```

创建任务查询对象

	taskAssignee(assignee)

设置任务负责人的查询条件
参数  是查询的任务负责人的名称

```
list()
```

执行查询操作并返回查询结果

示例：

```java
    /**
     * 查询当前个人待执行的任务 zhangsan
     */
    @Test
    public void findPendingTaskList() {
        //任务负责人
        String assignee = "zhangsan";
        List<Task> list = taskService.createTaskQuery()
          			.taskAssignee(assignee)//只查询该任务负责人的任务
                .list();
        for (Task task : list) {
            System.out.println("流程实例id：" + task.getProcessInstanceId());
            System.out.println("任务id：" + task.getId());
            System.out.println("任务负责人：" + task.getAssignee());
            System.out.println("任务名称：" + task.getName());
        }
    }
```

## 4、历史服务 historyService

### 1.查询已处理任务

```java
createHistoricTaskInstanceQuery()
```

创建一个历史任务实例查询对象，用于执行历史任务的查询操作


```java
taskAssignee("")
```

设置查询条件，只返回指定负责人的历史任务
参数  是指定负责人的名称


```java
finished()
```

只返回已完成的历史任务


```java
list()
```

执行查询操作并返回查询结果

示例：

```java
    /**
     * 查询已处理历史任务
     */
    @Test
    public void findProcessedTaskList() {
        //张三已处理过的历史任务
        List<HistoricTaskInstance> list = historyService.createHistoricTaskInstanceQuery().taskAssignee("zhangsan").finished().list();
        for (HistoricTaskInstance historicTaskInstance : list) {
            System.out.println("流程实例id：" + historicTaskInstance.getProcessInstanceId());
            System.out.println("任务id：" + historicTaskInstance.getId());
            System.out.println("任务负责人：" + historicTaskInstance.getAssignee());
            System.out.println("任务名称：" + historicTaskInstance.getName());
        }
    }
```
