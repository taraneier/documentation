---
title: Identify and Kill Queries with MySQL Command-Line Tool
description: Learn how to identify and kill long-running MySQL queries on your WordPress or Drupal site in a few commands.
keywords: mysql, mysql query, mysql queries, kill query, kill queries, kill mysql, kill mysql queries, queries, query, debug mysql, debug, mysql error
---
Long-running MySQL queries keep other transactions from accessing the necessary tables to execute a request, leaving your users on hold. To kill these queries, all you need is a local MySQL client such as [MySQL Workbench](http://dev.mysql.com/downloads/tools/workbench/), [Sequel Pro](http://www.sequelpro.com/download), or [Navicat](http://www.navicat.com/download).

## Create a New MySQL Connection
Each site environment (Multidev, Dev, Test and Live) has a separate database. Click **Connection Info** in the Dashboard of the environment you want to investigate. Paste the provided database command into your terminal to establish a connection.

### Create a Secure Connection
You can create a secure connection using SSH tunnels, but it is not required. For more information, see [SSH Tunnels for Secure Connections to Pantheon Services](/docs/articles/local/ssh-tunnels-for-secure-connections-to-pantheon-services).
<div class="alert alert-info" role="alert">
<h4>Note</h4>
Due to the nature of our platform, the connection information will change from time to time due to server upgrades, endpoint migrations, etc. You will need to check the Dashboard periodically or when you can’t connect.</div>

## Identify Long-Running Queries
After successfully creating a local MySQL connection to the site's database, run the following command to show a list of active threads:
```
show processlist;
```
Review the `Time` field to identify the longest running query and run the following command to kill it:
```
kill <thread_id>;
```
<div class="alert alert-info" role="alert">
<h4>Note</h4>
Replace <code>&lt;thread_id&gt;</code> with the ID of the query you want to terminate.
</div>

## Kill All Queries
If a large number of bad requests are blocking valid queries, you can clear them out without having to run `kill` on every individual thread.

Use the following command to fetch information from the `PROCESSLIST` table and generate an external file with `kill` commands:
```
mysql> select concat('KILL ',id,';') from information_schema.processlist where user='root' into outfile '/tmp/killallthreads.txt';
```
Run the following to execute the `kill` commands previously generated:
```
mysql> source /tmp/killallthreads.txt;
```

## See Also
- [Access MySQL Databases](/docs/articles/local/accessing-mysql-databases)
- [Database Connection Errors](/docs/articles/sites/database/database-connection-errors)
- [MySQL Slow Log](/docs/articles/sites/logs/mysql-slow-log/)
- [MySQL Troubleshooting with New Relic Pro](/docs/articles/sites/newrelic/mysql-troubleshooting-with-new-relic-pro/)
- [Converting MySQL Tables From MyISAM to InnoDB](/docs/articles/sites/database/myisam-to-innodb/)
