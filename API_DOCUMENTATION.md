# 夸克网盘 HTTP API 文档

本文档从代码层面分析 QuarkPanTool 项目中使用的所有 HTTP API。

---

## 1. 获取分享页面 Token (get_stoken)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/share/sharepage/token`

**请求方法:** POST

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| __dt | int | 随机数 (100-9999) |
| __t | int | 13位时间戳 |

**请求体 (JSON):**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pwd_id | string | 分享链接的 pwd_id |
| passcode | string | 提取码（可为空） |

**返回值:**
```json
{
    "status": 200,
    "data": {
        "stoken": "分享页面访问令牌"
    },
    "message": "状态消息"
}
```

---

## 2. 获取分享页面详情 (get_detail)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/share/sharepage/detail`

**请求方法:** GET

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| pwd_id | string | 分享链接的 pwd_id |
| stoken | string | 分享页面访问令牌 |
| pdir_fid | string | 父目录文件ID，根目录为 `0` |
| force | string | 固定值 `0` |
| _page | string | 页码 |
| _size | string | 每页数量，固定值 `50` |
| _sort | string | 排序方式 `file_type:asc,updated_at:desc` |
| __dt | int | 随机数 (200-9999) |
| __t | int | 13位时间戳 |

**返回值:**
```json
{
    "data": {
        "is_owner": 0,  // 是否为文件所有者
        "list": [
            {
                "fid": "文件ID",
                "file_name": "文件名",
                "file_type": 0,  // 文件类型
                "dir": true,  // 是否为目录
                "pdir_fid": "父目录ID",
                "include_items": 0,  // 包含的子项数量
                "share_fid_token": "分享文件令牌",
                "status": 1  // 文件状态
            }
        ]
    },
    "metadata": {
        "_total": 100,  // 总数量
        "_size": 50,    // 每页限制数量
        "_count": 50    // 当前页数量
    }
}
```

---

## 3. 获取文件列表（已排序）(get_sorted_file_list)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/file/sort`

**请求方法:** GET

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| pdir_fid | string | 父目录文件ID，根目录为 `0` |
| _page | string | 页码 |
| _size | string | 每页数量，默认 `100` |
| _fetch_total | string | 是否获取总数 `true/false` |
| _fetch_sub_dirs | string | 是否获取子目录 `1` |
| _sort | string | 排序方式 |
| __dt | int | 随机数 (100-9999) |
| __t | int | 13位时间戳 |

**返回值:**
```json
{
    "data": {
        "list": [
            {
                "fid": "文件ID",
                "file_name": "文件名",
                "dir": true  // 是否为目录
            }
        ]
    }
}
```

---

## 4. 获取用户信息 (get_user_info)

**URL:** `https://pan.quark.cn/account/info`

**请求方法:** GET

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| fr | string | 固定值 `pc` |
| platform | string | 固定值 `pc` |

**返回值:**
```json
{
    "data": {
        "nickname": "用户昵称"
    }
}
```

---

## 5. 创建目录 (create_dir)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/file`

**请求方法:** POST

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| __dt | int | 随机数 (100-9999) |
| __t | int | 13位时间戳 |

**请求体 (JSON):**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pdir_fid | string | 父目录ID，根目录为 `0` |
| file_name | string | 文件夹名称 |
| dir_path | string | 目录路径，空字符串 |
| dir_init_lock | boolean | 目录初始化锁，固定 `false` |

**返回值:**
```json
{
    "code": 0,  // 0 表示成功，23008 表示同名冲突
    "data": {
        "fid": "新创建的文件夹ID"
    },
    "message": "状态消息"
}
```

---

## 6. 保存分享文件到网盘 (get_share_save_task_id)

**URL:** `https://drive.quark.cn/1/clouddrive/share/sharepage/save`

**请求方法:** POST

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| __dt | int | 随机数 (600-9999) |
| __t | int | 13位时间戳 |

**请求体 (JSON):**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| fid_list | array[string] | 要保存的文件ID列表 |
| fid_token_list | array[string] | 文件令牌列表 |
| to_pdir_fid | string | 目标父目录ID |
| pwd_id | string | 分享链接的 pwd_id |
| stoken | string | 分享页面访问令牌 |
| pdir_fid | string | 源父目录ID，固定 `0` |
| scene | string | 场景，固定 `link` |

