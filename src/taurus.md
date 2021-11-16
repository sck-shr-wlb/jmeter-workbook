# Taurus

## Install

- Prerequisite: Python 3.7+

```sh
pip install Cython # only Mac OS
pip install bzt
```

```sh
pip install --upgrade bzt
```

### via brew

```sh
brew install bzt
```

```sh
brew upgrade bzt
```

## Run Command

ref: [https://gettaurus.org/docs/CommandLine](https://gettaurus.org/docs/CommandLine)

```sh
bzt [option] [config]
```

```sh
bzt -h
```

## Command-Line Options Override

```sh
bzt -o modules.jmeter.path=alternate/jmeter/bin/jmeter config.yml
```

- config.yml

```yml
modules:
  jmeter:
    path: ./local/jmeter
    properties:
      log_level: DEBUG
  console:
    disable: false
```

## Pass/Fail Criteria

ref:

- [https://gettaurus.org/docs/PassFail/](https://gettaurus.org/docs/PassFail/)
- [https://gettaurus.org/docs/Reporting/](https://gettaurus.org/docs/Reporting/)

```yml
reporting:
  - module: passfail
    criteria:
      - avg-rt of IndexPage>150ms for 10s, stop as failed
      - fail of CheckoutPage>50% for 10s, stop as failed
```

### The above example use short form for criteria, its general format is the following

> `subject` of `[label]` `condition` `threshold` `[logic]` `[timeframe]`, `action as status`

### Explanation

- `subject` is the KPI that will be compared, listed below.
- `[label]` is a sample label. If omitted, it is applied for overall.
- `condition` is the comparison operator, one of >, <, >=, <=, =, == (same as =). Keep in mind that no spaces should surround the operator.
- `threshold` is the value to compare with, some KPIs allow percentage thresholds.
- `[logic]` is the way value is aggregated within timeframe, see more details below.
- `[timeframe]` is a number of seconds the comparison must be valid; if timeframe is omitted, then the cumulative value for whole test will be used for comparison.
- `action` is either `stop` or `continue`, default is stop, if you have chosen to continue, the fail status will be applied at the end of the test execution.
- `status` is either failed (default) or non-failed.

| subject | of  | [label]   | condition | threshold | [logic] | [timeframe] | ,   | action as status |
| ------- | --- | --------- | --------- | --------- | ------- | ----------- | --- | ---------------- |
| avg-rt  | of  | IndexPage | >         | 150ms     | for     | 10s         | ,   | stop as failed   |

### The minimal form is `subject` `[condition]` `threshold`. Parameters in [square brackets] are optional.

Possible subjects are:

- `avg-rt` - average response time, e.g. avg-rt>2s500ms.
- `avg-lt` - average latency, e.g. avg-lt for my-label>2.
- `avg-ct` - average connect time, e.g. avg-ct>100ms.
- `stdev-rt` - standard deviation for full response time, e.g. stdev-rt>0.5.
- `p...` - percentile timing, e.g. p90>1s for 10s, p99.9>10s, stop as failed.
- `hits` - number of responses, e.g. hits for my-label>100 for 5s, stop as non-failed.
- `bytes` - response data size, e.g. bytes for my-label>10MB, possible types are B, kB and MB.
- `succ or success` - successful responses, supports percentage threshold, e.g. succ<100% .
- `fail or failures` - failed responses, supports percentage threshold, e.g. failures>50% for 5s, stop as failed.
- `rc...` - percentage of specific responce code amount of all requests. Response code may be specified using wildcards ? and _, e.g. rc500>20 for 5s, stop as failed, rc4??>20%, rc_>=10 for 1m, rcException>99% for 1m, continue as failed.

### Timeframe Logic

If no timeframe logic is specified, the pass/fail rule is processed at the very end of test, against total aggregate data. To apply checks in the middle of the test, please use one of possible timeframe logic options:

- `for` means that each value inside timeframe has to trigger the condition, for example avg-rt>1s for 5s means each of consecutive 5 seconds has average response time greater that 1 second.
- `within` means all values inside timeframe gets aggregated as average or sum (depends on KPI nature), then comparison is made.
- `over` is very similar to within, but the comparison is made only if full timeframe available (within will trigger even if partial timeframe matches the criteria).

## Generating Test Reports

ref: [https://gettaurus.org/docs/Reporting/](https://gettaurus.org/docs/Reporting/)

### Reporter modules

- console, that shows live test stats in your terminal
- blazemeter, that provides interactive online test reports
- final_stats, that provides post-test summary stats
- junit-xml, that generates test stats in JUnit-compatible format

- Example

```yml
reporting:
  - module: final-stats
    summary: true # overall samples count and percent of failures
    percentiles: true # display average times and percentiles
    summary-labels: false # provides list of sample labels, status, percentage of completed, avg time and errors
    failed-labels: false # provides list of sample labels with failures
    test-duration: true # provides test duration
    dump-xml: final-stats.xml
    dump-csv: final-stats.csv
  - module: console
  - module: blazemeter
  - module: junit-xml
    filename: junit.xml
    data-source: pass-fail
```

## Generate Jmeter Report

```yml
modules:
  jmeter:
    csv-jtl-flags:
      saveAssertionResultsFailureMessage: true
      sentBytes: true

services:
  - module: shellexec
    post-process:
      - '[ -f "${TAURUS_ARTIFACTS_DIR}/kpi.jtl" ] && /path/to/jmeter -g "${TAURUS_ARTIFACTS_DIR}/kpi.jtl" -o "${TAURUS_ARTIFACTS_DIR}/dashboard" -j "${TAURUS_ARTIFACTS_DIR}/generate_report.log" '
```

## Example config.yml

```yml
modules:
  jmeter:
    memory-xmx: 4G
    path: /path-to/jmeter
    properties:
      prop_name: prop value
    system-properties:
      sun.net.http.allowRestrictedHeaders: 'true'
    csv-jtl-flags:
      saveAssertionResultsFailureMessage: true
      sentBytes: true

reporting:
  - module: passfail
    criteria:
      - avg-rt>1.5s, continue as failed
  - module: final-stats
    summary: true # overall samples count and percent of failures
    percentiles: true # display average times and percentiles
    summary-labels: false # provides list of sample labels, status, percentage of completed, avg time and errors
    failed-labels: false # provides list of sample labels with failures
    test-duration: true # provides test duration
    dump-xml: final-stats.xml
    dump-csv: final-stats.csv
    #- module: blazemeter
  - module: junit-xml
    filename: junit.xml
    data-source: pass-fail

services:
  - module: shellexec
    post-process:
      - '[ -f "${TAURUS_ARTIFACTS_DIR}/kpi.jtl" ] && /path-to/jmeter -g "${TAURUS_ARTIFACTS_DIR}/kpi.jtl" -o "${TAURUS_ARTIFACTS_DIR}/dashboard" -j "${TAURUS_ARTIFACTS_DIR}/generate_report.log" '
```
