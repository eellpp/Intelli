---
layout: post
title: "Protocol Buffers for Sending Data Between Services"
date: 2015-11-22T00:00:00+08:00
tags: tools
---

Google Protocol Buffers provides a useful method to send data between services in binary format. Once the schema is defined then there are parsers in various languages that can consume the data. This handles the case of future updates when there are modifications to schema and it has to be transparently handled across services without breaking them. 


https://developers.google.com/protocol-buffers/


