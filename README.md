# Security Log Generator API
Rapid API Hub Listing - https://rapidapi.com/shook25/api/security_log_generator_v1
API Link (you need a API key from Rapid API first) - https://security_log_generator_v1.p.rapidapi.com

As well as this README file, the API has in built swagger documentation which can be accessed via the base URL in a Web Browser without any parameters i.e.
```
https://security_log_generator_v1.p.rapidapi.com
```

![swagger_ui_usage](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/slg_swagger_usage.gif?raw=true)

***Important first note for access via browser:*** To access the swagger docmentation on the base url,  ```https://security_log_generator_v1.p.rapidapi.com```, via a browser like in the gif above, you need to add the API Key provided by Rapid API to the request headers for HTTP requests you send to the API.

For browsers, this generally needs a HTTP Interceptor to achieve as you can't as easily specify request headers. I am using Requestly which allows me to setup rules for when I access certain URLs. To provide Rapid API your API key and be able to access the interactive Swagger documentation, supply the following headers ```x-rapidapi-host``` and  ``` x-rapidapi-key``` like in the image below:

![requestly_http_interceptor_config](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/requestly.png?raw=true)

There are plenty of free alternatives to Requestly, just search for "HTTP Interceptor" or "Request Header Editor" in your browser extension marketplace. 

**If you don't need browser based access, you can still use** ```curl``` **as usual from the command line.**

## Description
The Security Log Generator API returns synthetically generated events that are commonly logged and utilised within SIEM solutions. 



## Basic Usage

The API is simple to use. There is an endpoint per available log type as follows:

- For Generic Intrusion Detection Events:
```
/events/ids 
```

- For Generic Web Access Events:
```
/events/access
```

- For Generic Endpoint Anti-Virus Events:
```
/events/endpoint
```

- For Generic Firewall Events:
```
/events/firewall
```

- For Linux Secure Log Events:
```
/events/linux_secure
```

### Simplest request:

- **Request**:
```
curl --request GET 
	--url https://security_log_generator_v1.p.rapidapi.com/events/ids 
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' 
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```


- **Output** (only showing 1 event for brevity, by default you will get 10 events without additional parameters):
```
[
  {
    "Alert Description": "PING NMAP",
    "Destination IP": "254.22.3.30",
    "Destination Port": "38139",
    "Flag": "ACK",
    "Protocol": "TCP",
    "Severity": "high_severity",
    "Source IP": "59.134.70.55",
    "Source Port": "24450",
    "Timestamp": "2025-01-14 20:37:01"
  }
]
```

The events are uniquely generated each time, if the results appear the same in a second event, this will be purely due to random chance.

**Example from the command line:**

![swagger_cli_usage](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/slg_cli_usage.gif?raw=true)



## Request Parameters 

There are 5 request parameters that can modify the behaviour of responses from the API.



### 1. Number of Events.

**Parameter:**
```
no_events
```

**Description:**

Defines the number of events to generate and return from the API. Takes a number between `1` and `1000`.

Defaults to `10`.

The higher the number, the longer the server will take to generate the events.

**Example Usage:**
```
curl --request GET 
	--url 'https://security_log_generator_v1.p.rapidapi.com/events/ids?no_events=1000' 
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' 
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```



### 2. Response Type

**Parameter:**
```
response_type
```

**Description:**

Defines the format to return the events in. 

Currently supports returning `json` or a `gzip` file containing the events.

Defaults to `json`.

**Example Usage:**
```
curl --request GET \
	--url 'https://security_log_generator_v1.p.rapidapi.com/events/ids?response_type=gzip' \
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' \
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```

*Note: gzip does not work from the swagger interface, use either a terminal or browser to retrieve gzip files instead of json.*



### 3. Start Datetime

**Parameter:**
```
start_datetime
```

**Description:**

The timestamp of the first event, all subsequent events generated will have a timestamp later than this timestamp. 

Takes a timestamp in the format `"%Y-%m-%d %H:%M:%S"`.
- *Note: Timestamps have to be URL-encoded, i.e. `:` must be represented by `%3A` and spaces by `%20`.*

Defaults to current server time. 

**Example Usage:**
```
curl --request GET 
	--url 'https://security_log_generator_v1.p.rapidapi.com/events/ids?start_datetime=2025-01-01%252000%253A0%253A00' 
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' 
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```


### 4. End Datetime
**Parameter:**
```
end_datetime
```
**Description:**

The timestamp of the last event, all previous events will have a timestamp earlier than this.

Takes a timestamp in the format `"%Y-%m-%d %H:%M:%S"`.
- *Note: Timestamps have to be URL-encoded, i.e. `:` must be represented by `%3A` and spaces by `%20`.*

Defaults to the current server time + delta of 24 hours.

**Example Usage:**
```
curl --request GET \
	--url 'https://security_log_generator_v1.p.rapidapi.com/events/ids?end_datetime=2025-01-02%252000%253A0%253A00' \
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' \
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```



### 5. Timestamp Spread

**Parameter:**
```
timestamp_spread
```

**Description:**

This parameter controls the distribution of events between the given start and end time. 

This controls the pattern of the events over time if plotted on a time chart.

Right now there are `3` possible values, `linear`, `bumpy_random` or `bumpy_pattern`.

- `linear` spreads all events evenly across the time period i.e. 

![linear](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/linear_pattern.png?raw=true)

- `bumpy_random` events will be randomly distributed which will form uneven peaks and dips.

![bumpy_random](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/bumpy_random.png?raw=true)

- `bumpy_pattern` events will be distributed over a sinusoidal waveform, creating even peaks and dips.

![bumpy_pattern](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/bumpy_pattern.png?raw=true)


