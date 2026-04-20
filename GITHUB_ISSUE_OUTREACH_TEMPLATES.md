# GitHub issue 外联留言模板库

用途：
这是一套标准三段式 GitHub issue 外联模板，用来把“公开 helpful comment”变成“继续沟通 / 收配置 / 引导到邮箱”的最小转化流程。

原则：
- 第一条先帮对方判断，不要一上来推销
- 第二条把问题收敛到最小必要信息
- 第三条只在对方表现出配合意愿时，再引导到邮箱

---

## 模板结构

### 模板 A：首评版（公开评论）
适用：
- 对方刚发 issue
- 你判断这是部署 / 回调 / 代理 / 容器类问题
- 你想先展示专业度

通用模板：
```text
This looks less like a generic app bug and more like a deployment/integration mismatch.

From the symptoms, I would narrow it down to a few likely buckets first:
1. base/public URL mismatch
2. reverse proxy / forwarded headers
3. callback / redirect URI generation
4. container/runtime config not matching the expected external URL

At this point I wouldn’t keep changing values blindly — this kind of issue is usually caused by one exact mismatch in config or request path generation.

If you want, paste the relevant compose/proxy/config snippet (redacted if needed) and I can help narrow down the minimal fix path.
```

适合更强势一点的版本：
```text
This doesn’t smell like “random breakage” — it looks more like one exact mismatch between the public URL, proxy behavior, callback generation, or container-side runtime config.

If you share the minimal relevant config (redacted), I can help reduce it much faster than trial-and-error.
```

---

### 模板 B：对方回复后追问版
适用：
- 对方愿意继续
- 你需要把范围缩小
- 你不想在 issue 里做无效拉扯

通用模板：
```text
Sure — to narrow it down efficiently, the most useful next inputs would be:

1. the relevant compose / deployment section
2. the public/base URL related settings
3. the exact failing request / redirect / callback URL
4. the first visible error from logs or browser network trace

With those, it’s usually possible to tell whether this is env, proxy headers, callback generation, routing, or runtime config.
```

如果问题偏 OAuth / OIDC：
```text
To narrow this down quickly, the key things to compare are:
- expected public URL
- generated callback / redirect URI
- proxy headers / scheme handling
- the first failing request in the browser or server logs

Once those line up, the root cause is usually much easier to isolate.
```

如果问题偏容器 / 反向代理：
```text
The fastest way to reduce this is to compare:
- what URL the app thinks it is serving
- what URL the proxy is exposing publicly
- what the container is actually using at runtime
- where the first wrong request is generated
```

---

### 模板 C：引导到邮箱版
适用：
- 对方已经连续回复
- 对方愿意贴更多配置
- GitHub 评论区开始太长或不方便贴信息

通用模板：
```text
If GitHub comments get too noisy, feel free to send the redacted config/details to 1781946773@qq.com and I can help review it there.
```

更自然一点的版本：
```text
If it’s easier, you can send the redacted config/details to my email: 1781946773@qq.com
I do this kind of deployment / webhook / OAuth debugging work regularly.
```

更偏成交导向的版本：
```text
If you want to move faster, send the redacted details to 1781946773@qq.com and I can help turn this into a smaller diagnosis / fix path instead of continuing broad trial-and-error here.
```

---

## 常见场景专项模板

### 1) Webhook / 回调失败
```text
This looks more like a callback path / public URL / proxy mismatch than a generic webhook failure.

The first things I would compare are:
- the exact public callback URL you expect
- the callback URL the app actually generates or receives
- whether reverse proxy headers preserve the original scheme/host
- where the first failing request or timeout happens

If you share the relevant config (redacted), I can help narrow down the minimal fix path.
```

### 2) OAuth / OIDC / redirect URI 错误
```text
The symptom here is often caused by callback/base URL generation behind the proxy rather than the OAuth provider itself.

I would compare:
- public URL
- generated redirect URI
- trusted proxy / forwarded headers
- app external URL settings

If you paste the minimal relevant config, I can help isolate which one is out of sync.
```

### 3) Docker Compose 服务起不来 / 线上异常
```text
This looks like one of those cases where the app can run, but the deployment/runtime assumptions don’t match the actual server setup.

The fastest way to narrow it down is usually:
- effective env inside the running container
- compose service definition
- reverse proxy / exposed port mapping
- the first failing request or startup error

If you share those parts (redacted), I can help reduce it to the most likely root cause path.
```

### 4) 反向代理 / Nginx / Traefik / Cloudflare 问题
```text
At first glance this looks more like a proxy/header/scheme issue than an app logic problem.

I’d verify:
- what the public endpoint is supposed to be
- whether the app trusts forwarded headers
- whether the proxy preserves the original scheme/host/path
- whether the generated absolute URL matches the public entrypoint

That usually exposes the exact mismatch pretty quickly.
```

---

## 执行规则
- 首评不要太长，重点是“像懂行的人在帮忙判断”
- 追问时只要最关键的 3~4 项信息
- 如果对方不贴配置，不要免费长聊太久
- 如果对方开始明显依赖你，就顺势引导到邮箱
- 你的目标不是在 issue 里做完全部支持，而是展示你能快速缩小范围和给出修复路径

## 一条通用补刀话术
```text
At this point I wouldn’t keep changing values blindly — this usually comes down to one exact mismatch in base URL, forwarded headers, callback generation, or container-side trust/routing. If you paste the minimal relevant config (redacted), I can help narrow it down much faster.
```
