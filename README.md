# logf Appender and Encoder for systemd Journal

[![GoDoc](https://godoc.org/github.com/ssgreg/logfjournald?status.svg)](https://godoc.org/github.com/ssgreg/logfjournald)
[![Build Status](https://travis-ci.org/ssgreg/logfjournald.svg?branch=master)](https://travis-ci.org/ssgreg/logfjournald)
[![Go Report Status](https://goreportcard.com/badge/github.com/ssgreg/logfjournald)](https://goreportcard.com/report/github.com/ssgreg/logfjournald)
[![Coverage Status](https://coveralls.io/repos/github/ssgreg/logfjournald/badge.svg?branch=master)](https://coveralls.io/github/ssgreg/logfjournald?branch=master)

Package `logfjournald` provides `logf` Appender and Encoder for systemd Journal. It supports structured logging and bulk operations with zero allocs.

## Example

The following example creates the new `logf` logger with `logfjournald` appender.

```go
package main

import (
    "runtime"

    "github.com/ssgreg/logf"
    "github.com/ssgreg/logfjournald"
)

func main() {
    // Create journald Appender with default journald Encoder.
    appender, appenderClose := logfjournald.NewAppender(logfjournald.NewEncoder.Default())
    defer appenderClose()

    // Create ChannelWriter with journald Encoder.
    writer, writerClose := logf.NewChannelWriter(logf.ChannelWriterConfig{
        Appender: appender,
    })
    defer writerClose()

    // Create Logger with ChannelWriter.
    logger := logf.NewLogger(logf.LevelInfo, writer)

    logger.Info("got cpu info", logf.Int("count", runtime.NumCPU()))
}
```

The JSON representation of the journal entry this generates:

```json
{
  "TS": "2018-11-01T07:25:18Z",
  "PRIORITY": "6",
  "LEVEL": "info",
  "MESSAGE": "got cpu info",
  "COUNT": "4",
}
```
