# 升级部署说明

## 背景

文档主要说明 IAM 某些版本做了较大的变更, 导致升级前需要执行的一些特殊步骤, 以便数据等可以平滑迁移

## 1. 权限中心 V3 SaaS 从 `<1.3.6` 升级到 `>= 1.4.x`

> 权限中心 `1.4.x` 相对于之前版本, 对用户组/权限模板做了较大修改
> 所以升级前, 需要升级到`1.3.6`使用这个版本提供的命令进行权限模板的数据同步

说明:
- 如果全新部署, 不需要关注本文档, 直接安装最新版本的 SaaS 及后台
- 当前环境 SaaS 版本`<1.3.6`的, 需要关注, 按步骤处理

### 1.1 升级步骤

1. 将权限中心后台版本升级到`1.6.1`, SaaS 版本升级到`1.3.6`

2. 全量同步所有的权限模板
```bash
ssh $BK_APPO_IP
docker exec -it $(docker ps | grep -w bk_iam | awk '{print $1}') bash
export BK_FILE_PATH="/data/app/code/conf/saas_priv.txt"
/cache/.bk/env/bin/python /data/app/code/manage.py sync_templates
# 脚本打印 Successful completion of template version synchronization 表示执行同步成功
```
3. 升级 IAM 后台到 `1.7.x`或最新版本
```bash
# 检查IAM后台版本 
curl http://{IAM_HOST}/version 确认版本号
```
注意, 这里的`{IAM_HOST}`是权限中心后台地址, 对应企业版社区版地址`curl http://bkiam.service.consul:5001/version`(如不确定, 具体咨询环境运维人员)
4. 升级 IAM SaaS 到 `1.4.x`或最新版本

### 1.2 异常处理
1. SaaS 升级到 1.4.x 报错 `you must sync all templates before run migrate`
   
   请先回退 SaaS 到 1.3.6 版本, 后台 1.6.1 版本, 重新执行升级步骤后再升级 SaaS 到 1.4.x
   
2. SaaS 执行 `python manage.py sync_templates` 报错 `ErrorCode 1902000:(code: None, message: request iam api error`
   
   请先回退 IAM 后台到 1.6.1 版本, 再执行以上命令

## 2. 权限中心 V3 后台 从 `<1.11.9` 升级到 `>=1.12.5`

> 权限中心 后台 `1.12.5` 相对与之前的版本, 增加了RBAC相关的接入功能, 对用户组鉴权数据做了较大的变更
> 所以升级前, 需要使用数据迁移脚本`migrate_subject_system_group.py`做变更前数据迁移

说明:
- 如果全新部署, 不需要关注本文档, 直接安装最新版本的 SaaS 及后台
- 当前环境 后台 版本`<1.12.5`的, 需要关注, 按步骤处理

### 2.1 升级步骤

1. 安装迁移脚本依赖

```bash
pip3 install PyMySQL
```

2. 下载数据迁移脚本

[migrate_subject_system_group.py](https://raw.githubusercontent.com/TencentBlueKing/bk-iam/master/build/support-files/migrate_subject_system_group.py)

3. 使用迁移脚本执行数据迁移

```bash
python3 migrate_subject_system_group.py -H {db_host} -P 3306 -u {db_user} -p {db_password} -D {db_name} migrate
```

参数说明:

- db_host: 权限中心后台数据库 host
- db_user: 权限中心后台数据库 用户名
- db_password: 权限中心后台数据库 密码
- db_name: 权限中心后台数据库 db库名

4. 检查数据迁移结果

```bash
python3 migrate_subject_system_group.py -H {db_host} -P 3306 -u {db_user} -p {db_password} -D {db_name} check
```

说明:
- 数据迁移过程中不要在权限中心SaaS做用户组授权相关的操作

5. 升级权限中心后台

### 2.2 异常处理

以上迁移与检查脚本都是可重入的, 如出现异常, 可尝试重试以上步骤
