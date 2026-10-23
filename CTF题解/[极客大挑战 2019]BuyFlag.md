# [极客大挑战 2019]BuyFlag

## 题目描述
- **提示**：无
- **考点**：PHP弱类型比较、HTTP POST传参、业务逻辑漏洞

## 解题思路
1. **源码审计**：查看源代码（F12），在HTML注释中发现PHP代码逻辑
```bash
<!--
	~~~post money and password~~~
if (isset($_POST['password'])) {
	$password = $_POST['password'];
	if (is_numeric($password)) {
		echo "password can't be number</br>";
	}elseif ($password == 404) {
		echo "Password Right!</br>";
	}
}
-->
```
2. **弱类型绕过**：利用PHP特性，传入以数字开头但包含非数字字符的字符串（如`404a`），既不是纯数字（绕过`is_numeric`），在弱类型比较时又会转换为`404a`（满足`== 404`）。
3. **业务逻辑突破**：通过 `password=404a` 成功进入下一层，页面提示 "Pay for the flag!!! hacker!!!"，说明还需要传入 `money` 参数。
4. **金额绕过**：根据提示 `post money and password`，在 POST Body 中继续追加 `money` 参数。通过传入超大数值（可能会因为数字长度太长而失败，我们可以将其转换为16进制）触发后端支付逻辑的漏洞（如溢出、类型错误或逻辑反转），最终骗取 FLAG。

## 💡 核心技巧沉淀（最重要）
- **PHP 弱类型（Type Juggling）**：`is_numeric()` 判断和 `==` 比较结合时，极易产生漏洞。数字开头的字符串（如 `404a`、`1e10`）经常被用来绕过此类限制。
- **业务逻辑漏洞（逻辑漏洞）**：涉及金额、支付、积分等参数时，除了尝试 SQL 注入，多尝试传递**超大值**（溢出）、**负数值**（反向增加）、**数组**（`money[]=1` 引发报错）或**科学计数法**（`1e9`）。
- **HTTP 请求方法**：看到 `$_POST` 或 `$_GET` 限制时，一定要确保你的请求方法是正确的（用 Burp Suite 或 HackBar 修改）。

## 🛠️ 常用命令 / Payload
```bash
password=404a&money=1e10
```

<img width="1884" height="752" alt="image" src="https://github.com/user-attachments/assets/eac85999-7602-4b72-80b5-cad1ee4029af" />
