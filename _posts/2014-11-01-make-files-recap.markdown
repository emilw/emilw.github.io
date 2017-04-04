---
author: emilw
comments: true
date: 2014-11-01 16:16:13+00:00
#layout: post
slug: make-files-recap
title: Make files recap
wordpress_id: 191
commentIssueId: 2
categories:
- C++
---

Below is a basic makefile:

```bash

//Setup what's the end goal, in this case it's client that should be resolved and what it depends on
all: client

//Link them to the final runnable file, e.g. client in this example
client: main.o HttpClient.o HttpResponse.o StringBufferHelper.o URL.o TCPClient.o g++ main.o HttpClient.o HttpResponse.o StringBufferHelper.o URL.o TCPClient.o -o client

//Compile all source to object files
main.o: main.cpp
	g++ -c main.cpp

HttpClient.o: HttpClient.cpp HttpClient.h
	g++ -c HttpClient.cpp HttpClient.h

HttpResponse.o: HttpResponse.cpp HttpResponse.h
	g++ -c HttpResponse.cpp HttpResponse.h

StringBufferHelper.o: StringBufferHelper.cpp StringBufferHelper.h
	g++ -c StringBufferHelper.cpp StringBufferHelper.h

URL.o: URL.cpp URL.h
	g++ -c URL.cpp URL.h

TCPClient.o: TCPClient.cpp TCPClient.h
	g++ -c TCPClient.cpp TCPClient.h

#hello.o: hello.cpp
#    g++ -c hello.cpp

clean:
	rm -rf *o client
```
