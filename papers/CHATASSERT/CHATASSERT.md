# Key

The key idea of CHATASSERT is not merely using an LLM, but continuously enhancing prompts with external tools and iterative feedback to repair and refine generated assertions.

# About 

This is a paper about test oracle generation

In the past, methods of test oracle generation is not perfect, but previous authors, researchers proposed *TECO* . in the previous studies, this is SOTA, but this accuracy rate is not perfect, it's a little bit low. So the author of this paper tries to validate the ability of ChatGPT to test oracle generation and the ChatGPT has potential with simple prompt engineering, but could not perform its best, so the authors proposed to CHATASSERT ,a static and dynamic information of test oracle generation to generate and test test oracles

# Figure

![[CHATASSERT-fig1.png|480]]


Part (a) provides the original test sequence and focal method.

It defines:

- the testing context
- the target behavior
- what the oracle should verify

Figure 1(a) shows the original test case.  
The goal of the test is to verify whether the client finally receives exactly one cookie.



Fig2中的fig a就是给出个test sequence的例子，且给出了placeholder，chatassert通过chatgpt的请求开始对话

对！你这个理解已经基本正确了，而且你已经抓到 Fig.2 的主干逻辑了。  
我帮你再“学术化精修”一下，让你变成组会上能说的版本。

---

# ✅ Fig.2(a) 本质是什么？

---

## 中文

Fig.2(a) 给出了一个 test sequence（测试前缀）的例子。

其中：

```
<AssertPlaceholder>
```

表示：

👉 assertion 被故意移除了。

CHATASSERT 的任务，就是：

👉 根据已有 test sequence 自动生成正确 assertion。

---

## English

Figure 2(a) presents an example test sequence.

The placeholder:

```
<AssertPlaceholder>
```

indicates that the original assertion has been intentionally removed.

The task of CHATASSERT is to automatically generate the correct assertion based on the given test sequence.

---

# ✅ Fig.2(b) 在干嘛？

---

## 中文

Fig.2(b) 展示了：

👉 CHATASSERT 如何通过与 ChatGPT 的多轮对话来生成和修复 assertion。

流程是：

1️⃣ 先让 ChatGPT 总结代码（理解语义）  
2️⃣ 再让它生成 assertion  
3️⃣ 运行测试  
4️⃣ 如果失败，就把错误信息反馈给 ChatGPT  
5️⃣ ChatGPT 再修复 assertion

---

## English

Figure 2(b) illustrates how CHATASSERT interacts with ChatGPT through multiple rounds of conversation to generate and repair assertions.

The workflow is:

1. Summarize the code
2. Generate an assertion
3. Run the test
4. Feed execution errors back to ChatGPT
5. Repair the assertion iteratively

---

# ❗你刚刚那句话差一点点的地方

你说：

> “chatassert通过chatgpt的请求开始对话”

这个意思是对的。

但更准确应该是：

---

# ✅ CHATASSERT 是 orchestration framework

它：

- 不只是“问 ChatGPT”
- 而是：

👉 **组织整个 interaction loop**

包括：

- prompt construction
- code summarization
- test execution
- static repair
- dynamic repair
- feedback injection

---

# 🎯 所以最准确的一句话（推荐背）

---

## 中文

Fig.2(a) 给出了一个缺少 assertion 的 test sequence，而 Fig.2(b) 展示了 CHATASSERT 如何通过与 ChatGPT 的迭代交互，结合代码总结和运行反馈，逐步生成并修复 assertion。

---

## English

Figure 2(a) provides a test sequence with a missing assertion, while Figure 2(b) shows how CHATASSERT iteratively interacts with ChatGPT, using code summaries and execution feedback to progressively generate and repair assertions.

---

# 🧠 你现在已经真正理解 Fig.2 了

因为你已经意识到：

👉 ChatGPT 不是“自己突然变聪明”

而是：

👉 CHATASSERT 在外面不断：

- 给信息
- 跑测试
- 喂反馈
- 逼它修

这才是论文真正贡献。
![[CHATASSERT-fig1.png]]


![[CHATASSERT-fig3.png]]

![[CHATASSERT-fig4.png]]

![[CHATASSERT-fig5.png]]

![[CHATASSERT-fig6.png]]





![[CHATASSERT-fig9.png]]

![[CHATASSERT-algorithmn.png]]
# Table 

![[CHATASSERT-table1.png]]

![[CHATASSERT-table2.png]]

![[CHATASSERT-table3.png]]


![[CHATASSERT-table4.png]]


