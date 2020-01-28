![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Delete All Old Backup Files With SQL
**Post Date: May 27, 2014**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's a quick script you can run to remove old backups. All you need to do is place it into a Job.
Question:
What does this script do?
Answer:
1. This will first enable the xp_cmdshell command.
2. It pulls a list of every database path & file location
3. Deletes all old backup files ( older than 10 days ) from the locations identified in System tables.
4. This will remove all old files such as: Full Database, Transaction Logs, and Differentials.
All you need to do is place this into a Job, and schedule it to run nightly. Remember the account that the job runs under must have access rights to the backup folders.</p>      


## SQL-Logic
```SQL
se master;
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

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

     
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