**Example Usage:**
```
curl --request GET \
	--url 'https://security_log_generator_v1.p.rapidapi.com/events/ids?spread_type=bumpy_random' \
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' \
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```

You will need to generate a higher number of events to notice the spread of timestamps on a plot:

```
curl --request GET \
	--url 'https://security_log_generator_v1.p.rapidapi.com/events/ids?spread_type=bumpy_random&no_events=1000' \
	--header 'x-rapidapi-host: security_log_generator_v1.p.rapidapi.com' \
	--header 'x-rapidapi-key: YOUR_API_KEY_HERE'
```




## Current Features
The API is currently capable of generating events of the following types related to Cyber Security:

- Intrusion Detection System Events
    - Example Intrusion Detection Event:
    ```
    {
    "Alert Description": "Phishing",
    "Destination IP": "44.126.84.195",
    "Destination Port": "80",
    "Flag": "SYN",
    "Protocol": "HTTP",
    "Severity": "critical_severity",
    "Source IP": "138.125.164.151",
    "Source Port": "42933",
    "Timestamp": "2025-01-01 00:00:00"
    }
    ```

- Web Access Events
    - Example Web Access Event:
    ```
    {
    "Bytes": "8875",
    "Client IP": "151.192.208.168",
    "Method": "DELETE",
    "Protocol": "HTTP/1.1",
    "Referrer": "http://ramsey-obrien.infosearch/main.jsp",
    "Resource": "search/main",
    "Status": "200",
    "Timestamp": "2025-01-01 00:00:0",
    "User": "david",
    "User Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:89.0) Gecko/20100101 Firefox/89.0"
    }
    ```

- Endpoint Protection Events
    - Example Endpoint Protection Event:
    ```
    {
    "Action Taken": "Quarantine",
    "Computer": "QFAZAAJE83",
    "Event Type": "Malware Detected",
    "File Hash": "5b5e0160-0612-4812-b425-a414ffcb3129",
    "File Name": "password_stealer.exe",
    "File Path": "C:\\Users\\david\\security\\goal\\program\\girl\\password_stealer.exe",
    "Threat Name": "['Exploit.Win32.Example']",
    "Timestamp": "2025-01-01 00:00:00",
    "User": "maria"
    }
    ```

- Firewall Events
    - Example Firewall Event:
    ```
    {
    "Action": "Deny",
    "Action Taken": "REJECT",
    "Destination IP": "24.175.240.133",
    "Destination Port": "2403",
    "Direction": "Inbound",
    "Protocol": "UDP",
    "Source IP": "157.53.137.238",
    "Source Port": "21383",
    "Timestamp": "2025-01-01 00:00:00"
    }
    ```

- Linux Secure Events
    - Example Linux Secure Event:
    ```
    {
    "Event Message": "Failed password for user root from 55.90.42.180 port 346 ssh2",
    "Host": "COGEAGTV18",
    "Process ID": "[25573]",
    "Service": "sshd",
    "Timestamp": "2025-01-01 00:00:00"
    }
    ```


## In-Development Features
Features that are currently in or planned for development.

- NCSA Common Log Format Response Option 

- Additional Event Endpoints 
    - i.e. Windows Event Logs & other various common logs security based logs

- Custom Log Formats 
    - i.e. User defined log formats for when the generic endpoints are insufficient

- Threat Detection Use Cases
    - i.e. Generation of logs that would surface in typical types of attack scenarios.

- Max Event Generation Increase
    - As the API is currently considered in Beta, the official release will increase the max number of events that can be generated from a single API call.

## Expected Performance and Volumes

### Volumes

Log Type | ids | access | endpoint | firewall | linux_secure | 
--- | --- | --- | --- |--- |--- |
Kilobytes (10 events) | 2.4 | 3.5 | 1.7 | 2.2 | 3.5 | 
Kilobytes (100 events) | 23.5 | 35.6 | 16.1 | 22.5 | 37.5 |
Kilobytes  (1,000 events) | 239.6 | 354.1 | 172.8 | 225.5 | 395.1 |


### Performance

**notes on performance:**
 - The main limiting factor of performance is cloud hosting. 
 - Cloud hosting is expensive in 2025 and I need capital to be able to upgrade the hosting to more performant platforms and hardware.
 - The more users we reach, the more cores we can purchase to take full advantage parallel processing capability of the backend code.
 - We have recently achieved performance boosts compared to the below tables due to removing dependancy on the Faker library.

#### Max Performance Without Hardware Considerations (Code Limited)

Log Type | ids | access | endpoint | firewall | linux_secure | 
--- | --- | --- | --- |--- |--- |
Time in seconds to complete request (10 events) | 0.068s | 0.147s | 0.096s | 0.075s | 0.112s | 
Time in seconds to complete request (100 events) | 0.094s | 0.446s | 0.282s | 0.095s | 0.265s |
Time in seconds to complete request (1,000 events) | 0.180s | 3.066s | 1.744s | 0.172s | 1.732s |

#### Max Performance On Current Cloud Hosting Provider (Cloud Hosting Limited)

Log Type | ids | access | endpoint | firewall | linux_secure | 
--- | --- | --- | --- |--- |--- |
Time in Seconds (10 events) | 0.093s | 0.32s | 0.22s | 0.1s | 0.24s | 
Time in Seconds (100 events) | 0.12s | 1.32s | 0.77s | 0.11s | 0.84s |
Time in Seconds (1,000 events) | 0.474s | 11.43s | 6.48s | 0.44s | 6.19s |

## Hint, Tips and Other Notes

- access events tend to take the longest to generate, firewall or ids are the fastest.

- use the jq tool in terminal to 'prettify' the json output, for example:

![jq_pretty_output](https://github.com/cruikshank25/Security-Log-Generator-API-Docs/blob/main/pretty_output.png?raw=true)


