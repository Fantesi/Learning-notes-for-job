- Selenium库中有`common和webdriver`两个模块，common模块中主要是Selenium定义的异常类，webdriver模块中存放了所有操作浏览器的函数。
- **webdriver的实例化**：先导入webdriver模块 `from selenium import webdriver`, 再进行实例化`driver = webdriver.Chrome()`。

#### 元素定位方式

- 需要对对象进行定位时，需要`from selenium.webdriver.common.by import By`,只要需要元素定位，By类必不可少。例如：`driver.find_element(By.id,"kw").click()` 表示先根据id属性值“kw”定位，再执行点击操作。除了**ID定位**其它定位方式有：

- 根据`NAME`属性定位 	 `searchTextBox = driver.find_element(By.name,'wd')`

- 根据`CLASS`属性定位      `searchTextBox = driver.find_element(By.class,'s_ipt')`

  - **注**：一般情况下尽量根据`ID`或`NAME`属性定位，因为这两个属性通常用作元素的唯一标识。而`CLASS`可能会被多个元素引用。

- 根据`链接文本`定位      `link = driver.find_element(By.link_text,'贴吧')`

  - 还可以**模糊定位**      `link = driver.find_element(By.partial_link_text,'贴')`

- 根据`HTML标签`定位      **注**：标签无法唯一确认某个元素，注意使用场景 `find_element(By.tag_name,'input')`

- 根据`Xpath`定位      `find_element(By.Xpath,'Xpath表达式')`

  - Xpath是一种综合性的定位方式，不仅支持前六种定位方式，而且还能通过**Xpath表达式**进行更加丰富的高级查找。

  - **Xpath全称为XML Path Language,XML路径语言**，是一种用来**确定目标元素在XML文档中的位置的语言**。它通过**Xpath表达式选取XML节点（类似文件路径）**，从而确定目标元素的位置。

  - **基于绝对路径定位**

    - 以`/`开头，从根元素层层往下查找目标元素。如`/html/body/div/div/form/span/input`

  - **基于相对路径定位**

    - 以`//`开头，如`//span/input`，只需关注span元素和input元素的相对位置，用`//`的方式略过前面所有层级。

  - **基于索引或属性定位**

    - 一般结合相对路径定位
    - 索引定位使用`[]`结尾，填入索引。如`//span/input[1]`或`//span/input[last()]`
    - 属性定位在结尾`[]`中使用`@`前缀来指定属性名称。如`//input[@id='kw']`

  - **基于轴定位（Axis定位）**

    - **相对当前元素找其它元素**

      | **轴名称**             | **轴名称及其解释**                     | **示例表达式**                                 |
      | ---------------------- | -------------------------------------- | ---------------------------------------------- |
      | **ancestor**           | 选取当前节点的所有祖先（父、祖父等）   | `//div[@id='child']/ancestor::div`             |
      | **child**              | 选取当前节点的直接子节点               | `//div[@id='parent']/child::p`                 |
      | **descendant**         | 选取当前节点的所有后代节点（子、孙等） | `//div[@id='container']/descendant::a`         |
      | **following-sibling**  | 选取当前节点之后的所有兄弟节点         | `//p[@id='first']/following-sibling::p`        |
      | **preceding-sibling**  | 选取当前节点之前的所有兄弟节点         | `//p[@id='third']/preceding-sibling::p`        |
      | **parent**             | 选取当前节点的父节点                   | `//p[@id='child']/parent::div`                 |
      | **following**          | 选取当前节点之后的所有节点             | `//p[@id='first']/following::p`                |
      | **preceding**          | 选取当前节点之前的所有节点             | `//p[@id='third']/preceding::p`                |
      | **self**               | 选取当前节点自身                       | `//p[@id='target']/self::p`                    |
      | **ancestor-or-self**   | 选取当前节点和所有祖先                 | `//p[@id='child']/ancestor-or-self::*`         |
      | **descendant-or-self** | 选取当前节点和它的所有后代             | `//div[@id='container']/descendant-or-self::*` |

  - **基于函数或表达式定位**

    - ```python
      # 查找文本内容完全等于 "hao123" 的 <a> 元素
      driver.find_element_by_xpath("//a[text()='hao123']")
      
      # 查找 <a> 标签，其 href 属性包含 "www.hao123.com" 的元素
      driver.find_element_by_xpath("//a[contains(@href, 'www.hao123.com')]")
      
      # 查找 <a> 标签，其文本内容包含 "ao12" 的元素
      driver.find_element_by_xpath("//a[contains(text(), 'ao12')]")
      
      # 查找 <a> 标签，其 href 属性的值以 "https://www.hao" 开头的元素
      driver.find_element_by_xpath("//a[starts-with(@href, 'https://www.hao')]")
      
      # 查找 <a> 标签，name 属性等于 "errorname" 或者 文本内容等于 "hao123" 的元素
      driver.find_element_by_xpath("//a[@name='errorname' or text()='hao123']")
      
      # 查找 <a> 标签，href 属性包含 "hao123" 且文本内容等于 "hao123" 的元素
      driver.find_element_by_xpath("//a[contains(@href, 'hao123') and text()='hao123']")
      
      ```

    - **提示：在浏览器中按F12，Console中输入$x(“Xpath表达式”),可以方便地测试Xpath表达式能否找到元素**