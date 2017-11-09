+++
date = "2017-10-09T08:36:54-07:00"
draft = true
title = "关于CDN注入系统的api设计"
+++

# api doc

## 注入api

### 测试地址:

api: `/v1/inject/task/add`


### 注入任务的engine及proxy数据流转
#### HTTP模块:

`/v1/inject/task/add`

```json
{
    "task_list": [
        {
            "task_type": "inject",
            "task_id": "1000000001",
            "inject_type": "base",
            "task_time_out": 10000,
            "api_id": "api_id_99",
            "priority": 5,
            "task_body":
                {
                    "publish_url": "http://vodfree-bj.wasu.cn/pcsan12/mams/vod/201702/06/23/20170206235403044ef342cdd.mp4",
                    "source_url": "http://192.168.15.115/repo/mh.mp4",
                    "verify_value": "73252e7b3f597ada1bcb12a2ac177a9f",
                    "action": ["f1", "f2"],
                    "scope": {
                        "store_master": "storage_zjnb",
                        "store_slave": "storage_sdjn",
                    }
                }

        },
    ]
}
```
### 中间个状态流转的数据包体:
```json
{
    "task_type": "inject",
    "task_id": "1000000001",
    "task_time_out": 10000,
    "inject_type": "base",
    "api_id": "api_id_99",
    "priority": 5,
    "task_body":
        {
            "publish_url": "http://vodfree-bj.wasu.cn/pcsan12/mams/vod/201702/06/23/20170206235403044ef342cdd.mp4",
            "source_url": "http://192.168.15.115/repo/mh.mp4",
            "verify_value": "73252e7b3f597ada1bcb12a2ac177a9f",
            "action": ["download", "md5_check"],
            "args": {
                "transcoding":[
                    {"since_time":10, "bitrate":10000}
                ]
            },
            "scope": {
                "store_master": "storage_zjnb",
                "store_slave": "storage_sdjn",
            }
        }
}
```


### Agent上传的状态:
```json
{
    "extra": {
        "info": "raise conn many",
        "cost": "5s",
    },
    "status": "success",
    "task_type": "inject",
    "task_id": "zus1499328786253",
    "api_id": "api_id_99",
    "timestamp": 1499240856,
    "message": {
        "code": "TI018",
        "hostname": "devops",
        "size": 1,
    }
}

```

### 返回给Boss的数据包体:
```
{
    "task_id": "aaaa",
    "task_type": "inject",
    "code": "t123",
    "file_size": 0
}
```
