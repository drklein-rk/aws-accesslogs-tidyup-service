# aws-accesslogs-tidyup-service
Access your load balancer logfiles from your s3 bucket in R for further analyses.

The log-tidyup-service reads logfiles in a certain timeframe without downloading and 
makes multiple log files readable in a data.frame/tibble or as a list.

Documentation: [Access logs for your application load balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html)

# OUTPUT (als Tablle: field | example value)

```
type: https
time: 2020-09-25T08:44:26.769625Z
elb: app/service-alb/cf001cd83398f7e2
client:port: 123.123.123.123:1111
target:port: 123.123.123.123:1111
request_processing_time: 0.001 (ms)
target_processing_time: 0.001 (ms)
response_processing_time: 0.001 (ms)
elb_status_code: 403
target_status_code: 403
received_bytes:  120
sent_bytes: 345
request: "GET https://123.123.123.123:443/ HTTP/1.1"
user_agent: "libwww-perl/6.47"
ssl_cipher: ABCDE-FGH-IJK128-LMN-OPQ256
ssl_protocol: TLSv1.2
target_group_arn: arn:aws:elasticloadbalancing:eu-central-1:xxxxxxxxxxxx:targetgroup/service-http/b12b3abe45d678c9
trace_id: "Root=1-5f6dadea-4efd7070a5cce59db8ca4625"
domain_name: "-"
chosen_cert_arn: "arn:aws:acm:eu-central-1:xxxxxxxxxxxx:certificate/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
matched_rule_priority: 0
request_creation_time: 2020-09-25T08:44:26.767000Z
"redirect_url": "forward"
"error_reason": "-"
"target:port_list": "-" "123.123.123.123:1111"
"target_status_code_list": "403"
"classification": "-"
"classification_reason": "-"
```

# HOW TO USE
**FUNCTION:**

```r
get_s3_logfiles(bucket, start_datetime, end_datetime, time_window, logfile_prefix, logfile_filter, return_as = [tibble, data.frame, list], ...)
```

**ARGUMENTS:**

- `bucket` must be the name of your s3 bucket.
- `start_datetime`  Format: list(date = "YYYY-MM-DD", time = "hh:mm:ss").
- `end_datetime`  Optional. If set, it must have the same format as start_datetime NOTE: end_datetime will override time_window
- `time_window` Format: list(unit = "H", duration = 2) default duration are 2 hours, optinal values for unit are ["D", "H", "M", "S"]
- `logfile_prefix` is used by aws.s3::get_bucket() function. Can be a single characterstring or a list: list(aws_account_id = "xxxxx", region = "ab-xyz-12", bucket_prefix = "character")
- `logfile_filter` Format: list(pattern = c("regex"), field = c("one of the output columns")) pattern and field must be of same length. filter looks for pattern[i] in field[i]
- `return_as` default = tibble, can be data.frame, tibble and list

**DETAILS**
- `logfile_prefix` The file names of the access logs use the following format: bucket[/prefix]/AWSLogs/aws-account-id/elasticloadbalancing/region/yyyy/mm/dd/aws-account-id_elasticloadbalancing_region_load-balancer-id_end-time_ip-address_random-string.log.gz. NOTE that logfile_prefix isn't the same as [/prefix] in the logfile name format. The logfile_prefix must contain the whole string up to region - region included - and end with a slash: "bucket[/prefix]/AWSLogs/aws-account-id/elasticloadbalancing/region/" or a list, which contains your aws_account_id, region and optional the bucket_prefix: The [/prefix] (logical hierarchy) in the bucket. If you don't specify a bucket_prefix, the logs are placed at the root level of the bucket. See EXAMPLE for more details.
[Access log files](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html)

**DEPENDENCIES**

- `aws.s3`
- `dplyr`
- `lubridate`

**EXAMPLE**



