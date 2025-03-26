[ASP.NET](http://ASP.NET) core applications need server to receive requests and send responses

  

# Kestrel

-Default cross platform http server for [asp.net](http://asp.net) core app

-Acts as both **development server** and **real application server**(receive requests from real internet). But in real world, we will only use Kestrel server as ==**application server and development server**== only, and in ==**production**==, we will use **==reverse proxy servers(IIS, Nginx, Apache,…)==**

  

Internet will send request to kestrel and the kestrel will fill the request in form of an object called as httpcontext(contains details of the request: requestheader, requestbody, cookies,…) then send to app code and app code will receivce and process and send back to kestrel then kestrel send back to internet

But kestrel doesn’t support advanced features on internet in modern day

![[/Untitled 115.png|Untitled 115.png]]

  

Real-world scenes:

internet will send request to the reverse proxy server and the RPS will offers some of the advanced features then its the same as above

![[/Untitled 1 6.png|Untitled 1 6.png]]

somes of the advanced features:

![[/Untitled 2 5.png|Untitled 2 5.png]]

[[Summary]]

[[launchSettings.json]]

[[HTTP]]