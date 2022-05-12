# cron basics

`crontab -e` to open editor  
`crontab -l [-u user]` to list cron jobs

[Reference: wikipedia](https://en.wikipedia.org/wiki/Cron)

┌───────────── minute (0 - 59)  
│ ┌───────────── hour (0 - 23)  
│ │ ┌───────────── day of the month (1 - 31)  
│ │ │ ┌───────────── month (1 - 12)  
│ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday)  
│ │ │ │ │     
│ │ │ │ │  
│ │ │ │ │  
* * * * * command to execute  
(7 is also Sunday on some systems)

**example**
`* * * * *` starts the job every minute.  
`0 0 * * *` starts the job at 00:00 every day.  
`50 * * * *` starts the job at the 50th minute of every hour.  
`*/15 * * * *` starts the job at every 15 minues (running at 0, 14, 30, 45 of the hour).  

# run cron job

`5 * * * * /test.sh`  
`5 * * * * /test.sh -v` enables logging details

# check logs

`vi /var/log/cron` cron job change log  
`vi /var/spool/mail/d_pbp` cron job error log  
`sudo vi /var/spool/mail/d_pbp` to force view job error log

# setup PySpark in cron

Add the following in the bash that calls the PySpark script

```bash
export JAVA_HOME=/home/gs/java/current
export HADOOP_CONF_DIR=/home/gs/conf/current
```
