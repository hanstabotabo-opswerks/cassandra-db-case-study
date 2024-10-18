# Wrong ports configured
the main cause of this problem is that the endpoint set up has the wrong port. to solve this create another nodeport service for the splunk instance. 

```bash
curl -k http://139.162.8.62:32756/en-US/services/collector -H "Authorization: Splunk 63340ac5-5f37-4883-89ad-1e670f99220d" -d '{"event":"Hello, World!"}'
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<!-- 
    This is a static HTML string template to render errors.  To edit this
    template, see appserver/mrsparkle/lib/error.py. 
-->
```

# HTTP 404 not found
this pod is running and is successfully working with other kubernetes components in the namespace but it can't reach the endpoint set up. this error disappeared after creating another nodeport service

```bash
2024-10-13T05:34:09.465Z    info    internal/retry_sender.go:118    Exporting failed. Will retry the request after interval.    {"kind": "exporter", "data_type": "logs", "name": "splunk_hec/platform_logs", "error": "HTTP 404 \"Not Found\"", "interval": "15.146878891s"}
```

# Cant connect to server
this error occurs when i try to pass in sample data to the k8s_events in splunk, the service can't seem to be reached because it cannot reach the server. troubleshoot it by editting the k8s_metrics in splunk-operator directly

```bash
curl -k http://139.162.8.62:30088/services/collector/event -H "Authorization: Splunk 63340ac5-5f37-4883-89ad-1e670f99220d" -d '{"event":"Hello, World!"}'
curl: (7) Failed to connect to 139.162.8.62 port 30088 after 191 ms: Couldn't connect to server
```

# Connection reset by peer
the splunk server is the one that's denying my access using curl. the connection was finally established but it was unexpectedly terminated by the server. this is the final the final hindrance, to solve this change http into https. after you run (curl -k https://139.162.8.62:30088/services/collector/event -H "Authorization: Splunk 63340ac5-5f37-4883-89ad-1e670f99220d" -d '{"event":"Hello, World!"}' the curl was a success and finally sent an event to the k8_events index.


```bash
curl -k http://139.162.8.62:30088/services/collector/event -H "Authorization: Splunk 63340ac5-5f37-4883-89ad-1e670f99220d" -d '{"event":"Hello, World!"}'
curl: (56) Recv failure: Connection reset by peer
```
