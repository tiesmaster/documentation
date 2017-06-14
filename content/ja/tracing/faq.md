---
title: Tracing FAQ
kind: Documentation
autotocdepth: 2
hideguides: true
customnav: tracingnav
---

<!-- ### Naming

* Service names and resource names **must be lowercase, alphanumeric characters**.
* Service names and metadata keys are limited to **a maximum length of 50 characters**.
* Metadata and resource names have **a maximum length of 5000 bytes**. -->

### 命名規則

* サービス名とリソース名は、**小文字と英数字である必要があります**。
* サービス名とメタデータキーは、**最大50文字の長さ**に制限されます。
* メタデータとリソース名は、**最大長は5000バイト**に制限されます。


<!-- ### Data Retention

Trace data is currently being stored for about 36 hours. -->

### データ保持期間

トレースのデータは、約36時間保存されています。


<!-- ### Heroku

Heroku support is currently provided by the Datadog Heroku Buildpack. For more information, please reference [the project on Github](https://github.com/DataDog/heroku-buildpack-datadog) -->

### Heroku サポート

Herokuサポートは、Datadog Heroku Buildpackによって提供されています。詳細については、[Githubのプロジェクト](https://github.com/DataDog/heroku-buildpack-datadog)を参照してください。


<!-- ### Agent Source

Tracing is supported in the Datadog Agent for Linux and Docker. For other architectures, you can use the source install (coming soon). Trace agent source code is available on [the Github repo](https://github.com/DataDog/datadog-trace-agent). -->

### Agent のソースコード

LinuxおよびDockerのDatadog Agentで、トレースをサポートしています。他のアーキテクチャーを利用している場合は、ソースインストールからインストールすることができます。（近々公開予定）。トレースエージェントのソースコードは、[Github repo](https://github.com/DataDog/datadog-trace-agent)で閲覧することができます。


### Verifying Functionality

Tracing functionality is an opt-in process. To enable the trace agent add `apm_enabled: true` to your `datadog.conf` file and restart `datadog-agent`.
Verify this by tailing the logs:

    tail -f /var/log/datadog/trace-agent.log


When tracing is working properly you will see flushed payload messages similar to the following:

    2017-02-07 23:12:10 INFO (endpoint.go:140) - flushed payload to the API, time:185.409088ms, size:1437
    2017-02-07 23:12:20 INFO (endpoint.go:140) - flushed payload to the API, time:17.781515ms, size:753

### Connection refused

If you're receiving `error: [Errno 111] Connection refused` in your application logs, either the trace functionality is not running or your application is having trouble connecting to the Datadog Agent. This often happens because of a misconfiguration in Docker environments.  Tracing is supported in Docker, but you'll have to make sure:

1.  Your application is able to connect to the Agent (by default it submits to localhost). For Docker, you will likely need to specify the host or ip address. For example:
    tracer.configure(hostname="172.17.0.1")

2) The agent accepts requests by binding to 0.0.0.0 (or the appropriate IP address). You can set the agent binding via `bind_host` in `/etc/dd-agent/datadog.conf` or the DD_BIND_HOST env variable

For more info see [the docker-dd-agent documentation](https://github.com/DataDog/docker-dd-agent/blob/master/README.md#tracing--apm)
