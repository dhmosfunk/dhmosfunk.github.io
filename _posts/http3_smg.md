---
layout: post
title: HTTP3 Smuggling
date: 2025-08-05
Author: dhmosfunk
tags: [http3, poc, research, request-smuggling, cve-2023-25950, haproxy, exploit]
comments: true
toc: true
---

## Introduction

HTTP request smuggling is a well known web vulnerability that occurs when an attacker is able to send a request that is interpreted differently by two/N web servers/reverse proxies. On this blog, we will explore how we can archive HTTP request smuggling using wrong HTTP/3 implementation in HAProxy and its behavior. This is a continuation of the previous research on CVE-2023-25950 [HTTP3ONSTEROIDS](https://github.com/dhmosfunk/HTTP3ONSTEROIDS).
