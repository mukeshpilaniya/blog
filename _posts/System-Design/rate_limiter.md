https://konghq.com/blog/how-to-design-a-scalable-rate-limiting-algorithm

https://github.com/gitgik/distributed-system-design/blob/master/designing_api_rate_limiter.md

https://www.enjoyalgorithms.com/blog/design-api-rate-limiter

https://tools.ietf.org/id/draft-polli-ratelimit-headers-00.html

Algorithms-
1. Token Bucket
2. leaky Bucket 
    It’s also easy to implement on a single server or load balancer and is memory efficient for each user, given the limited queue size.
    adv- traffic is smooth 
3. fixed window counter
    disadv- traffic is not smooth 
4. sliding logs 
5. sliding window countermy