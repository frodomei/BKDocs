
### 请求方法

GET


### 请求地址

/api/c/compapi/v2/sops/get_periodic_task_list/


### 通用参数

| 字段 | 类型 | 必选 |  描述 |
|-----------|------------|--------|------------|
| bk_app_code  |  string    | 是 | 应用ID     |
| bk_app_secret|  string    | 是 | 安全密钥(应用 TOKEN)，可以通过 蓝鲸智云开发者中心 -> 点击应用ID -> 基本信息 获取 |
| bk_token     |  string    | 否 | 当前用户登录态，bk_token与bk_username必须一个有效，bk_token可以通过Cookie获取 |
| bk_username  |  string    | 否 | 当前用户用户名，应用免登录态验证白名单中的应用，用此字段指定当前用户 |


### 功能描述

查询某个业务下所有的周期任务

### 请求参数

#### 接口参数

| 字段          |  类型       | 必选   |  描述             |
|---------------|------------|--------|------------------|
|   bk_biz_id    |   string     |   是   |  任务所属业务ID |
| scope | string | 否 | bk_biz_id 检索的作用域。默认为 cmdb_biz，此时检索的是绑定的 CMDB 业务 ID 为 bk_biz_id 的项目；当值为 project 时则检索项目 ID 为 bk_biz_id 的项目|

### 请求参数示例

```
{
    "bk_app_code": "esb_test",
    "bk_app_secret": "xxx",
    "bk_token": "xxx",
    "bk_username": "xxx",
    "bk_biz_id": "2",
    "scope": "cmdb_biz"
}
```

### 返回结果示例

```
{
    "data": [
        {
            "cron": "*/1 15 * * * (m/h/d/dM/MY)",
            "total_run_count": 1,
            "name": "from api 3",
            "creator": "admin",
            "last_run_at": "2018-11-28 15:57:01 +0900",
            "enabled": false,
            "id": 11,
            "template_id": "2",
            "auth_actions": [
                "periodic_task_view",
                "periodic_task_delete",
                "periodic_task_edit"
            ]
        },
        {
            "cron": "1,2,3-19/2 2 3 4 5 (m/h/d/dM/MY)",
            "total_run_count": 0,
            "name": "from api 1",
            "creator": "admin",
            "last_run_at": "",
            "enabled": false,
            "id": 6,
            "template_id": "2",
            "auth_actions": [
                "periodic_task_view",
                "periodic_task_delete",
                "periodic_task_edit"
            ]
        },
        {
            "cron": "*/5 * * * * (m/h/d/dM/MY)",
            "total_run_count": 0,
            "name": "定时",
            "creator": "admin",
            "last_run_at": "",
            "enabled": false,
            "id": 4,
            "template_id": "2",
            "auth_actions": [
                "periodic_task_view",
                "periodic_task_delete",
                "periodic_task_edit"
            ]
        }
    ],
    "result": true,
    "request_id": "xxx",
    "trace_id": "xxx"
}
```

### 返回结果参数说明

|   名称   |  类型  |           说明             |
| ------------ | ---------- | ------------------------------ |
|  result      |    bool    |      true/false 操作是否成功     |
|  data        |    dict      |      result=true 时成功数据，详细信息请见下面说明     |
|  message        |    string      |      result=false 时错误信息     |
|  request_id     |    string  |      esb 请求 id     |
|  trace_id     |    string  |      open telemetry trace_id     |


#### data

|   名称   |  类型  |           说明             |
| ------------ | ---------- | ------------------------------ |
|  cron      |    string    |      周期调度表达式    |
|  total_run_count      |    int    |    周期任务运行次数   |
|  name      |    string    |    周期任务名   |
|  creator      |    string    |    创建者   |
|  last_run_at      |    string    |    上次运行时间   |
|  enabled      |    bool    |    是否激活   |
|  id      |    int    |    周期任务ID   |
|  template_id      |    string    |    用于创建该任务的模板ID   |
|  auth_actions      |    array   |      用户对该资源有权限的操作   |