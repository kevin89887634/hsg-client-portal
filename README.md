# 恒盛集团客户门户 (hsg-client-portal)

把 117 份 inforce 客户的"全息方案" PDF 通过 GitHub Pages 私链接发给客户。

## 链接格式

每个客户一个唯一 token (sha256(salt+policy_id)[:12]):

```
https://kevin89887634.github.io/hsg-client-portal/?token=<12-char-hex>
```

## 找客户的 token

```bash
python3 -c "
import json
with open('manifest.json') as f: d = json.load(f)
for c in d['clients']:
    if 'WANG' in c['owner_name'].upper() or '王' in c['owner_name']:
        print(c['policy_id'], c['owner_name'], 'token=' + c['token'])
"
```

## 安全说明

- Token = `sha256("hsg-client-portal-v1-2026-05" + policy_id)[:12]`
- 12-char hex = 2^48 组合,不可暴力枚举
- 撤销机制: 改 SALT 重新跑 build,所有旧 token 全部失效

## 架构

```
GitHub Pages 静态 (此 repo)
  ├── index.html           — 入口,读 ?token=&fetch manifest.json
  ├── manifest.json        — {token: pdf_filename} 映射
  └── pdfs/<token>.pdf     — 各客户 PDF (文件名 = token, 不暴露 policy_id)
```

- 零后端,零运维,GitHub Pages 自动 HTTPS + CDN
- 60 MB 总,GitHub Pages 1GB 限内
