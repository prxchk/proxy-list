# [DRAFT] PRXCHK API

> [!TIP]
> To use our API, you will need a token, which can be obtained through our Telegram bot.

To obtain a list of available proxies, you need to make a GET request to the
address `https://api.prxchk.com/v2/proxies.txt`

First and foremost, it is important to remember that every request must include an access token. If you pass an empty,
incorrect, or expired token, the system will automatically add your IP address to a blacklist, and you will lose access
to the service. To successfully authenticate, you need to pass the token as a request parameter or an HTTP header:

> [!CAUTION]
> The token `AQBA5gEABCDEFDobwmAxV3XTSrDpU7bxaEaeeM2Wg` is used as a demonstration example. Please **DO NOT ATTEMPT TO USE IT** in your requests. Doing so may result in permanent blocking of your IP address 🤕

```bash
# strongly recommend to use HTTP-header
curl -H "Token: AQBA5gEABCDEFDobwmAxV3XTSrDpU7bxaEaeeM2Wg" https://api.prxchk.com/v2/proxies.txt

# it is possible to use the query parameter, but it is not secure enough
curl https://api.prxchk.com/v2/proxies.txt?token=AQBA5gEABCDEFDobwmAxV3XTSrDpU7bxaEaeeM2Wg
```

To prevent abuse of our service, we have implemented strict restrictions and automatically bind the token to the
sender's IP address. This limitation is in effect for 24 hours and prevents the token from being disclosed. In order to
reduce the load on our service, we also impose a limitation on the number of requests.

> ⚠️ The **optimal limit** is no more than **50 requests per minute**

> ⛔ The **maximum limit** is no more than **1000 requests per hour**

In case of systematic violation of the limitations, you risk losing the ability to use the service.

## Params

| Param      | Available                                                  |
|------------|------------------------------------------------------------|
| with_proto | _isset_                                                    |
| json       | _isset_                                                    |
| protocol   | `http`, `socks4`, `socks5`                                 |
| country    | _iso2 country code_ like `US`, `DE`, etc.                  |
| anonymity  | `elite` or `anonymous`                                     |
| port       | _int between 1 and 65535_                                  |
| order      | `uptime`, `stability`, `updated_at`, `timeout` or `random` |
| limit      | _int between 1 and 1000_                                   |

By default, the request returns all available proxies in TXT-format without specifying the protocol.

```bash
176.313.73.104:3128
67.205.290.164:8080
446.21.153.16:1080
...
```

If you consider it important, you can add the parameter `with_proto` to include the protocol information.

```bash
https://api.prxchk.com/v2/proxies.txt?with_proto

http://176.313.73.104:3128
socks4://67.205.290.164:8080
socsk5://446.21.153.16:1080
...
```

The parameters `protocol`, `country` and `port` can combine permissible values by separating them with commas, allowing
you to flexibly manage filtering rules for the list.

```bash
https://api.prxchk.com/v2/proxies.txt?protocol=http,socks5
# or
https://api.prxchk.com/v2/proxies.txt?country=US,DE
# or
https://api.prxchk.com/v2/proxies.txt?port=8080,3128
```

The parameters `country` and `port` can accept inverted values, which are equivalent to the statement "all except." To
achieve this, you need to add an exclamation mark (!) before the parameter value.

```bash
https://api.prxchk.com/v2/proxies.txt?country=!RU,!CN
# or
https://api.prxchk.com/v2/proxies.txt?port=!80,!8888
```

Each of the provided proxies has a very high level of anonymity. However, you may want to receive only those proxies
that do not use rotation under the hood.

```bash
https://api.prxchk.com/v2/proxies.txt?anonimity=elite
```

You have the option to conveniently sort the list of returned proxies based on one of the specified criteria. For any of
the parameters, sorting does not have an explicit ASC or DESC notation. The returned result is always generated from
"**best to worst**".

```bash
https://api.prxchk.com/v2/proxies.txt?order=uptime

176.313.73.104:3128 # 87%
67.205.290.164:8080 # 75%
446.21.153.16:1080 # 34%
...
```

```bash
# represents the number of consecutive successful checks performed
https://api.prxchk.com/v2/proxies.txt?order=stability

176.313.73.104:3128 # 5
67.205.290.164:8080 # 4
446.21.153.16:1080 # 3
...
```

```bash
https://api.prxchk.com/v2/proxies.txt?order=updated_at

176.313.73.104:3128 # 2024-02-01 12:56:46
67.205.290.164:8080 # 2024-02-01 11:34:35
446.21.153.16:1080 # 2024-02-01 10:12:15
...
```

```bash
https://api.prxchk.com/v2/proxies.txt?order=timeout

176.313.73.104:3128 # 0.1 s
67.205.290.164:8080 # 2.3 s
446.21.153.16:1080 # 5 s
...
```

```bash
# each request sets a random order for the list
https://api.prxchk.com/v2/proxies.txt?order=random

67.205.290.164:8080
446.21.153.16:1080
176.313.73.104:3128
77.338.79.191:5678
...
```

## Tips & Tricks

```bash 
# the most stable any-type proxy from Germany
curl -H 'AQBA5gEABCDEFDobwmAxV3XTSrDpU7bxaEaeeM2Wg' \
  https://api.prxchk.com/v2/proxies.txt?with_proto&order=stability&country=DE&limit=1
```

```bash 
# the freshest socks5 proxy not from China
curl -H 'AQBA5gEABCDEFDobwmAxV3XTSrDpU7bxaEaeeM2Wg' \
  https://api.prxchk.com/v2/proxies.txt?protocol=socks5&order=updated_at&country=!CN&limit=1
```

```bash 
# the fastest http proxy from  
curl -H 'AQBA5gEABCDEFDobwmAxV3XTSrDpU7bxaEaeeM2Wg' \
  https://api.prxchk.com/v2/proxies.txt?protocol=http&order=timeout&limit=1
```
