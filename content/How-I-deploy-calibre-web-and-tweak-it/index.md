+++
title = "How I deploy Calibre-Web and tweak it"
date = 2022-08-19T01:22:08+08:00
updated = 2022-08-19T01:22:08+08:00
in_search_index = true

[taxonomies]
tags = [ "calibre", "self-host", "proxy", "server", "GFW"]
categories = [ "Notes",]
archives = [ "archive",]
+++

# TL;DR

I managed to host a calibre-web with self signed certificate at non-standard port, and let it fetch metadata through proxy(fuck GFW).

And I found a solution to bulk upload my eBooks onto it.

<!-- more -->

# Originate from an IP

After moving place and settling down, I managed to have a publicly accessable IP. When you have a server, you want an IP. When you have an IP, you want a domain. When you have a domain, wow, you have all great things in the internet in your pocket(if your server can be that small like a NUC).

So I began to found services can be deployed on my server through an [awesome-list](https://github.com/awesome-selfhosted/awesome-selfhosted). 

#

# TODO

There is still some improvement to be done, but don't know where to start, I'll update this article as soon as I have done that.
