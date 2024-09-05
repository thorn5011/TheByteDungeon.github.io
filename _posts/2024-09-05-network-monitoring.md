---
layout: newpost
title: "Monitor my network connection go go?.."
date: 2024-09-05
tags: [go, monitoring, sql]
categories: tech
---

I often get the feeling that my network is acting slow on purpose. Often pages are stuck loading, seemingly without reason, and when I start up a ping, there is nothing wrong. Continuing to resolve the troublesome domain progresses me no further and when I more move back to the browser tab, everything seems fine again..

Grab your tinfolis and let's get to the bottom of this conspiracy! *(we shall at least try)*

![tinfoil hats](/assets/images/blogs/tinfoil.jpg)

- [Getting all the moving part working](#getting-all-the-moving-part-working)
  - [Creating my first go script :baby:](#creating-my-first-go-script-baby)
  - [Set variables for the script to use](#set-variables-for-the-script-to-use)
- [Setting up SQL for persistent storage](#setting-up-sql-for-persistent-storage)
  - [Create DB and assign permissions](#create-db-and-assign-permissions)
- [Setup crontab for regular script execution](#setup-crontab-for-regular-script-execution)
- [Query the data](#query-the-data)
- [Conclusion](#conclusion)


---

This is really just an excuse to finally code something in go (even though GitHub copilot did ~~most~~all of the heavy lifting) and get some more use out of the MariaDB.

---
# Getting all the moving part working

1. Install `go` (duh!)
2. Setup the go project `go mod init github.com/thorn5011/netMon`
3. Install any dependencies `go mod tidy` (after adding imports to the code)
   1. This is a reaaally good feature where you lock down the dependencies with the correspiding hash in `go.sum`, ex. `github.com/go-sql-driver/mysql v1.8.1 h1:LedoTUt/eveggdHS9qUFC1EFSa8bU2+1pZjSRpvNJ1Y=`. No worrying about [dependency confusion](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610) :fireworks:
4. Run script with `go run script.go`
   1. You can also build the project into an executable, but I won't bother with that for now.

##  Creating my first go script :baby:

I simply want a script to loop through a few different DNS servers and resolve a couple of hostnames, while measuring the resolve time. Then we can graph it over time and look for anomalies.

The script scan be found [here](https://github.com/thorn5011/sharing-is-caring/tree/main/netMon).

Learnt to code some very simple go, with arugments, different outputs and a bunch of troubleshooting.


## Set variables for the script to use

Set variables in Windows:
```bat
setx NETMON_DB_USERNAME netMon_user /m
setx NETMON_DB_PASSWORD KEY /m
```

In Linux:
```sh
export NETMON_DB_USERNAME=netMon_user
export NETMON_DB_PASSWORD=KEY
```

---

# Setting up SQL for persistent storage

## Create DB and assign permissions
```sql
CREATE DATABASE netMon;
CREATE USER 'netMon_user'@'192.168.0.96' IDENTIFIED BY 'KEY';
GRANT CREATE, SELECT, INSERT ON netMon.* TO 'netMon_user'@'192.168.0.96';
-- show permissions
SHOW GRANTS FOR 'netMon_user'@'192.168.0.96';
```

---
# Setup crontab for regular script execution

Envrionment variables are stored in `/root/.envs` and imported at crontab execution:

```sh
*/5 * * * * . /root/.envs && cd PATH/netMon/startMonitor && /usr/local/go/bin/go run PATH/netMon/startMonitor/netMon.go >> PATH/netMon/startMonitor/app.log
```

---
# Query the data

```sql
-- count all rows
select COUNT(*) from dns_results;

-- show rows
select *
from dns_results
limit 5;

+----+------------+------------+---------------+---------------------+
| id | server     | hostname   | response_time | timestamp           |
+----+------------+------------+---------------+---------------------+
|  1 | 8.8.8.8:53 | google.com |            35 | 2024-09-02 17:15:36 |
|  2 | 8.8.8.8:53 | vg.no      |            17 | 2024-09-02 17:15:36 |
|  3 | 8.8.8.8:53 | vipps.no   |            23 | 2024-09-02 17:15:36 |
|  4 | 1.1.1.1:53 | google.com |             4 | 2024-09-02 17:15:36 |
|  5 | 1.1.1.1:53 | vg.no      |            24 | 2024-09-02 17:15:36 |
+----+------------+------------+---------------+---------------------+
```


Checking average response times
```sql
-- per server and hostname
SELECT server,hostname, AVG(response_time) AS avg_response_time
FROM dns_results
GROUP BY hostname,server;

-- per server
SELECT server, AVG(response_time) AS avg_response_time
FROM dns_results
GROUP BY server;

+----------------+-------------------+
| server         | avg_response_time |
+----------------+-------------------+
| 1.1.1.1        |           34.2612 |
| 192.168.0.1    |           26.3343 |
| 192.168.0.187  |           66.8090 |
| 8.8.8.8        |           43.2505 |
+----------------+-------------------+
```
So far the local router is in the lead and the Pi-hole is sadly at the back of the back. :cry:


Slow responses:
```sql
SELECT server,hostname,response_time,timestamp
FROM dns_results
ORDER BY response_time desc
LIMIT 5;


SELECT COUNT(*), server
FROM dns_results 
WHERE response_time > 250
GROUP BY server;

+----------+----------------+
| COUNT(*) | server         |
+----------+----------------+
|       14 | 1.1.1.1        |
|        7 | 192.168.0.1    |
|       12 | 192.168.0.187  |
|        5 | 8.8.8.8        |
+----------+----------------+
```
I do want to graph this data out over time, but for that I need more tools.

---

# Conclusion

There is much to learn from go, but I would bet that most, if not all use cases could be dealt with by Python. But having a rudimental understanding of a popular language such as go is useful (especially since go is one of the ***go-to*** languages we use) 

I want to better understand the network data, so I think I'll look into [Metabase](https://github.com/metabase/metabase) or [redash](https://github.com/getredash/redash) to visualize it all, but we'll see. :file_folder: