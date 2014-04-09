gomkafka
========

Per rsyslog external plugin requirements, we should read from stdin, use a single thread, and ensure no output is sent to stdout.

## Usage

```shell
$ bin/zookeeper-server-start.sh config/zookeeper.properties
$ bin/kafka-server-start.sh config/server.properties
$ gomkafka "client id" localhost:9092 monitoring
(CTRL-C to exit)
```

## Integration with Rsyslog

Add this to your `rsyslog.conf`

```shell
module(load="omprog")

if $rawmsg contains "[monitoring]" then
  action(type="omprog"
         binary="/path/to/gomkafka" --param1=\"client id\" --param2=localhost:9092 --param3=monitoring"
```

The `$rawmsg` is a default rsyslog property representing the raw message. The statement "if `property` contains `value` then..." conditionally executes gomkafka where contains must exactly match the `value`. It  cannot be a regex.

Additionally, you may filter on severity, per RFC 3164, using the `$syslogseverity` or `$syslogseverity-text` property.

```
Numerical         Severity
  Code

    0       Emergency: system is unusable
    1       Alert: action must be taken immediately
    2       Critical: critical conditions
    3       Error: error conditions
    4       Warning: warning conditions
    5       Notice: normal but significant condition
    6       Informational: informational messages
    7       Debug: debug-level messages
```

## Testing

```shell
$ echo "foo" | gomkafka "client id" localhost:9092 monitoring
```

## Author

Jeff Chao, @thejeffchao, http://thejeffchao.com
