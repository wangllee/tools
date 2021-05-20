gitlab备份和恢复

1.gitlab备份的相关配置在 /etc/gitlab/gitlab.rb文件的第300-330行左右，最主要的两个配置为

- 备份目录：```gitlab_rails['backup_path'] = "/data/gitlab/backups" ```
- 备份存放时间(秒)：```gitlab_rails['backup_keep_time'] = 604800```

2.使用默认配置备份和还原

- 备份命令：```gitlab-rake gitlab:backup:create CRON=1```
  备份完成会生成文件1583218241_2020_03_03_11.7.5_gitlab_backup.tar

  注意：环境变量CRON=1的作用是如果没有任何错误发生时， 抑制备份脚本的所有进度输出
  
- 恢复命令：

  执行恢复命令前，先关闭相关连接

  `gitlab-ctl stop unicorn
  gitlab-ctl stop sidekiq
  gitlab-ctl status`

  备份：```gitlab-rake gitlab:backup:restore -BACKUP=1583218241_2020_03_03_11.7.5```(不需要加_gitlab_backup.tar)

  恢复时会提示：需要删除各种信息是否继续，输入yes即可

  ```Before restoring the database, we will remove all existing  tables to avoid future upgrade problems. Be aware that if you have  custom tables in the GitLab database these tables and all data will be  removed.  ```
Do you want to continue (yes/no)? yes
  
  ```This task will now rebuild the authorized_keys file.  You will lose any data stored in the authorized_keys file.```
Do you want to continue (yes/no)? yes
  
  完成后重启：```gitlab-ctl restart```

3.自定义备份路径备份和还原

- 创建备份目录，修改gitlab配置文件

  `mkdir /wll/gitlab/backups `

  `gitlab_rails['backup_path'] = "/wll/gitlab/backups" `

  修改完成后需要刷新配置重启gitlab服务

  `gitlab-ctl reconfigure`

  `gitlab-ctl restart`

- 修改备份目录权限

  `chown -R git.git /wll/gitlab/backups`

  `chmod -R 777 /wll/gitlab/backups`

  如果不执行此步骤，备份和还原时会出现`Errno::EACCES: Permission denied @ dir_s_mkdir`的异常，可参考https://www.jianshu.com/p/68a39c7193e7

- 按照默认配置备份和还原方式操作

4.设置定时备份(crontab)

编辑定时任务：`crontab -e`

每周日1点备份,在文件最后一行输入：```0 1 * * 7 gitlab-rake gitlab:backup:create > /dev/null 2>&1```

#查看任务 ```crontab -l```

5.排除特定目录(skip)

`gitlab-rake gitlab:backup:create skip=db,uploads`

- `db` (数据库)
- `uploads` (附件)
- `repositories` (Git repositories 数据)
- `builds` (CI job output logs)
- `artifacts` (CI job artifacts)
- `lfs` (LFS objects)
- `registry` (Container Registry images)
- `pages` (Pages content)

6.备份到远程目录

- 挂载远程目录：`mount -t cifs -o uid=0,gid=0,rw,file_mode=0777,dir_mode=0777,username=np,password=123a //192.168.1.47/smt/gitlab_backups /mnt/gitlab_backups`

  修改挂载文件：`vim /etc/fstab` 添加

  `//192.168.1.47/smt/gitlab_backups /mnt/gitlab_backups cifs uid=0,gid=0,rw,file_mode=0777,dir_mode=0777,username=np,password=123a 0 0`

  //192.168.1.59/gitlab-backup /mnt/zsk-gitlab-backups cifs uid=0,gid=0,rw,file_mode=0777,dir_mode=0777,username=shaokunzhou,password=Zsk@8492032Admin 0 0

- 修改配置文件：```gitlab_rails['backup_upload_connection'] = {  :provider => 'Local',  ```
  `:local_root => '/mnt/gitlab_backups'}`
  `gitlab_rails['backup_upload_remote_directory'] = ''`

  然后刷新gitlab配置即可

vim /etc/gitlab/gitlab.rb
gitlab-rake gitlab:backup:create CRON=1
gitlab-ctl reconfigure
 cifs uid=0,gid=0,rw,file_mode=0777,dir_mode=0777,username=shaokunzhou,password=Zsk@8492032Admin 0 0
mount -t cifs -o uid=0,gid=0,uid=0,gid=0,rw,file_mode=0777,dir_mode=0777,username=shaokunzhou,password=Zsk@8492032Admin //192.168.1.59/gitlab-backup /mnt/zsk-gitlab-backups

