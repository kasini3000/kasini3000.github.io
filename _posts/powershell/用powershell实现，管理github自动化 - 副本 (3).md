用powershell实现，管理github自动化

搜索关键字如下：
PowerShellForGitHub

powershell 传教士 原创文章。始于 2021-02-04 允许转载，但必须保留名字和出处，否则追究法律责任

# ---【简介】---

PowerShellForGitHub是微软开发的powershell库。它通过github v3版api，访问相关功能。以达到自动化的目的。

官网：https://github.com/microsoft/PowerShellForGitHub

# ---【库的功能列表】---

查询，创建，更新和删除存储库，包括：
查询，创建和删除Branches以及关联的分支保护规则。
查询并创建新的Forks
在仓库中查询和创建内容。
查询存储库中的语言和标签，并查询/更新其主题。
更改存储库所有权。
查询，启用和禁用安全和漏洞警报。
查询并设置GitHub Actions权限。
查询，设置和删除团队权限。
查询各种流量报告，包括引荐来源和路径，页面视图和克隆。

查询，创建，编辑，锁定/解锁问题及其所有相关属性：
查询，检查，添加和删除受让人
查询，创建，编辑和删除问题注释
查询，创建，编辑和删除标签
查询事件和 时间表
查询，创建，编辑和删除里程碑

查询和创建拉取请求
查询合作者
查询贡献者
查询组织及其成员。
查询和更新用户
查询，创建，编辑和删除Teams以及查询其成员。
查询，创建，编辑和删除Projects以及 Project Columns和 Project Cards
查询，创建，编辑和删除发布以及相关的内容/资产。
查询，创建，编辑，删除，分叉和取消（un）star gists以及要点注释。
查询，编辑和删除对“问题”和“拉取请求”的反应。

杂项功能：
获取所有行为准则以及特定回购协议的行为准则。
获取所有GitHub表情符号
获取gitignore模板
获取常用许可证以及特定存储库的许可证。
将markdown转换为等效的HTML
获取您当前使用API的速率限制。

# ---【安装】---

Install-Module -Name PowerShellForGitHub

# ---【保存github账户密码】---

在这里设置api token：
https://github.com/settings/tokens/new

用这个命令，保存账户密码：
Set-GitHubAuthentication

查看帮助：
Get-Help Set-GitHubAuthentication -Examples

# ---【例子：查找所有标签为“需要进行复制的步骤”的问题，并为这些问题添加新的注释】---

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

