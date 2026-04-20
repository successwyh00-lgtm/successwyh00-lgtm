# GitHub outreach leads — 2026-04-20

用途：
这份文件不是单纯的线索记录，而是可直接拿来执行的外联话术包。
推荐动作顺序：
1. 先发公开 issue 评论，保持“先帮忙判断问题”的姿态
2. 对方一旦回复或愿意贴配置，再继续追问最小必要信息
3. 如果对方明显卡住且愿意配合，再引导到邮箱进一步沟通

---

## 1) elie222/inbox-zero#2218
- URL: https://github.com/elie222/inbox-zero/issues/2218
- Title: Failed to start Google sign-in Failed to fetch
- 优先级：最高
- 判断：高匹配、高痛点、高转化潜力

### 为什么值得优先跟进
- 用户是 Debian VM + Docker + Cloudflared + 自定义域名，自托管部署链路复杂。
- 问题不是普通使用问题，而是典型部署 / 反向代理 / OAuth 回调错配。
- 对方已经改过 `.env`、external URL、Google redirect URI，仍未解决，说明已进入“自己折腾不动”的阶段。
- 维护者给的是泛化建议，没有真正接住排障，这里很适合你切入。

### 你的切入角度
- 把问题定义成：public URL / internal URL / reverse proxy / callback generation 四者不一致。
- 强调你能帮助对方快速缩小范围，不需要他再盲试。
- 不要一上来推销，先给具体判断路径。

### 可直接发的公开评论
```text
This looks more like a deployment/base-URL + callback path mismatch than a pure Google OAuth problem.

From your description, the main red flag is that the browser is still trying to call:
`http://192.168.1.27:3000/api/auth/sign-in/social`
from your public origin:
`https://inbox-zero.digitik.co`

That usually means at least one of these is still leaking an internal address:
1. app base URL / public URL env
2. reverse proxy forwarded headers
3. OAuth callback/base URL generation
4. cached build/runtime env inside the container

If you want, I can help you narrow it down step by step. In problems like this I usually check:
- effective env inside the running container
- generated callback/base URL
- Cloudflared / reverse proxy headers
- browser network trace for the first wrong absolute URL

If you share your docker compose + the relevant env keys (redacted), I can help isolate the minimal fix path.
```

### 如果对方回复“可以，看一下”
```text
Sure — if you paste these 4 things (redacted if needed), I can help you narrow it down faster:

1. your docker compose service for Inbox Zero
2. the public/base URL related env vars
3. the Google OAuth redirect URI you configured
4. the first failed browser request URL + response/error

With those, it’s usually possible to tell whether the wrong URL comes from env, proxy headers, or cached runtime config.
```

### 如果对方迟迟不贴配置，你的轻推进行话术
```text
No pressure — even just the compose snippet + the public URL related env keys is usually enough to tell whether this is a base-URL leak or a proxy/header issue.
```

### 如果对方明显愿意继续沟通，可引导到站外
```text
If it’s easier, you can also send the redacted config/details to my email: 1781946773@qq.com
I do this kind of deployment / webhook / OAuth debugging work regularly.
```

---

## 2) ChrispyBacon-dev/DockFlare#307
- URL: https://github.com/ChrispyBacon-dev/DockFlare/issues/307
- Title: OAuth Redirect URI for general OAuth wrong
- 优先级：高
- 判断：典型 OIDC / redirect URI / 代理协议识别问题

### 为什么值得跟进
- 是真实集成问题，不是泛泛提问。
- owner 已经要求 docker labels，说明线程仍在配置排障阶段。
- `http` / `https` scheme 错误，非常符合你的强项。

### 你的切入角度
- 强调：这类问题经常不是 OAuth 提供商本身错，而是应用在代理后面识别 scheme 出错。
- 让对方先贴 labels / compose，而不是继续猜。

### 可直接发的公开评论
```text
The symptom here is very often caused by scheme detection behind the proxy rather than the OAuth provider itself.

Because the generated authorize URL contains:
`redirect_uri=http://cf.domain.nl/auth/samarium/callback`
while the real public endpoint should be HTTPS, I would check these first:
- whether the app trusts `X-Forwarded-Proto=https`
- whether Traefik / proxy labels are forwarding the original scheme correctly
- whether DockFlare has an explicit external/base URL setting that still points to HTTP
- whether Authelia redirect URI and the app-generated callback URI are using the exact same scheme/host/path