**返回值:**
```json
{
    "data": {
        "task_id": "任务ID"
    }
}
```

---

## 7. 获取文件下载地址 (quark_file_download)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/file/download`

**请求方法:** POST

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| sys | string | 系统类型 `win32` |
| ve | string | 版本号 `2.5.56` |
| ut | string | 空字符串 |
| guid | string | 空字符串 |

**请求头 (特殊):**
| 参数名 | 说明 |
|--------|------|
| User-Agent | 夸克云盘客户端 UA: `quark-cloud-drive/2.5.56 Chrome/100.0.4896.160 Electron/18.3.5.12-a038f7b798` |

**请求体 (JSON):**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| fids | array[string] | 要下载的文件ID列表 |

**返回值:**
```json
{
    "status": 200,
    "data": [
        {
            "file_name": "文件名",
            "download_url": "下载地址",
            "pdir_fid": "父目录ID"
        }
    ],
    "message": "状态消息"
}
```

---

## 8. 提交/查询任务状态 (submit_task)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/task`

**请求方法:** GET

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| task_id | string | 任务ID |
| retry_index | int | 重试次数索引 |
| __dt | int | 固定值 `21192` |
| __t | int | 13位时间戳 |

**返回值:**
```json
{
    "message": "ok",
    "code": 0,  // 32003 表示容量不足，41013 表示文件夹不存在
    "data": {
        "status": 2,  // 2 表示任务完成
        "task_title": "分享-转存",
        "save_as": {
            "to_pdir_name": "目标文件夹名称"
        }
    }
}
```

---

## 9. 创建分享任务 (get_share_task_id)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/share`

**请求方法:** POST

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |

**请求体 (JSON):**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| fid_list | array[string] | 要分享的文件ID列表 |
| title | string | 分享标题 |
| url_type | int | 链接类型: 1=公开, 2=私密 |
| expired_type | int | 过期类型 |
| passcode | string | 提取码（url_type=2时需要） |

**返回值:**
```json
{
    "data": {
        "task_id": "分享任务ID"
    }
}
```

---

## 10. 获取分享ID (get_share_id)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/task`

**请求方法:** GET

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |
| task_id | string | 分享任务ID |
| retry_index | string | 重试索引，固定 `0` |

**返回值:**
```json
{
    "data": {
        "share_id": "分享ID"
    }
}
```

---

## 11. 提交分享获取链接 (submit_share)

**URL:** `https://drive-pc.quark.cn/1/clouddrive/share/password`

**请求方法:** POST

**Query 参数:**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| pr | string | 固定值 `ucpro` |
| fr | string | 固定值 `pc` |
| uc_param_str | string | 空字符串 |

**请求体 (JSON):**
| 参数名 | 类型 | 说明 |
|--------|------|------|
| share_id | string | 分享ID |

**返回值:**
```json
{
    "data": {
        "share_url": "分享链接",
        "title": "分享标题",
        "passcode": "提取码（如果是私密分享）"
    }
}
```

---

## 通用请求头

所有 API 调用（除特殊说明外）使用以下请求头：

```
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36 Edg/130.0.0.0
Accept: application/json, text/plain, */*
Content-Type: application/json
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Origin: https://pan.quark.cn
Referer: https://pan.quark.cn/
Cookie: [用户Cookie]
```

---

## API 域名说明

| 域名 | 用途 |
|------|------|
| `drive-pc.quark.cn` | 主要 API 域名，用于文件操作 |
| `drive.quark.cn` | 分享文件保存 API |
| `pan.quark.cn` | 用户账户信息 API |

---

## 通用参数说明

| 参数名 | 说明 |
|--------|------|
| pr | 产品标识，固定为 `ucpro` |
| fr | 来源标识，固定为 `pc` |
| uc_param_str | UC参数字符串，通常为空 |
| __dt | 随机数，用于防缓存 |
| __t | 13位时间戳 |

---

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 23008 | 文件夹同名冲突 |
| 32003 | 网盘容量不足 (capacity limit) |
| 41013 | 文件夹不存在 |

---

## 业务流程图

### 流程概览

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          QuarkPanTool 核心业务流程                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────────┐                                                           │
│  │   用户登录    │ ──────► get_user_info (API 4)                            │
│  └──────────────┘         验证Cookie有效性，获取用户昵称                       │
│                                                                             │
│  ════════════════════════════════════════════════════════════════════════   │
│                                                                             │
│  【功能1: 批量转存】     【功能2: 批量分享】      【功能3: 批量下载】            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 流程1: 批量转存分享文件

