---
layout: post
title: "Socket.io Cluster With Nginx as Reverse Proxy"
date: 2015-01-18T00:00:00+08:00
tags: socketio nodejs devops
---

Node.js by default runs on a single process and at max utilizes one CPU. To take the full advantage of a multi core system, multiple node processes can be run with a frontend proxy interfacing with the client.

This scenario can be supported in node.js using the cluster module. However with websocket additionally it is required to provide a mapping between the client session-id and the server handling the client requests. This is achieved by routing the clients based on their originating address.

Nginx versions since 1.3, supports websocket protocol and are well suited to provide this functionality.

Relevant configuration in nginx.conf

# for websocket clustering

{% highlight bash %}
upstream io_nodes{

ip_hash;

server 127.0.0.1:6001;

server 127.0.0.1:6002;

server 127.0.0.1:6003;

server 127.0.0.1:6004;

}

server {

listen 8080;

server_name io.yourhost.com;

location / {

proxy_set_header Upgrade $http_upgrade;

proxy_set_header Connection “upgrade”;

proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

proxy_set_header Host $host;

proxy_http_version 1.1;

proxy_pass http://io_nodes;

}

}
{% endhighlight %}

If your server is on Amazon EC2 then have to enable the ports for inbound traffic in your security groups. Found that when changing the setting on a running server, we have to edit the existing security groups and add new ports.

With the changes, the client is able to connect to proxy server and is routed to the node processes.

It is also useful to view the node process logs by prefixing the startup command as DEBUG=* node server.js

References:

- http://nginx.com/blog/websocket-nginx/
- http://socket.io/docs/using-multiple-nodes/#