```
$issues = @(Get-GitHubIssue -OwnerName microsoft -RepositoryName PowerShellForGitHub -Label 'repro steps needed')
foreach ($issue in $issues)
{
    $params = @{
        'OwnerName' = 'microsoft'
        'RepositoryName' = 'PowerShellForGitHub'
        'Issue' = $issue.number
        'Body' = 'Any update on those repro steps?'
    }
    New-GitHubIssueComment @params
}
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)]()

# ---【更多命令的用法和例子】---

https://github.com/microsoft/PowerShellForGitHub/blob/master/USAGE.md#examples

# ---【附录a：命令列表】---

Add-GitHubAssignee
Add-GitHubIssueLabel
Backup-GitHubConfiguration
Clear-GitHubAuthentication
ConvertFrom-GitHubMarkdown
Get-GitHubConfiguration
Get-GitHubEmoji
Get-GitHubRateLimit
Group-GitHubIssue
Group-GitHubPullRequest
Invoke-GHRestMethod
Invoke-GHRestMethodMultipleResult
Join-GitHubUri
New-GitHubTeam
Remove-GitHubAssignee
Remove-GitHubGistFile
Reset-GitHubConfiguration
Restore-GitHubConfiguration
Set-GitHubAuthentication
Set-GitHubConfiguration
Set-GitHubGistFile
Set-GitHubIssueLabel
Set-GitHubProfile
Set-GitHubRepositoryTopic
Test-GitHubAuthenticationConfigured
Add-GitHubGistStar
Copy-GitHubGist
Disable-GitHubRepositorySecurityFix
Disable-GitHubRepositoryVulnerabilityAlert
Enable-GitHubRepositorySecurityFix
Enable-GitHubRepositoryVulnerabilityAlert
Get-GitHubAssignee
Get-GitHubCloneTraffic
Get-GitHubCodeOfConduct
Get-GitHubContent
Get-GitHubEvent
Get-GitHubGist
Get-GitHubGistComment
Get-GitHubGitIgnore
Get-GitHubIssue
Get-GitHubIssueComment
Get-GitHubIssueTimeline
Get-GitHubLabel
Get-GitHubLicense
Get-GitHubMilestone
Get-GitHubOrganizationMember
Get-GitHubPathTraffic
Get-GitHubProject
Get-GitHubProjectCard
Get-GitHubProjectColumn
Get-GitHubPullRequest
Get-GitHubReaction
Get-GitHubReferrerTraffic
Get-GitHubRelease
Get-GitHubReleaseAsset
Get-GitHubRepository
Get-GitHubRepositoryActionsPermission
Get-GitHubRepositoryBranch
Get-GitHubRepositoryBranchProtectionRule
Get-GitHubRepositoryCollaborator
Get-GitHubRepositoryContributor
Get-GitHubRepositoryFork
Get-GitHubRepositoryLanguage
Get-GitHubRepositoryTag
Get-GitHubRepositoryTeamPermission
Get-GitHubRepositoryTopic
Get-GitHubTeam
Get-GitHubTeamMember
Get-GitHubUser
Get-GitHubUserContextualInformation
Get-GitHubViewTraffic
Initialize-GitHubLabel
Lock-GitHubIssue
Move-GitHubProjectCard
Move-GitHubProjectColumn
Move-GitHubRepositoryOwnership
New-GitHubGist
New-GitHubGistComment
New-GitHubIssue
New-GitHubIssueComment
New-GitHubLabel
New-GitHubMilestone
New-GitHubProject
New-GitHubProjectCard
New-GitHubProjectColumn
New-GitHubPullRequest
New-GitHubRelease
New-GitHubReleaseAsset
New-GitHubRepository
New-GitHubRepositoryBranch
New-GitHubRepositoryBranchProtectionRule
New-GitHubRepositoryFork
New-GitHubRepositoryFromTemplate
Remove-GitHubGist
Remove-GitHubGistComment
Remove-GitHubGistStar
Remove-GitHubIssueComment
Remove-GitHubIssueLabel
Remove-GitHubLabel
Remove-GitHubMilestone
Remove-GitHubProject
Remove-GitHubProjectCard
Remove-GitHubProjectColumn
Remove-GitHubReaction
Remove-GitHubRelease
Remove-GitHubReleaseAsset
Remove-GitHubRepository
Remove-GitHubRepositoryBranch
Remove-GitHubRepositoryBranchProtectionRule
Remove-GitHubRepositoryTeamPermission
Remove-GitHubTeam
Rename-GitHubGistFile
Rename-GitHubRepository
Rename-GitHubTeam
Set-GitHubContent
Set-GitHubGist
Set-GitHubGistComment
Set-GitHubGistStar
Set-GitHubIssue
Set-GitHubIssueComment
Set-GitHubLabel
Set-GitHubMilestone
Set-GitHubProject
Set-GitHubProjectCard
Set-GitHubProjectColumn
Set-GitHubReaction
Set-GitHubRelease
Set-GitHubReleaseAsset
Set-GitHubRepository
Set-GitHubRepositoryActionsPermission
Set-GitHubRepositoryTeamPermission
Set-GitHubTeam
Split-GitHubUri
Test-GitHubAssignee
Test-GitHubGistStar
Test-GitHubOrganizationMember
Test-GitHubRepositoryVulnerabilityAlert
Unlock-GitHubIssue

-完-

分类: [powershell](https://www.cnblogs.com/piapia/category/420584.html)

标签: [PowerShellForGitHub](https://www.cnblogs.com/piapia/tag/PowerShellForGitHub/)
