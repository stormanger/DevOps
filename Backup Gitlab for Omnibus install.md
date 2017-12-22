# Docs:

```
https://about.gitlab.com/installation/#ubuntu
https://docs.gitlab.com/omnibus/settings/backups.html
https://docs.gitlab.com/ce/raketasks/backup_restore.html
http://docs.gitlab.com/omnibus/update/README.html
```

# Package:

```
https://packages.gitlab.com/gitlab/gitlab-ee?filter=debs
```

# Backup configuration / 备份配置文件

All configuration for omnibus-gitlab is stored in /etc/gitlab. To backup your configuration, just backup this directory.使用omnibus安装的Gitlab的所有配置都在/etc/gitlab目录中，只需要备份这个目录目录就好了

Use this command for backup configuration one time.一次备份的话，使用这条命令就好了

```
$ sudo sh -c 'umask 0077; tar -cf /mnt/gitlab-backup/$(date "+etc-gitlab-%s.tar") -C / etc/gitlab'
```

To create a daily backup, use the cron table for user root.如果需要周期性的备份，则以root权限配置cron table

```
$ sudo crontab -e
0 4 * * * umask 0077; tar -cf /mnt/gitlab-backup/$(date "+etc-gitlab-\%s.tar") -C / etc/gitlab
```

# Backup data / 备份数据

## Backup configuration instructions / 备份配置说明

All backup configurations are in /etc/gitlab/gitlab.rb.所有的备份配置都在/etc/gitlab/gitlab.rb文件中

The default configuration is gitlab_rails['backup_path'] = "/var/opt/gitlab/backups".默认的配置参数是gitlab_rails['backup_path'] = "/var/opt/gitlab/backups"

You can modify the default backup stored path, than use this command to make changes effective.你可以修改默认的备份路径，并且使用下面的命令使配置生效

```
$ sudo gitlab-ctl reconfigure
```

## Uploading to local storage / 备份自动上传到本地目录

Config for auto send backups to a local directory, The directory must be owned by the git user . 配置自动发送备份到本地目录，但是必须要注意这个目录的所有者必须是git

```
gitlab_rails['backup_upload_connection'] = {
     :provider => 'Local',
     :local_root => '/mnt/gitlab-backup'      
}
gitlab_rails['backup_upload_remote_directory'] = '.'
```

As the same，to make the changes effective by use sudo gitlab-ctl reconfigure. 同样的，用sudo gitlab-ctl reconfigure使配置生效

```
$ sudo gitlab-ctl reconfigure
```

Use this command as one time backup if you've installed GitLab with the Omnibus package. 使用这条命令作为一次性备份，如果Gitlab是用Omnibus安装的

```
$ sudo gitlab-rake gitlab:backup:create

$ sudo crontab -e
0 2 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create
```

# Restore / 还原

## Restore data / 还原数据

Please make sure there is the same Gitlab version between backup tar file and the target Gitlab, or the restore command will abort with an error. 保证备份包和待还原的Gitlab版本相同，否则还原命令将会报错

First make sure your backup tar file is in the backup directory described in the gitlab.rb configuration gitlab_rails['backup_path']. 开始请将备份包拷贝到配置文件gitlab.rb中指定的目录中，详见这条参数gitlab_rails['backup_path']

The default is /var/opt/gitlab/backups/ 默认的备份文件路径是/var/opt/gitlab/backups

```
$ sudo cp 1493107454_2017_04_25_9.1.0_gitlab_backup.tar /var/opt/gitlab/backups/
```

Stop the processes that are connected to the database. 停止数据库的连接

```
$ sudo gitlab-ctl stop unicorn
$ sudo gitlab-ctl stop sidekiq
```

Verify / 确认状态

```
$ sudo gitlab-ctl status
```

This command will overwrite the contents of your GitLab database! So please do not use the same datebase to other application. 这条命令会先删除数据库中的所有表再根据备份包中的记录还原，请不要和其他应用的数据库混用

```
$ sudo gitlab-rake gitlab:backup:restore BACKUP=1508918829_2017_10_25_10.1.0-ee
```

## Restore configuration / 还原配置

```
$ sudo tar -xf etc-gitlab-1508918941.tar -C /
```

Restart and check gitlab / 恢复及检查服务

```
$ sudo gitlab-ctl restart
$ sudo gitlab-rake gitlab:check SANITIZE=true
```

# Delete backups created over a month / 自动删除30天以上的备份文件

```
$ sudo crontable -e
0 6 * * * find "/var/opt/gitlab/backups/" -maxdepth 1 -type f -name "*.tar" -mtime +30 -exec rm -f {} \;

```

```
0 2 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create
0 4 * * * umask 0077; tar -cf /mnt/gitlab-backup/$(date "+etc-gitlab-\%s.tar") -C / etc/gitlab
0 5 * * * find "/var/opt/gitlab/backups/" -maxdepth 1 -type f -name "*.tar" -mtime +15 -exec rm -f {} \;
0 6 * * * find "/mnt/gitlab-backup/" -maxdepth 1 -type f -name "*.tar" -mtime +30 -exec rm -f {} \;
0 7 * * * find "/mnt/FullAccessFolder/Gitlab-Backup/" -maxdepth 1 -type f -name "*.tar" -mtime +60 -exec rm -f {} \;
0 8 * * * find "/mnt/gitlab-backup/" -maxdepth 1 -type f -name "*.tar" -mtime -1 -exec rsync -avlP {} /mnt/FullAccessFolder/Gitlab-Backup/ \;
```
