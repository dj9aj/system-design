<!-- MarkdownTOC -->

- [Why CDN, not web storage / distributed cache?](#why-cdn-not-web-storage--distributed-cache)
- [How to put an item on CDN](#how-to-put-an-item-on-cdn)
- [How to get an item from CDN](#how-to-get-an-item-from-cdn)

<!-- /MarkdownTOC -->


## Why CDN, not web storage / distributed cache? 
* Web storage or distributed cache could not necessarily be deployed as close as CDN to the end user. 
* Static resource such as video or images are so big. 
* If they were to serve from web storage / distributed cache, it will be 
  1. a huge requirement for network bandwidth
  2. high latency for such content

## How to put an item on CDN

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│        1. A user has a video xxx.avi to put on CDN and wants to access it as        │
│                        https://video.yourcompany.com/xxx.avi                        │
│                                                                                     │
└──────────────────────────────────────────┬──────────────────────────────────────────┘
                                           │                                           
                                           ▼                                           
┌────────────────────────────────────────────────────────────────────────────────────┐ 
│                                                                                    │ 
│     2. CDN Provider provides a domain name for you such as xxx.akamai.cdn.com      │ 
│                                                                                    │ 
└───────────────────────────────────────────┬────────────────────────────────────────┘ 
                                            │                                          
                                            │                                          
                                            ▼                                          
 ┌────────────────────────────────────────────────────────────────────────────────────┐
 │ 3. a CName mapping between your preferred domain name and the one provided by CDN  │
 │              provider is configured on the .com DNS authority server               │
 │                                                                                    │
 │                    video.yourcompany.com => xxx.akamai.cdn.com                     │
 │                                                                                    │
 └──────────────────────────────────────────┬─────────────────────────────────────────┘
                                            │                                          
                                            │                                          
                                            ▼                                          
 ┌────────────────────────────────────────────────────────────────────────────────────┐
 │ 4. A CName mapping between cdn domain name returned from 3 and the CDN global load │
 │        balancer address is configured on the .cdn.com DNS authority server         │
 │                                                                                    │
 │                 xxx.akamai.cdn.com => global.loadbalancer.cdn.com                  │
 │                                                                                    │
 └────────────────────────────────────────────────────────────────────────────────────┘
                                           │                                           
                                           │                                           
                                           │                                           
                                           ▼                                           
  ┌─────────────────────────────────────────────────────────────────────────────────┐  
  │           5. The video is uploaded to CDN, which could be accessed by           │  
  │                      https://video.yourcompany.com/xxx.avi                      │  
  │                                                                                 │  
  └─────────────────────────────────────────────────────────────────────────────────┘  
```

## How to get an item from CDN

```
 ┌────────────────────────────────────────────────────────────────────────────────────┐
 │                                                                                    │
 │              1. A user accesses https://video.yourcompany.com/xxx.avi              │
 │                                                                                    │
 └─────────────────────────────────────────┬──────────────────────────────────────────┘
                                           │                                           
                                           │                                           
                                           │                                           
                                           ▼                                           
┌────────────────────────────────────────────────────────────────────────────────────┐ 
│          2. A request is sent to .com DNS authority server for resolving           │ 
│    video.yourcompany.com. Based on the CName mapping configured, a domain name     │ 
│                          xxx.akamai.cdn.com is returned.                           │ 
│                                                                                    │ 
│                    video.yourcompany.com => xxx.akamai.cdn.com                     │ 
│                                                                                    │ 
└────────────────────────────────────────────────────────────────────────────────────┘ 
                                           │                                           
                                           │                                           
                                           │                                           
                                           ▼                                           
┌────────────────────────────────────────────────────────────────────────────────────┐ 
│        3. A request is sent to .cdn.com DNS authority server for resolving         │ 
│      xxx.akamai.cdn.com. Based on the CName mapping configured, a domain name      │ 
│                    xxx.global.loadbalander.cdn.com is returned.                    │ 
│                                                                                    │ 
│               xxx.akamai.cdn.com => xxx.global.loadbalancer.cdn.com                │ 
│                                                                                    │ 
└────────────────────────────────────────────────────────────────────────────────────┘ 
                                           │                                           
                                           │                                           
                                           │                                           
                                           │                                           
                                           ▼                                           
┌────────────────────────────────────────────────────────────────────────────────────┐ 
│4. The CDN Global balancer xxx.global.loadbalancer.cdn.com returns an IP address    │ 
│based on the following conditions:                                                  │ 
│                                                                                    │ 
│a). User's IP address                                                               │ 
│b). User's network operator such as ATT                                             │ 
│c). Request url to determine which CDN server has it                                │ 
│d). Current traffic distribution condition                                          │ 
│                                                                                    │ 
└────────────────────────────────────────────────────────────────────────────────────┘ 
                                           │                                           
                                           │                                           
                                           │                                           
                                           ▼                                           
┌────────────────────────────────────────────────────────────────────────────────────┐ 
│                                                                                    │ 
│   5. A user accesses the resource by using https://{returned ip address}/xxx.avi   │ 
│                                                                                    │ 
└────────────────────────────────────────────────────────────────────────────────────┘ 
```