If you want, paste the relevant docker labels / compose section (redacted) and I can help narrow it down to the exact mismatch.
```

### 对方一旦愿意继续配合，可追问
```text
If you share the labels / compose section plus the exact public URL you expect, I can usually tell pretty quickly whether this is:
1. proxy header handling
2. app external URL config
3. OAuth redirect mismatch
```

### 轻度转站外话术
```text
If GitHub comments get too noisy, feel free to send the redacted config to 1781946773@qq.com and I can help review it there.
```

---

## 3) fosrl/pangolin#1637
- URL: https://github.com/fosrl/pangolin/issues/1637
- Title: OCID fails with Authentik - how to troubleshoot?
- 优先级：中高
- 判断：高难度、高客单可能，但回复成本也更高

### 为什么值得跟进
- 是自托管 OIDC + Traefik + 自定义 CA + 容器信任链问题，复杂度高。
- 对方已经做过多轮尝试，说明痛感强。
- 这类问题一旦愿意让你介入，通常不是一句两句能解决，反而更有付费空间。

### 你的切入角度
- 不要承诺“我知道答案”，而是给出专业 triage 路径。
- 把焦点放在：容器到 IdP 的可达性、CA trust、issuer/token/callback 一致性。

### 可直接发的公开评论
```text
Given the error happens at the token request / callback stage, I would triage this as a container-to-IdP trust/connectivity problem before assuming the OIDC config itself is wrong.

From your notes, the first things I would verify are:
1. from inside the Pangolin container, can it resolve and reach the Authentik issuer URL exactly as configured?
2. does the container trust the CA that signs the Authentik cert?
3. are the issuer URL, authorize URL, token URL, and callback URL all using the same reachable hostname/path from the container’s point of view?
4. if you use internal DNS / split routing, is Pangolin reaching the IdP through the same route you expect?

The `Failed to send request` part often points to outbound trust/routing rather than user-authentication failure.

If you want, I can help reduce this to a small checklist against your Traefik labels / compose / issuer settings.
```

### 对方愿意继续时的追问模板
```text
If you want to narrow it down efficiently, the most useful next inputs would be:
- issuer URL
- callback URL
- Traefik labels / relevant compose section
- whether the Pangolin container trusts the Authentik CA
- result of reaching the issuer URL from inside the container
```

### 转站外话术
```text
If easier, send the redacted config/details to 1781946773@qq.com and I can help you turn it into a smaller troubleshooting checklist.
```

---

## 4) mickael-kerjean/filestash#950
- URL: https://github.com/mickael-kerjean/filestash/issues/950
- Title: [bug] Dropbox Sign-in process not working when launched from docker-compose
- 优先级：中
- 判断：问题真实，但更可能偏应用内部行为；转化率略低于前三个

### 为什么仍值得跟进
- 用户已经在 raw host / localhost / Traefik 都试过，说明不是最低级错误。
- OAuth provider 成功但回调后会话不成立，这类问题你仍然有切入点。
- 适合作为“公开展示你会排障”的辅助线索。

### 你的切入角度
- 不争论一定是谁的 bug。
- 帮对方先把问题拆成 provider redirect 成功 vs app-side handoff 失败。

### 可直接发的公开评论
```text
This looks like one of those cases where the OAuth provider succeeds but the app-side callback/session handoff breaks afterward.

Because you reproduced it across raw host, localhost, and Traefik, I’d separate the problem into two parts:
1. provider redirect succeeds
2. Filestash session establishment after redirect does not complete automatically

A practical debugging path would be:
- inspect the final redirect target and fragment/query values
- compare expected callback/session endpoint vs the one actually used by the Dropbox plugin
- confirm whether reverse proxy/base URL settings affect the post-login handoff
- trace the first failing request in the browser network tab

If useful, I can help turn this into a minimal reproducible diagnosis checklist instead of trial-and-error.
```

### 对方愿意继续时的追问模板
```text
If you can share the final redirect URL, the first failing network request, and the compose/reverse-proxy setup, that’s usually enough to tell whether the break happens in callback routing, session setup, or plugin behavior.
```

---

## 建议执行顺序
1. inbox-zero#2218
2. DockFlare#307
3. pangolin#1637
4. filestash#950

---

## 外联执行规则
- 先公开评论，避免一上来像硬推销。
- 第一条评论必须给“具体判断框架”，不能只说“我可以帮你”。
- 如果对方回复，就迅速把问题收敛到 3~4 个最关键配置点。
- 只有当对方已经表现出配合意愿，再引导去邮箱。
- 你的目标不是在 issue 里免费做完整支持，而是展示你能快速缩小问题范围。

## 你真正要卖的不是“会部署”
你卖的是：
- 快速判断是 env、proxy、callback、header、CA trust 还是 runtime cache 问题
- 少走弯路的最小修复路径
- 从“卡住”到“恢复可用”的落地能力

## 一条通用补刀话术
当对方已经反复试错但没贴全配置时，可用：

```text
At this point I wouldn’t keep changing values blindly — this usually comes down to one exact mismatch in base URL, forwarded headers, callback generation, or container-side trust/routing. If you paste the minimal relevant config (redacted), I can help narrow it down much faster.
```
