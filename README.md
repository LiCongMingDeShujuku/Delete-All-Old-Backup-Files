![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 删除所有旧的备份文档 SQL
#### Delete All Old Backup Files SQL
**发布-日期: 2014年05月27日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这是一个可以删除旧备份的快速脚本。 你需要做的就是将其置于作业中。

问题：
这个脚本有什么用？

答案：
1. 这将会首先启用xp_cmdshell命令。
2. 它会拉一个列表，包含每个数据库的路径和文件位置
3. 从系统表中标识的位置删除所有旧备份文件（超过10天）。
4. 这将删除所有旧文件，例如：整个数据库，事务日志和差速器。

你需要做的就是将其放入作业中，并安排它每晚运行。请注意运行作业的帐户必须具有备份文件夹的访问权限。

## English
Here’s a quick script you can run to remove old backups. All you need to do is place it into a Job.

Question:
What does this script do?

1. This will first enable the xp_cmdshell command.
2. It pulls a list of every database path & file location
3. Deletes all old backup files ( older than 10 days ) from the locations identified in System tables.
4. This will remove all old files such as: Full Database, Transaction Logs, and Differentials.

All you need to do is place this into a Job, and schedule it to run nightly. Remember the account that the job runs under must have access rights to the backup folders.

---
## Logic
```SQL
use master;
set nocount on
 
exec master..sp_configure 'show advanced options', '1'; reconfigure
exec master..sp_configure 'xp_cmdshell', '1'; reconfigure
 
declare @delete_old_files   varchar(max)
declare @today              datetime
declare @10_days_old        datetime
declare @20_days_old        datetime
set     @today              = (select getdate())
set     @10_days_old        = @today - 10  -- number of days you want to keep.  older files will be deleted.
set     @20_days_old        = @today - 20  -- extra variable if you want to incorporate another delete statement.
set     @delete_old_files   = ''
select  @delete_old_files   = @delete_old_files + 'exec master..xp_cmdshell ''del "' + bmf.physical_device_name + '"'';' + char(10)
from    msdb..backupset bs join msdb..backupmediafamily bmf on bs.media_set_id = bmf.media_set_id
where   bs.type in ('D', 'I', 'L' ) and bs.backup_finish_date between @20_days_old and @10_days_old
order by bs.backup_finish_date desc
exec    (@delete_old_files)-- for xml path(''), type


```

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