将他人分享的文件保存到自己的网盘。

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              批量转存流程                                    │
└─────────────────────────────────────────────────────────────────────────────┘

     分享链接 URL
          │
          ▼
   ┌──────────────┐
   │  解析 pwd_id  │  从分享链接中提取 pwd_id 和提取码
   │  和 passcode  │
   └──────┬───────┘
          │
          ▼
   ┌──────────────┐     POST /share/sharepage/token
   │ get_stoken   │ ──► 获取分享页面访问令牌 stoken
   │   (API 1)    │
   └──────┬───────┘
          │ stoken
          ▼
   ┌──────────────┐     GET /share/sharepage/detail
   │ get_detail   │ ──► 获取分享文件列表
   │   (API 2)    │     返回: fid, file_name, share_fid_token
   └──────┬───────┘
          │
          │ 如果是目录，递归获取子文件
          ▼
   ┌──────────────┐     POST /file
   │ create_dir   │ ──► 在目标位置创建对应的文件夹结构
   │   (API 5)    │     返回: 新文件夹的 fid
   └──────┬───────┘
          │
          ▼
   ┌──────────────────┐   POST /share/sharepage/save
   │get_share_save    │ ─► 提交转存任务
   │task_id (API 6)   │   参数: fid_list, fid_token_list, to_pdir_fid
   └──────┬───────────┘   返回: task_id
          │
          ▼
   ┌──────────────┐     GET /task
   │ submit_task  │ ──► 轮询查询任务状态
   │   (API 8)    │     status=2 表示完成
   └──────┬───────┘
          │
          ▼
      转存完成
```

**关键数据流:**
```
pwd_id + passcode ──► stoken ──► file_list(fid, share_fid_token)
                                        │
                                        ▼
                      to_pdir_fid + fid_list + fid_token_list ──► task_id ──► 完成
```

---

### 流程2: 批量生成分享链接

将自己网盘中的文件生成分享链接。

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            批量分享流程                                      │
└─────────────────────────────────────────────────────────────────────────────┘

     目标文件夹路径
          │
          ▼
   ┌────────────────────┐   GET /file/sort
   │get_sorted_file_list│ ─► 获取文件夹内的文件列表
   │      (API 3)       │    返回: fid, file_name
   └──────┬─────────────┘
          │
          │ 递归遍历子文件夹
          ▼
   ┌──────────────────┐     POST /share
   │get_share_task_id │ ──► 创建分享任务
   │     (API 9)      │     参数: fid_list, title, url_type, expired_type
   └──────┬───────────┘     返回: task_id
          │
          ▼
   ┌──────────────┐         GET /task
   │ get_share_id │ ──────► 查询分享任务获取 share_id
   │   (API 10)   │
   └──────┬───────┘
          │
          ▼
   ┌──────────────┐         POST /share/password
   │ submit_share │ ──────► 获取最终分享链接
   │   (API 11)   │         返回: share_url, passcode
   └──────┬───────┘
          │
          ▼
      分享链接生成完成
```

**关键数据流:**
```
文件夹路径 ──► file_list(fid) ──► task_id ──► share_id ──► share_url + passcode
```

---

### 流程3: 批量下载文件

从网盘下载文件，绕过网页端大小限制。

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            批量下载流程                                      │
└─────────────────────────────────────────────────────────────────────────────┘

   ┌─────────────────────────────────────────────────────────────┐
   │                      两种入口方式                            │
   ├────────────────────────┬────────────────────────────────────┤
   │    A. 从分享链接下载     │       B. 从自己网盘下载              │
   │                        │                                    │
   │   分享链接 URL          │      目标文件夹路径                  │
   │        │               │           │                        │
   │        ▼               │           ▼                        │
   │  get_stoken (API 1)    │   get_sorted_file_list (API 3)    │
   │        │               │           │                        │
   │        ▼               │           │                        │
   │  get_detail (API 2)    │           │                        │
   │        │               │           │                        │
   │        ▼               │           ▼                        │
   │  ┌─────────────────────┴───────────┐                        │
   │  │      save → 自己网盘 (API 6,8)   │  ◄── 必须先转存         │
   │  └─────────────────────────────────┘                        │
   └────────────────────────┴────────────────────────────────────┘
                            │
                            ▼
                    获得文件 fid 列表
                            │
                            ▼
                ┌───────────────────────┐   POST /file/download
                │ quark_file_download   │ ─► 获取下载地址
                │       (API 7)         │   特殊UA: quark-cloud-drive客户端
                └───────────┬───────────┘   返回: download_url
                            │
                            ▼
                ┌───────────────────────┐
                │   HTTP 下载文件        │   使用返回的 download_url
                │   (普通HTTP请求)       │   需要携带正确的 Referer 和 Cookie
                └───────────┬───────────┘
                            │
                            ▼
                       下载完成
