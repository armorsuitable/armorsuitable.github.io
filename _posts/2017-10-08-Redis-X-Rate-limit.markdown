---
layout: post
title:  "使用 Redis 限制接口调用频次"
date:   2017-04-26 20:30:05 +0800
categories: jekyll update
---


Redis 凭借基于在内存中访问的数据的优势，可以很大程度频繁访问某个数据。例如可以在接口的调用上来控制访问频次。

首先我们选用一个键来存储每分钟一个接口请求的次数
    
    keyHour = "tmp:bugTracker :" + (接口类型/ID字符串) + "user:requestRecord:hour"  + (userKey);
    
    keyDay = "tmp:bugTracker :" + (接口类型/ID字符串) + "user:requestRecord:day" + (userKey);
    

首先来实例化一个 redis 的客户端。
    
    redis = new RedisLibrary.RedisClient.getInstance();

//循环操作redis 数据
    
    do {
    
        if(! redis->exists(keyHour)){
            redis->set(keyHour , 0, 3600); // 最后的参数的是过期时间
        }
        
        requestRecordHour = redis->incr(keyHour);
        
        if(! redis->exists(keyDay)) {
            redis->set(keyDay, 0, 86400);
        }
        
        requestRecordDay = redis->incr(keyDay);
        
        // 该次请求次数
        requestAmount--; 
    
    }while( requestAmount > 0)
    

上面的意思是直接操作 redis 中存储的 KeyHour 和 keyDay 键的数据类限制数据操作。 (持续更新)
