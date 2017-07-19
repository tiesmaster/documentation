---
title: Datadog-MySQL Integration
integration_title: MySQL
kind: integration
git_integration_title: mysql
newhlevel: true
---
# Overview

The Datadog Agent can collect many metrics from MySQL databases, including those for:

* Query throughput
* Query performance (average query run time, slow queries, etc)
* Connections (currently open connections, aborted connections, errors, etc)
* InnoDB (buffer pool metrics, etc)

And many more. You can also invent your own metrics using custom SQL queries.

The Agent sends one MySQL-related service check: whether or not the Agent is successfully collecting metrics from MySQL.

The Agent does not send anything MySQL-related to your events stream.

# Setup

## Installation

The MySQL integration - also known as the MySQL check - is included in the Datadog Agent package, so simply [install the Agent](https://app.datadoghq.com/account/settings#agent) on your MySQL servers. If you need the newest version of the MySQL check, install the `dd-check-mysql` package; this package's check will override the one packaged with the Agent. See the [integrations-core](https://github.com/DataDog/integrations-core#installing-the-integrations) repository for more details.

## Configuration

### Prepare MySQL

On each MySQL server, create a database user for the Datadog Agent:

```
mysql> CREATE USER 'datadog'@'localhost' IDENTIFIED BY '<YOUR_CHOSEN_PASSWORD>';
Query OK, 0 rows affected (0.00 sec)
```

The Agent needs a few privileges to collect metrics. Grant its user ONLY the following privileges:

```
mysql> GRANT REPLICATION CLIENT ON *.* TO 'datadog'@'localhost' WITH MAX_USER_CONNECTIONS 5;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> GRANT PROCESS ON *.* TO 'datadog'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

If the MySQL server has the `performance_schema` database enabled and you want to collect metrics from it, the Agent's user needs one more `GRANT`. Check that `performance_schema` exists and run the `GRANT` if so:

```
mysql> show databases like 'performance_schema';
+-------------------------------+
| Database (performance_schema) |
+-------------------------------+
| performance_schema            |
+-------------------------------+
1 row in set (0.00 sec)

mysql> GRANT SELECT ON performance_schema.* TO 'datadog'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

### Connect the Agent

Create a basic `mysql.yaml` in the Agent's `conf.d` directory to connect it to the MySQL server:

```
init_config:

instances:
  - server: localhost
    user: datadog
    pass: <YOUR_CHOSEN_PASSWORD> # from the CREATE USER step earlier
    port: <YOUR_MYSQL_PORT> # e.g. 3306
    options:
        replication: 0
        galera_cluster: 1
        extra_status_metrics: true
        extra_innodb_metrics: true
        extra_performance_metrics: true
        schema_size_metrics: false
        disable_innodb_metrics: false
```

If you found above that MySQL doesn't have `performance_schema` enabled, do not set `extra_performance_metrics` to `true`.

See our [sample mysql.yaml](https://github.com/Datadog/integrations-core/blob/master/mysql/conf.yaml.example) for all available configuration options, including those for custom metrics.

Restart the Agent to start sending MySQL metrics to Datadog.

## Validation

Run the Agent's `info` subcommand and look for `mysql` under the Checks section:

```
  Checks
  ======

    [...]

    mysql
    -----
      - instance #0 [OK]
      - Collected 168 metrics, 0 events & 1 service check

    [...]
```

If the status is not OK, see the Troubleshooting section.

# FAQ

You may observe one of these common problems in the output of the Datadog Agent's `info` subcommand.

### Why can't the Agent cannot authenticate to MySQL?
```
    mysql
    -----
      - instance #0 [ERROR]: '(1045, u"Access denied for user \'datadog\'@\'localhost\' (using password: YES)")'
      - Collected 0 metrics, 0 events & 1 service check
```

Either the `'datadog'@'localhost'` user doesn't exist or the Agent is not configured with correct credentials. Review the Configuration section to add a user, and review the Agent's `mysql.yaml`.

### Database user lacks privileges
```
    mysql
    -----
      - instance #0 [WARNING]
          Warning: Privilege error or engine unavailable accessing the INNODB status                          tables (must grant PROCESS): (1227, u'Access denied; you need (at least one of) the PROCESS privilege(s) for this operation')
      - Collected 21 metrics, 0 events & 1 service check
```

The Agent can authenticate, but it lacks privileges for one or more metrics it wants to collect. In this case, it lacks the PROCESS privilege:

```
mysql> select user,host,process_priv from mysql.user where user='datadog';
+---------+-----------+--------------+
| user    | host      | process_priv |
+---------+-----------+--------------+
| datadog | localhost | N            |
+---------+-----------+--------------+
1 row in set (0.00 sec)
```

Review the Configuration section and grant the datadog user all necessary privileges. Do NOT grant all privileges on all databases to this user.

# Further Reading

### Blog posts

* See our blog series on [MySQL monitoring with Datadog](https://www.datadoghq.com/blog/monitoring-mysql-performance-metrics/).

### Knowledge Base

* [How to collect metrics from custom MySQL queries](https://help.datadoghq.com/hc/en-us/articles/115000133723-How-to-collect-metrics-from-custom-MySQL-queries)
* [How to monitor the MySQL slow query log](https://help.datadoghq.com/hc/en-us/articles/204770085-How-do-I-monitor-the-MySQL-slow-query-log-)

# Data collected

## Service Checks

## Metrics

{{< get-metrics-from-git "mysql" >}}