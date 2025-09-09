# 利用外部定时器cron-job，多账户多app应用一起并发拉起保活 #
## 1、设置对应好的环境变量 ##
每个账号每个app都不一样，需要多个一起的话，每个变量都新建变量分别填好对应的。路径是你所在的项目settings——Secrets and variables——Actions——New repository secret。
比如新加坡的第一个应用的API，变量名称填写：CF_API_APP1，对应的值填写：https://api.cf.ap21.hana.ondemand.com
其他的也是以此类推。

## 2、创建具有正确权限的 PAT ##
1）登录 GitHub，点击右上角头像 → Settings

2）在左侧菜单中，选择 Developer settings

3）选择 Personal access tokens → Tokens (classic)

4）点击 Generate new token → Generate new token (classic)

5）填写 Token 描述（例如 "cron-job.org SAP 保活"）

6）设置过期时间（建议选择较长的有效期）

7）选择以下权限范围（这是关键步骤）：

repo (必须全选)

repo:status

repo_deployment

public_repo (对于私有仓库，这个可以不选，但选了也无妨)

repo:invite

security_events

workflow (必须选中)

点击 Generate token 按钮

8）立即复制生成的 token（之后将无法再次查看完整 token）

## 3、在 cron-job.org 中配置 ##

1）注册登陆定时任务网站：https://cron-job.org

2）创建新的定时任务：
新建任务
标题: SAP保活（任意自取） <br> 
URL  网址: <br> 
https://api.github.com/repos/[用户名]/[仓库名]/actions/workflows/main.yml/dispatches <br> 
将 [用户名] 和 [仓库名] 替换为您的实际信息

运行任务根据自己需求勾选，我选的是自定，小时选8，分钟选15，表达式是：*/15 8 * * *
也就是8~9点每15分钟运行一次。

如图：
![Image](https://github.com/jok699/sap/blob/main/cron01.png)


进阶处：
请求方法: POST

请求头:
Authorization: token [您的PAT] <br> 
Accept: application/vnd.github.v3+json <br> 
Content-Type：application/json <br> 
User-Agent：cron-job.org

请求体:
{"ref":"main"}

如图：
如图：
![Image](https://github.com/jok699/sap/blob/main/cron02.png)

最后保存计划。

3）测试是否成功
点击“测试运行”，运行状态返回 ✓204 No Content，表示运行成功。
![Image](https://github.com/jok699/sap/blob/main/cron03.png)
