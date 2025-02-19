# 更新日志
## v2.1.7
### Bugfix
* 管理面板有权限的情况下刷新404

### Chore
* 调整提交文件按钮尺寸
* 提示文案更新

## v2.1.6
### Feature
* 管理面板中将 "文件数量" 调整为 "记录/OSS"
  * 记录即`提交记录数量`
  * OSS即`云上实际存在的文件数量`
* 上传表单信息支持`单选`，`下拉选择`，`固定内容`
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MTQ5NjU2ODcyNg==651496568726" style="height:100px;" />
* 任务支持设置批注
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MTQ5NjI2OTI0MQ==651496269241" style="height:100px;" />
* 上传的文件支持查看原文件名
* 支持直接从任务卡片跳转到文件面板展示对应任务数据
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MTU2MzY3MTQzMA==651563671430" style="height:100px;" />
* 支持查看已收集文件的大小
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MTU2MzU2ODk4Mg==651563568982" style="width:200px;" />
* 添加赞赏入口
* 管理端支持直接重置密码/换绑手机号

### Chore
* 提交面板默认展示删除文件icon
* 更新模板下载提示文案
* 慢查询优化
* 列表数据增加`Loading`状态优化交互

### Bugfix
* 验证码登录不支持19x，放开对手机号的严格限制
* 上传时可修改表单内容
* 未正确处理文件名中特殊字符`?`
* PC/H5来回切换导航栏展示异常
* H5侧删除弹窗展示异常

<!-- * ~ 重命名文件后缀从头开始读取，兼容 `.d.ts`,`.tar.gz` 等情况 ~
  * 部分文件包含较多无用信息 -->
## v2.1.5
### Feature
* 支持从其它任务导入限制人员
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MDYzODMzNTI5Mw==650638335293" style="height:100px;" />
* 支持从其它任务导入表单信息
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MDYzODU5OTQ2Mg==650638599462" style="width:200px;"/>
* 支持手动清理OSS上已失效的归档文件，节约存储空间
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MDYzODczNzUyMw==650638737523" style="width:200px;"/>
* 文档支持图片预览
* PC侧支持拖拽上传
* 提交信息页支持控制姓名展示

### Chore
* 移除CNZZ统计
* UI/UE优化
* 保存表单信息，添加防抖避免反复触发
* 升级 Element UI 版本

### UI/UE
* 优化登录页的UI
* 优化表单信息设置页的UI
* DDL面板UI和文案优化
* 优化任务提交页UI
* 优化移除未上传文件的交互逻辑

## v2.1.4
### Feature
* 信息维护面板，简单显示帮助提示信息 + 图例
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MDE4Mzg4NjUzMw==650183886533" style="height:100px;"/>
* 文件提交页增加提示文案
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MDE4NDE0MTE0MA==650184141140" style="height:100px;"/>

### Chore
* 升级客户端 qiniu-sdk版本

### Bugfix
* 文件提交页交互缺陷修复
* 图片/PDF/视频等浏览器支持预览的文件将会直接进行预览
  * 改由应用去控制此类文件的下载 <Picture src="https://img.cdn.sugarat.top/mdImg/MTY1MDE4NDM0Nzg2NA==650184347864" style="height:100px;"/>
* 批量删除文件的误删
* 撤回文件误删

## v2.1.3
### Bugfix
* 提交文件面板，未选择文件时可点击提交文件并进行后续逻辑
* 单个文件记录删除，导致关联文件被误删
* 将无关联任务的文件，单独展示出来
  * <Picture src="https://img.cdn.sugarat.top/mdImg/MTY0OTkzNjA2OTY1Ng==649936069656" style="width:200px;"/>

### Chore
* 优化删除提示文案”数据无价，谨慎操作“
## v2.1.2
### Feature
* 文件列表支持图片数据预览
### Chore
* 更新导航栏展示数据

## v2.1.1
### Chore
* 支持日志搜索

## v2.1.0
### Feature
* 优化提交记录的Excel导出格式
  * ![图片](https://img.cdn.sugarat.top/mdImg/MTY0OTgxNTg0MDg1Mg==649815840852)

### Chore
* 添加[51la](https://v6.51.la/)的的数据统计SDK
### BugFix
* 文件面板，批量操作卡顿问题修复

## v2.0.8
### Bugfix
* 提交查询权限一定几率的失败

## v2.0.7
### Feature
* 支持导出日志数据

## v2.0.6
### Feature
* 人员提交情况面板，展示提交文件数量
* 管理员面板支持查看详细日志信息

### Chore
* 优化批量下载错误提示信息
* 日志切换为分页接口

## v2.0.5
### Feature
* 优化截止日期设置面板的提示文案

### Bugfix
* 批量下载由于特殊字符“•”导致下载失败

### Chore
* 优化批量下载错误提示信息

## v2.0.4
### Feature
* 人员提交情况面板展示序号

## v2.0.3
### Chore
* UE/UE:一系列样式与交互优化...