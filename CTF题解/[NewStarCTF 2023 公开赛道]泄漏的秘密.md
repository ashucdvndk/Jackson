# [NewStarCTF 2023] 泄漏的秘密

## 题目描述
- **提示**：粗心的网站管理员总会泄漏一些敏感信息在 Web 根目录下。
- **考点**：信息收集 / 目录扫描 / 源码泄露

## 解题思路（我的思考过程）
1. **信息收集**：看到“泄漏”和“根目录”，第一反应是找备份文件。
2. **目录扫描**：使用 `dirsearch` 对靶机进行扫描，发现存在 `/www.zip` 和 `/robots.txt`。
3. **源码下载**：访问下载 `www.zip`，解压后发现是网站源码。
4. **审计源码**：全局搜索 `flag{`，在某个 `php` 文件（或者注释里）找到第二段 Flag。
5. **检查 robots**：访问 `robots.txt`，得到第一段 Flag。

## 核心技巧沉淀（最重要）
- **扫描字典**：实战中遇到备份泄露，重点扫 `www.zip`, `web.zip`, `backup.zip`, `www.tar.gz`, `.git` 等。
- **Robots 规则**：永远不要忘记检查 `http://ip/robots.txt`，里面经常藏着出题人/管理员不想让爬虫看到，但恰好是你想要的路径。

## 常用命令/Payload
```bash
# 扫描命令示例
dirsearch -u 靶机地址 -e php,zip,txt,bak,swp
```

## 目录扫描结果
<img width="1162" height="370" alt="image" src="https://github.com/user-attachments/assets/4fb5c644-30a7-423d-af7d-e7b330e3cab8" />

**结果分析**：
- 成功扫出 `www.zip`，说明网站源码备份暴露。
- 成功扫出 `robots.txt`，里面有隐藏路径。

**获取 Flag**：
- Flag 1：访问 `/www.zip` 下载源码，全局搜索 `flag` 拿到。
- Flag 2：访问 `/robots.txt` 找到隐藏页面，访问拿到。
