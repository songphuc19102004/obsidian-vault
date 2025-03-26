> Everything is a trade-off

  

# Scalability

## Horizontal scaling

Horizontal scaling is ==**adding multiple servers (nodes)**== as opposed to upgrading or swapping to the stronger one ==**(vertical scaling)**==, and ==**distribute**== the requests among those severs

→ We need to some how figure the way to distribute those request to the servers

## Load Balancing

![[/image 139.png|image 139.png]]

Load Balancer distributes or balance the traffic coming from people on the internet across our backend servers

When user send request, the request is sent to the IP of the Load Balancer instead of servers, and the Load Balancer will decide to route it to the appropriate servers

→ The Load Balancer need a public IP address, and backend servers technically don’t need them (private IP Address)

→ Can’t be contact directly → Better security

→ Just need one IPv4 for the Load Balancer, because IPv4 (32 bit) has come into scarcity (shortage) (now we are out of it)

> How Load Balancer decides which server to send to?

There are many factors we can based on:

- Based of Load (who is the busiest vs. the least busy)
- Round robin (sending each request to server in a circle)
    - Price: by bad luck, one of the servers only do hard request (computationaly difficult, like sending a lot of mails), RR still send more and more users to that server

We can have dedicated servers (like one server only contains HTML files, one only contains images). We can have different URLs or different host name (images.example.com, videos.something.com), and the Load Balancer can figure out the host HTTP header to decide the direction.