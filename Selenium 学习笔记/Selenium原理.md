#### Selenium原理

1. ##### Selenium RC的实现原理

   - 如图所示<img src="C:\Users\hp\Pictures\测试工程师\Selenium1.0原理.jpg" alt="IMAGE 1" style="zoom: 50%;" />

   - Selenium1.0的**自动化测试执行步骤**如下

     1. 测试人员基于Selenium支持的编程语言写好测试脚本程序
     2. 测试人员执行测试程序
     3. 测试脚本程序发送访问网站的HTTP请求给RC Server
     4. RC Server收到请求后，访问被测试网站并获取网页数据内容，并在网页中插入Selenium Core的JavaScript代码库，然后返回给执行测试的浏览器
     5. 测试脚本在浏览器内部再调用Selenium Core 来执行测试代码逻辑，最后记录测试的结果，完成测试。

   - Javasript的**同源策略**

     - 浏览器获取到网页内容后，会执行此域名下的JavaScript语句和文件。如果外部引用的JavaSript文件URL与当前网页的域名不一致，那么浏览器会拒绝执行外部JavaScript文件的代码，以防止恶意的JavaScript文件攻击用户浏览器，起到安全防护作用。总而言之，网页内的**外部JavaScript文件URL必须要和网页域名同源**。
     - 而Selenium的JavaScript代码库的URL就与被测试网站的域名不一致，怎么解决的呢？如下

   - Selenium1.0**通过HTTP代理的方式解决同源问题**。

     - 如图所示<img src="C:\Users\hp\Pictures\测试工程师\Selenium proxy原理.jpg" alt="IMAGE 2" style="zoom:50%;" />
     - 代理模式的实现机制具体如下：
       1. 执行测试脚本，脚本向Selenium Server发起请求，要求和Selenium Server建立连接。
       2. Selenium Server的Launcher启动浏览器，向浏览器插入Selenium Core的JavaScript代码库，并把浏览器的代理设置为Selenium Server的HTTP Proxy。
       3. 测试脚本向Selenium Server发送HTTP请求，Selenium Server对请求进行解析，然后通过HTTP Proxy发送JS指令通知Selenium Core操作浏览器。
       4. Selenium Core接收到指令后，按测试脚本逻辑执行网页操作命令。
       5. Selenium Core在操作浏览器时，可能会引发新的页面请求，浏览器收到Selenium Core发来的新的页面请求信息后，发送HTTP请求给Selenium Server的HTTP Proxy（实现了**拦截**），请求获取新的Web页面。
       6. Selenium Server接收到浏览器发送的HTTP请求后，重组HTTP请求，获取对应的Web页面，再通过Proxy把接收到的Web页面返回给浏览器。（**实现了修改**）。

     - 区分：**Selenium Core 本质上是一个 JavaScript 代码库，它本身不包含具体的测试脚本，而是一个执行引擎**。**测试脚本**不会直接插入到浏览器，而是**通过 Selenium Server 控制 Selenium Core 运行测试逻辑**。**Selenium Server拦截修改**来自浏览器的请求。

   - ##### Selenium2.0 webdriver的实现原理

     - **Selenium 2.0（WebDriver）完全不同，它使用浏览器的** **原生 API** **控制浏览器，不再依赖 JavaScript 注入。**

       ### **Selenium 2.0 工作流程**

       1. **测试脚本** 直接与 **浏览器驱动（WebDriver）** 交互，例如 ChromeDriver、GeckoDriver。
       2. **WebDriver 直接调用浏览器原生 API**，控制浏览器执行各种操作（点击、输入等）。
       3. **浏览器直接执行操作**，返回执行结果给 WebDriver。
       4. **WebDriver 直接反馈执行结果给测试脚本**。

       ### **为什么 Selenium 2.0 不需要绕过同源策略？**

       - **WebDriver 不是通过 JavaScript 运行，不需要RC Server参与，而是直接调用浏览器 API**，所以它的操作不受 JavaScript 的同源策略限制。

       - WebDriver 能够直接访问浏览器内部功能

         ，如：

         - 直接控制浏览器 UI（模拟鼠标、键盘输入）。
         - 访问浏览器的 DevTools API（如修改 Cookie、存储）。
         - 直接与操作系统交互（如文件上传、下载）。

     - 当你运行一个 Selenium 测试脚本时，执行流程如下：

       ```
       [ 测试脚本 ] → [ Selenium WebDriver ] → [ 浏览器驱动 ] → [ 浏览器 ]
       ```

       | **步骤**                                          | **操作**                                      | **涉及组件**       |
       | ------------------------------------------------- | --------------------------------------------- | ------------------ |
       | **1. 测试脚本调用 WebDriver API**                 | `driver.get("https://example.com")`           | 测试脚本           |
       | **2. WebDriver 生成 HTTP 请求**                   | `POST /session`，创建新会话                   | Selenium WebDriver |
       | **3. WebDriver 通过 JSON Wire Protocol 发送请求** | `POST /session/{id}/url`                      | 浏览器驱动         |
       | **4. 浏览器驱动调用浏览器原生 API**               | 通过 Chrome DevTools 或 Marionette 控制浏览器 | 浏览器驱动         |
       | **5. 浏览器执行指令**                             | 加载网页、执行 UI 操作                        | 浏览器             |
       | **6. 浏览器返回执行结果**                         | 通过浏览器驱动返回 JSON 响应                  | 浏览器             |
       | **7. WebDriver 解析响应并返回给测试脚本**         | `{"status": 0, "value": "success"}`           | Selenium WebDriver |
       | **8. 测试脚本继续执行下一步**                     | 例如 `driver.find_element().click()`          | 测试脚本           |

   - ##### Selenium RC与Selenium WebDriver对比

     | **对比项**           | **Selenium 1.0 (RC)**                                        | **Selenium 2.0 (WebDriver)**                        |
     | -------------------- | ------------------------------------------------------------ | --------------------------------------------------- |
     | **底层原理**         | **基于 JavaScript（Selenium Core）**                         | **直接调用浏览器原生 API**                          |
     | **执行方式**         | 通过 **Selenium Server** 代理和 JavaScript 在浏览器中执行命令 | 直接使用浏览器驱动（WebDriver），不需要代理         |
     | **同源策略绕过**     | **使用代理和 URL 重写**（伪装所有请求为 `localhost:4444`）   | **直接控制浏览器，不受 JavaScript 同源策略限制**    |
     | **启动浏览器的方式** | **Selenium Server 启动浏览器，并注入 Selenium Core**         | **浏览器驱动（ChromeDriver、GeckoDriver）原生启动** |
     | **执行效率**         | **较慢**（所有请求都需经过 Selenium Server 代理）            | **更快**（直接控制浏览器，无需代理）                |
     | **稳定性**           | 依赖 JavaScript 注入，兼容性较差                             | 直接调用原生 API，更稳定                            |
     | **支持的功能**       | 只能测试 Web 页面                                            | 除 Web 页面外，还可处理弹窗、文件上传、截图等       |
     | **是否仍然使用**     | **已被淘汰（Selenium 3+ 废弃）**                             | **主流方式（Selenium 3、Selenium 4）**              |

   