```

**关键点:**
- API 7 使用特殊的 User-Agent 模拟夸克云盘客户端
- 这样可以绕过网页端的文件大小下载限制
- 下载地址有时效性，需要及时下载

---

### API 调用关系总图

```
                              ┌─────────────────┐
                              │  get_user_info  │
                              │     (API 4)     │
                              │   验证登录状态    │
                              └────────┬────────┘
                                       │
           ┌───────────────────────────┼───────────────────────────┐
           │                           │                           │
           ▼                           ▼                           ▼
   ┌───────────────┐           ┌───────────────┐           ┌───────────────┐
   │   批量转存     │           │   批量分享     │           │   批量下载     │
   └───────┬───────┘           └───────┬───────┘           └───────┬───────┘
           │                           │                           │
           ▼                           ▼                           │
   ┌───────────────┐           ┌────────────────────┐              │
   │ get_stoken    │           │get_sorted_file_list│              │
   │   (API 1)     │           │      (API 3)       │              │
   └───────┬───────┘           └────────┬───────────┘              │
           │                            │                          │
           ▼                            │                          │
   ┌───────────────┐                    │                          │
   │ get_detail    │                    │                          │
   │   (API 2)     │                    │                          │
   └───────┬───────┘                    │                          │
           │                            │                          │
           ▼                            │                          │
   ┌───────────────┐                    │                          │
   │ create_dir    │                    │                          │
   │   (API 5)     │                    │                          │
   └───────┬───────┘                    │                          │
           │                            │                          │
           ▼                            ▼                          │
   ┌────────────────────┐       ┌───────────────────┐              │
   │get_share_save      │       │get_share_task_id  │              │
   │  task_id (API 6)   │       │     (API 9)       │              │
   └────────┬───────────┘       └────────┬──────────┘              │
            │                            │                         │
            ▼                            ▼                         │
   ┌───────────────┐            ┌───────────────┐                  │
   │ submit_task   │            │ get_share_id  │                  │
   │   (API 8)     │            │   (API 10)    │                  │
   └───────┬───────┘            └───────┬───────┘                  │
           │                            │                          │
           │                            ▼                          │
           │                    ┌───────────────┐                  │
           │                    │ submit_share  │                  │
           │                    │   (API 11)    │                  │
           │                    └───────┬───────┘                  │
           │                            │                          │
           ▼                            ▼                          ▼
   ┌───────────────────────────────────────────────────────────────────┐
   │                     文件已在自己网盘中                              │
   └───────────────────────────────────┬───────────────────────────────┘
                                       │
                                       ▼
                           ┌───────────────────────┐
                           │ quark_file_download   │
                           │       (API 7)         │
                           │   获取下载地址          │
                           └───────────────────────┘
```

---

### 异步任务处理模式

转存和分享操作都采用异步任务模式：

```
┌─────────────┐        ┌─────────────┐        ┌─────────────┐
│  提交任务    │  ───►  │  获取task_id │  ───►  │  轮询状态    │
│ (POST请求)  │        │   (响应)     │        │ (GET请求)   │
└─────────────┘        └─────────────┘        └──────┬──────┘
                                                     │
                                              ┌──────┴──────┐
                                              │  status=2?  │
                                              └──────┬──────┘
                                                     │
                                      ┌──────────────┼──────────────┐
                                      │ Yes          │              │ No
                                      ▼              │              ▼
                              ┌───────────┐         │      ┌───────────────┐
                              │  任务完成  │         │      │ 等待后重试     │
                              └───────────┘         │      │ (retry_index++) │
                                                    │      └───────┬───────┘
                                                    │              │
                                                    └──────────────┘
```

**任务状态码:**
- `status = 2`: 任务完成
- 其他状态: 任务进行中，需要继续轮询
