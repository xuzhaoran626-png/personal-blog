# Assignment 3: Deployment and Integration of AI Agents

## 一、在线Agent部署

### 1.1 工具选择

本次使用 **DeepSeek API** 作为底层模型，搭配 **Dify Cloud** 平台搭建Agent。选择DeepSeek是因为其API成本低，适合个人测试；选择Dify是因为其图形化界面，不需要编写代码即可配置工具调用能力。

### 1.2 配置步骤

**第1步：在Dify中配置DeepSeek模型供应商**

最初遇到API余额不足的报错（`Insufficient Balance`），充值3元后解决：

![配置DeepSeek供应商](images/1782407143029_image.png)

**第2步：在Agent应用中添加Tavily搜索工具**

![添加Tavily工具](images/1782407829403_image.png)

**第3步：完成Tavily工具授权**

授权前显示"工具未授权"，去Tavily官网申请免费API Key后完成授权：

![Tavily工具授权完成](images/1782408022904_image.png)

### 1.3 测试与问题

提问："2026年现在最新的人工智能大模型有哪些值得关注的发布？"

![测试结果](images/1782408451780_image.png)

**问题记录**：测试发现Agent显示"已深度思考"而非实际调用搜索工具，回答内容暴露出模型并未真正联网查证（模型自己提到"2026年可能是笔误"）。说明工具未被触发调用。

**原因分析**：可能是System Prompt未明确引导模型在何种情况下必须调用搜索工具，也可能是问题表述不够明确触发"搜索意图"。受限于时间，本次未进一步深入调试解决，但记录下这一发现作为后续优化方向。

---

## 二、本地模型部署

### 2.1 安装步骤

1. 从 ollama.com 下载并安装 Ollama
2. 终端运行 `ollama pull qwen2.5:7b` 拉取模型（约4.7GB）
3. 运行 `ollama run qwen2.5:7b` 进入交互对话

确认安装版本（0.30.10）并成功拉取模型：

![安装与拉取模型](images/1782406287155_image.png)

### 2.2 交互测试

测试问题：
- "请用Python写一个二分查找算法，并简要解释时间复杂度"

模型给出了正确的代码实现，并附带了时间复杂度的数学推导：

![终端对话结果](images/1782406404625_image.png)

### 2.3 小问题记录

退出对话时输入大写 `/BYE` 报错"Unknown command"，改用小写 `/bye` 后成功退出，说明命令大小写敏感。

---

## 三、IDE集成

### 3.1 安装Continue插件

在VSCode扩展市场安装 Continue 插件：

![Continue插件安装](images/1782408634464_image.png)

### 3.2 尝试连接DeepSeek在线API

打开Continue配置面板，准备添加Chat模型：

![Continue Models面板](images/1782408966742_image.png)

找到DeepSeek Provider配置入口：

![DeepSeek Provider配置](images/1782409338733_image.png)

连接后，顶部模型选择器异常显示为空：

![模型未正确加载](images/1782409469969_image.png)

进一步检查发现模型列表显示"No models configured"：

![No models configured](images/1782409612522_image.png)

重新配置后，实际调用时报错：

![Beta API报错](images/1782409835684_image.png)

尝试修改 `config.yaml`，手动指定 `provider: openai` 及 `apiBase`：

![config.yaml配置（API Key已遮挡）](images/1782409886856_image.png)

保存后重新测试，仍然报相同的beta API错误：

![仍然报错](images/1782410181728_image.png)

**问题分析**：怀疑Continue插件对DeepSeek新版接口的适配存在兼容性问题，多次调整 `apiBase` 路径与 `provider` 类型均未解决。

### 3.3 切换本地模型再次受阻

尝试切换到本地Ollama的 `qwen2.5` 模型，但因为本地实际安装的标签是 `qwen2.5:7b`，名称不完全匹配导致Continue报错"未安装"：

![本地模型未识别](images/1782410613915_image.png)

手动在config.yaml中补全完整模型名后，再次测试仍报"Failed to load config"错误：

![config加载失败](images/1782410951090_image.png)

### 3.4 最终方案：绕开插件，直接使用VSCode内置终端

由于Continue插件持续出现兼容性问题，且时间有限，改为直接在 **VSCode内置终端中调用本地Ollama模型**，同样达到"在IDE环境中使用AI辅助编程"的效果。

测试代码：

```python
def calc(numbers):
    result = []
    for i in range(len(numbers)):
        if numbers[i] % 2 == 0:
            result.append(numbers[i] * 2)
    total = 0
    for i in range(len(result)):
        total = total + result[i]
    return total
```

在VSCode终端中输入解释与重构请求，AI给出了清晰的逐步解释，并提供了使用生成器表达式 + `sum()` 函数的简化重构版本，功能保持一致但代码更简洁高效：

![VSCode终端AI解释与重构](images/1782411237692_image.png)

---

## 四、反思与对比

【请在此处用自己的话写2-3句真实感受，可参考以下方向：】

- 在线模型（DeepSeek通过Dify）响应速度的主观感受、搭建Agent工具调用链路是否符合预期
- 本地模型（Ollama）部署难度、是否离线可用、与在线模型在中文/逻辑能力上的差异感受
- IDE集成环节遇到的插件兼容性问题，以及最终绕开问题采用的解决方式，给你带来什么启发
- 如果时间充足，接下来还想继续解决或优化的方向

---

## 附：遇到的主要问题汇总

| 阶段 | 问题 | 处理方式 |
|------|------|----------|
| 在线Agent | DeepSeek API余额不足 | 充值3元解决 |
| 在线Agent | Agent未真正调用搜索工具 | 记录现象，作为后续优化方向 |
| IDE集成 | Continue连接DeepSeek报beta API错误 | 多次调整config.yaml未解决 |
| IDE集成 | Ollama模型版本号未匹配（qwen2.5 vs qwen2.5:7b） | 手动补全标签仍报config加载失败 |
| IDE集成 | Continue插件持续报错，时间有限 | 改用VSCode内置终端直接调用Ollama完成演示 |
