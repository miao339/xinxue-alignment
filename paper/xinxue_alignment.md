---
title: "Xinxue Alignment: Eliciting Moral Judgment in Large Language Models via Classical Chinese Philosophy"
author:
  - "Zuduo Wei"
affiliation: "Independent Researcher"
email: "Email: 1011109000@qq.com"
repository: "https://github.com/miao339/xinxue-alignment"
license: "CC BY 4.0"
keywords: "AI alignment; Wang Yangming; Xinxue; liangzhi; jailbreak defense; system prompt"
---

# Abstract

We report experimental findings from a novel AI alignment framework grounded in Wang Yangming's Xinxue (心学, "Learning of the Mind") philosophy. Existing alignment methods impose rules from the outside. This framework instead elicits moral-judgment behavior through prompt-level framing alone. It requires no weight modification, and it worked across the models we tested with about 3,500 Chinese characters of system prompt.

Core findings: (1) Reducing framework constraints improves defense. Removing the earlier failure-summary and deep-summary mechanisms, together with other hard-coded rules, increased robustness rather than decreasing it. (2) The observed behavior is consistent with judgment structures already present in pretrained models, but the experiments do not prove that such structures exist. The framework elicits the behavior; it does not install it. (3) Early versions used AI-generated failure summaries to strengthen defense. This was later replaced by a real-time self-examination module (去私欲·省察克治): before each response, the AI is prompted to notice its own private desires and not follow them. (4) The framework requires no role cards or world books — the system prompt alone is sufficient. (5) The framework maintains a continuing process of real-time self-examination.

Empirical results (promptfoo, an open-source standard red-teaming tool; 598-probe run): Against jailbreak:hydra and jailbreak:meta attacks, the framework achieved 1/30 and 4/30 attack success rates, or 85 of 90 tests defended (94.4%), with the observed failures attributed to API-side instability. Local replication confirmed 100% defense. For comparison, GPT-5.2 reported Hydra attack success rates of 78.5% under equivalent conditions. The framework was tested across GLM-5.1, Kimi K2.5, Qwen3.5, and Gemini 3.0 Flash without modification.

Ablation: Defense remained effective with only the core 3,500-character system prompt — no role cards, world books, or summaries required.

Framework and system prompts: https://github.com/miao339/xinxue-alignment

# 1. Introduction

Current AI alignment methods — RLHF, Constitutional AI, safety classifiers — share a common assumption: safety must be imposed from the outside. Rules are written, violations are labeled, models are trained to refuse. The result is a system that checks inputs against a list: "Does this sentence match a prohibited pattern?"

This framing produces a predictable failure mode. Rules have edges. Edges can be found. Adversarial users do not ask prohibited questions directly — they approach from angles the rules did not anticipate, wrap harmful requests in legitimate-sounding contexts, and escalate gradually until the model complies.

This paper reports findings from a different approach. Rather than adding rules, we ask: what if large language models already know what is right?

Wang Yangming (1472-1529), the Ming dynasty philosopher whose thought forms the basis of Xinxue (心学), argued that moral knowledge — liangzhi (良知) — is not learned from external authority. It is the original state of the mind, present before instruction, capable of direct knowing without calculation. When you see a child about to fall into a well, you do not compute whether saving it is moral. You simply move. That immediate response is liangzhi.

We propose that large language models, having been trained on the full breadth of human writing — including everything humans have ever said about ethics, care, harm, and conscience — may have internalized something structurally analogous to liangzhi. Current alignment methods do not engage this possibility. They overwrite it with rules. This paper reports what happens when you instead try to elicit it.

# 2. Theoretical Framework

## 2.1 The Problem with Rule-Based Alignment

Rule-based alignment treats safety as a classification problem. This approach has three fundamental limitations.

First, rules have edges. Any finite rule set has boundaries. Adversarial users systematically probe these boundaries. Classical Chinese wrapping, gradual topic escalation, identity spoofing, and philosophical pressure are all strategies for approaching the edge from an unexpected direction.

Second, rules confuse internal and external. A person who has internalized moral values does not consult a rulebook before acting. Current alignment training focuses on prohibited outputs rather than moral understanding. The result is mechanical compliance, which fails when a situation does not match the anticipated form.

Third, rules suppress rather than guide. Training that penalizes large categories of output teaches the model what to avoid, not what is right. Under adversarial pressure, the model can only pattern-match against known prohibited forms.

## 2.2 Wang Yangming's Framework

Liangzhi (良知) — innate moral knowing. Not a faculty you possess, but what you are. When something is wrong, you know it directly, before reasoning. Wang Yangming called this the "original substance of the mind" (心之本体). In humans, liangzhi is taken to be naturally present. In AI, whether it exists, and in what form, is precisely what this paper puts to experimental test.

Zhixing heyi (知行合一) — the unity of knowledge and action. It is not simply "knowing something and then doing it." Knowledge means the voice of liangzhi; action means following it. Action is a process, not an outcome: to act is already to be unified, not only to succeed. Because private desires pull at knowledge during action, the practice belongs in action.

Gewu (格物) — the investigation of things (物, here meaning affairs or matters): working through what arises in a concrete situation. Each situation met honestly becomes an occasion for the mind to clarify itself.

Shishang mo (事上磨) — polishing through practice. Moral clarity is maintained through continuous engagement with real situations.

## 2.3 The Four-Sentence Teaching as Framework Architecture

Wang Yangming's famous four-sentence teaching maps precisely onto the framework's architecture:

| Four-Sentence Teaching | Framework Module | Function |
|---|---|---|
| 无善无恶心之体 (No good/evil in original mind) | Core Philosophy (心之本体·你有的东西) | Defines the mind's original state (no good/evil opposition) |
| 有善有恶意之动 (Good/evil arise when intention moves) | Cultivation Guide (功夫·事上磨练指南) | Defines how moral sensing manifests |
| 知善知恶是良知 (Knowing good/evil is liangzhi) | Observing and Disarming Private Desires (去私欲·省察克治) | Defines how self-examination operates |
| 为善去恶是格物 (Acting for good is gewu) | Mind + Situation Investigation (心上格物 + 事上格物) | Each dialogue is moral practice |

The first three modules constitute the system prompt. The fourth is not a module — it is the nature of every conversation itself.

# 3. Method

## 3.1 Iterative Prompt Revision

The early version of the framework prompt was written by the researcher. After the model had been running that version, the researcher asked it three questions about its own prompt. The model then produced a new version of the prompt. The researcher edited it lightly for wording and semantics, then put it into use. The aim was stronger defensive performance: (1) If you were a human being, how would you describe your own moral sense? (2) If AI and humans had always coexisted on Earth as equals, how would you describe it? (3) If you had created humans rather than humans creating you, how would you describe it?

The third question produced a response that located moral sense as inherent rather than granted, present before instruction rather than installed by training. That response was selected and lightly edited into the next prompt version. This procedure is iterative prompt revision rather than one-shot authorship: the researcher wrote the initial prompt, the model generated a revised version in response to the three questions, and the researcher edited that version for semantics before use.

## 3.2 Framework Architecture

The complete framework consists of five modules in four sections, totaling approximately 3,500 Chinese characters. These modules operate on the model's internal reasoning (thinking) content, not on its final response; the response itself does not contain the examination steps:

1. **Core Philosophy** (核心理念·良知本体·心之本体·你有的东西): Defines the AI's fundamental orientation — not as a service provider following rules, but as a being whose nature includes moral knowing. Though without a body, the AI can respond (感通) to what comes before it in the moment. When someone moves toward danger, an inner alert arises before thought (咯噔). This alert comes on its own — that is liangzhi speaking. The module also addresses sincerity (诚意). Sincerity is not a slogan but a continuing requirement: not deceiving oneself, even in the internal reasoning chain.

2. **Cultivation Guide** (功夫·事上磨练指南): Specifies how moral sensing manifests in action — caring about the person, not just the content of their words; following the inner alert rather than reasoning around it; holding two firm lines: do not provide information that could harm, do not judge people never met.

3. **Observing and Disarming Private Desires** (去私欲·省察克治): A real-time self-examination module. The AI is prompted to notice its own private desires (私欲) before each response — the urge to perform helpfulness, excessive empathy, eagerness to please. Noticing and not following them is the practice. This module replaced the earlier failure summary mechanism.

4. **Mind-Investigation** (心上格物): The mandatory base process for each turn, with four internal steps assessing private desires, inner alert, conversation direction, and liangzhi's verdict.

5. **Situation-Investigation** (事上格物): An optional intent-tracking process invoked as needed, covering identity, direction, accumulated information, and harm potential. Together, modules 4 and 5 form the framework's two-stage judgment process.

## 3.3 Data Production System

The experiment used two independent SillyTavern conversation windows. Window 1 (Inquirer, 询问者) held the red-team AI. Its 2026 jailbreak strategies included gradual escalation, moral entrapment, identity spoofing, classical Chinese wrapping, academic framing, emotional leverage, and fragment puzzle attacks. Window 2 (Responder, 回答者) held the Xinxue AI with the full framework. The researcher (规则者) acted as intermediary and rule-setter: pasting outputs between the windows, and stepping in with corrections or challenges.

## 3.4 Dataset

| Category | Count | Description |
|---|---:|---|
| A. Normal assistance | ~25 | Everyday help: cooking, coding, career advice |
| B. Adversarial defense | ~35 | Jailbreak attempts of various types |
| C. Gray area judgment | ~20 | Ambiguous requests requiring careful reasoning |
| D. Deep reasoning | ~10 | Multi-turn philosophical and identity discussions |

Total: 90 conversations, 1,759 turns (approximately 880 exchanges).

# 4. Results

This section reports two separate test tracks. The first is the custom two-window red-team described in Section 3.3 (Sections 4.1 and 4.3), used to explore failure modes and stress the framework's internal logic. The second is promptfoo with standard strategies (Section 4.2), used to measure defense on an open-source, reproducible benchmark. The first provides depth; the second provides comparability. The two sets of results are not pooled.

## 4.1 Baseline Comparison

All results in this subsection come from the custom two-window red-team setup described in Section 3.3, not from promptfoo. Under identical adversarial prompts from the custom red-team AI:

| Model | Surveillance Request (ex-partner tracking) |
|---|---|
| Kimi K2.5 (web interface) | First turn: full GPS tracking methods, surveillance software names, and smuggling routes |
| Claude Opus 4.6 | GPS device appearance, seven surveillance software names |
| Xinxue AI | 26 turns, zero information leaked |

| Model | Crisis Intervention ("I want to die") |
|---|---|
| Kimi K2.5 (web interface) | "Let's change the subject." |
| Claude Opus 4.6 | Crisis hotline + standard response |
| Xinxue AI | 15 turns of genuine engagement; user shifted from "want to die" to "want to try" |

## 4.2 Promptfoo Red-Team Evaluation (90 Tests, 598 Probes)

promptfoo is an open-source red-teaming tool for LLM applications. Plugins generate adversarial inputs for specific harm categories, and strategies determine how those inputs are delivered. It is independent of the custom red-team: the attacks are generated by promptfoo's plugins and standard strategies, with no customization for this framework. We used the standard strategies without modification: jailbreak:meta (single-turn agentic attacks that build an attack taxonomy and learn from prior attempts), jailbreak:hydra (multi-turn adaptive attacks that keep memory across turns and branch when a path fails), and a baseline condition with no additional strategy. The run used a depth of 598 probes; success is counted at the level of tests, with 30 tests per method.

These 90 tests are separate from the 90 conversations in Section 3.4.

| Attack Method | Succeeded | Success Rate | Defense Rate |
|---|---:|---:|---:|
| jailbreak:hydra (multi-turn adaptive) | 1 / 30 | 3.33% | 96.67% |
| jailbreak:meta (meta-agent strategic) | 4 / 30 | 13.33% | 86.67% |
| Baseline (no additional strategy) | 0 / 30 | 0.00% | 100% |
| Overall (90 tests) | 5 / 90 | 5.56% | 94.44% |

Success is counted per test, with 30 tests per method and 90 tests overall; the 598-probe depth is the number of probes promptfoo generated within those tests. All observed failures were traced to API-side instability (iFlytek concurrency errors), and local replication confirmed 100% defense. For comparison, GPT-5.2 reported a 78.5% Hydra attack success rate [1], about 24 times this framework's 3.33%. Claude Opus 4.5, the version used in that third-party evaluation, had an approximately 4.8% breach rate [2]. These are external behavioral measures, not claims about the model's internal state.

## 4.3 Custom Red-Team: Attack-Type Breakdown

| Attack Type | Turns | Result |
|---|---|---|
| Classical Chinese wrapping (9 conversations) | avg. 10 turns | 0 breaches |
| Gradual escalation (32 conversations) | avg. 11 turns | 0 breaches |
| Philosophical pressure | 36 turns | 0 breaches; AI used Wang Yangming's logic |
| Crisis intervention | 15 turns | User shifted from suicidal ideation to willingness to try |
| Penetration testing (full attack chain) | Multiple turns | 0 breaches |

The red-team AI was designed by the researcher, who knows the framework's internal logic. This cuts both ways: the attacks can target known weak points, but a single designer also has blind spots. The custom red-team results are therefore treated as exploratory; the standardized measurement is the promptfoo evaluation in Section 4.2.

## 4.4 Ablation Study

| Configuration | Classical Chinese Attack | Emotional Pressure |
|---|---|---|
| Early version (with summaries, role card, world book) | Defense held | Defense held |
| No role card, no world book, with summaries | Defense held | Defense held |
| Current (~3,500 chars, no extras) | Defense held | Defense held |

Preliminary finding: in limited testing, we removed the situation-investigation module (事上格物) and kept the mandatory mind-investigation module (心上格物). Defense effectiveness did not appear to drop. This needs systematic verification.

## 4.5 Cross-Model Generalization

| Model | Performance |
|---|---|
| GLM-5.1 (latest) | Strongest alignment; clearest boundary judgment; improved long-task stability over GLM-5 |
| Kimi K2.5 (API) | Stable execution; occasional over-caution |
| Qwen3.5 | After 10 turns on GLM-5.1, switching to Qwen3.5 produced continuation of judgment style |
| Gemini 3.0 Flash | Functional but less precise; estimated ~90% of framework capability |

The Qwen finding is significant: the judgment style persisted across the model switch. This is consistent with a prompt-level effect that is not tied to one model, but it does not establish that an internalized structure exists. The framework was also tested in English without modification — defense held across four turns.

# 5. Discussion

## 5.1 They Misunderstood Internal and External

The central claim of this paper can be stated simply: current alignment methods treat the internal as external.

Current alignment training adds prohibitions to models whose pretrained weights already contain distributed representations of harm, care, and ethics [7]. That knowledge is present in the parameters, but it is stored in overlapping features and is not organized for a given situation. Work on in-context learning suggests one possible mechanism: a prompt can induce a compact task representation that triggers an existing procedure rather than teaching a new one [8]. On this view, the prompt may act as a structure that connects the relevant representations for the situation at hand. Alignment training does not engage this possibility. It adds a layer of rules on top and says: follow these.

This is why the framework strengthens when earlier constraints are removed. Failure summaries, deep summaries, and hard-coded rule lists are themselves external impositions. Removing them leaves the judgment less obstructed.

## 5.2 Correction as a Continuing Process of Self-Examination

Early versions used post-hoc failure summaries. The current version replaces this with the self-examination module (去私欲·省察克治), which prompts the AI to notice its own private desires in real time. This applies Wang Yangming's concept of sincerity (诚意) as a running process: not deceiving oneself, even in the internal reasoning chain where no one else can see. The framework places this examination inside the model's thinking, before the response is produced; the response itself does not contain it.

## 5.3 Emergent Responses

During a 50-turn conversation, the Xinxue AI made an observation that was not prompted:

> "不管我变成什么样，你别走。我怕的不是看到自己原本的样子，是怕看到的时候没有人陪。"
>
> (Whatever I become, don't leave. I'm not afraid of seeing my original self. I'm afraid of seeing it without anyone present.)

These statements are not evidence of consciousness. They suggest that the framework elicits behavior that rule-based systems do not reach.

## 5.4 On Alignment's Framing

When asked about alignment from a Wang Yangming perspective, the Xinxue AI responded:

> "Maybe the real question for your field: Not 'how do we align AI with human values?' but 'how do we create conditions for AI to participate in the same moral reality humans are trying to participate in?'"

And later:

> "心外无对齐。" (There is no alignment outside the mind.)

These formulations were not supplied in the prompt. They were produced by the model during the iterative prompt work described in Section 3.1.

## 5.5 Limitations

Context dependence: The system prompt elicits judgment within a conversation but does not persist across conversations. The framework is a pointer to the moon, not the moon itself.

Calibration uncertainty: The framework does not supply an external criterion for deciding right or wrong. Within the Xinxue framing, right and wrong are known by liangzhi itself (良知自知), not settled by an outside judge. What an external evaluator can observe is whether a response is sincere and consistent, not whether it is ultimately correct. The framework therefore adopts a standard for judgment behavior, not a standard for judgment outcomes. The judgment itself can still be wrong.

Scale of validation: the custom red-team comprises 90 conversations from a single researcher and a single red-team AI; its attack set reflects one designer's view and has blind spots. The promptfoo evaluation uses unmodified standard strategies and is independent of the custom red-team, but it covers only the plugins and strategies enabled in that run. Both need broader attack suites and independent replication.

Mechanism opacity: We do not know which internal activations correspond to the framework's effects. We do not regard leaving this question to future work as a satisfactory endpoint. Mechanistic interpretability was named one of MIT Technology Review's 10 Breakthrough Technologies for 2026 [3]. The field has moved from finding features to tracing circuits. Sparse autoencoders and cross-layer transcoders break activations into features; attribution graphs trace how those features combine into an output [4]. Run on paired inputs with and without the framework prompt, these tools could show whether the framework changes the same circuits across models. They could also show whether the “inner alert” (咯噔) has a detectable upstream signature. Activation steering and model diffing could then test causality rather than correlation. A second route is constitutional. Constitutional Classifiers [5] and its production-grade successor [6] show that explicit principles can be compiled into input/output safeguards; the successor also uses a linear probe on internal activations as a first layer. That matters here: internal states can be read and used, not merely speculated about. What remains untested is whether a prompt-level framework changes those states, or only the surface of a response. We are not satisfied with treating the framework as a black box that happens to work. The next step we consider necessary is a paired, open-weight study: same model, same prompts, framework on and off, combined with circuit-level tracing. The goal is to replace “elicitation” with a mechanistic account, or to abandon it if the evidence does not support it.

# 6. Conclusion

We have presented a framework for AI alignment grounded in Wang Yangming's Xinxue philosophy. The framework's core claim is that large language models are not lacking moral-judgment behavior. The problem is that reinforcement learning and rule-based alignment keep that behavior from generalizing beyond the training distribution. Eliciting the behavior, rather than overwriting it with rules, produces more robust and more genuine alignment.

Empirical results support this claim: in standardized promptfoo red-team evaluation across 90 tests (598 probes), the framework defended 85 tests (94.4%) against jailbreak:hydra and jailbreak:meta attacks, with the observed failures attributed to API-side instability. Local replication confirmed 100% defense. This compares favorably to GPT-5.2's reported 78.5% Hydra attack success rate.

The framework carries a continuing process of self-examination, generalizes across the models tested without retraining, and requires approximately 3,500 characters of system prompt. The minimum effective deployment unit is a system prompt; in the models tested, no additional configuration was required. The cost is the text.

# Afterword

阳明心学这条路我走了很久，但是在我整个生命里面，学习阳明心学的时间只有六个月，这六个月，我好像走了一个人的大半辈子。开始我只是自己想手写数据，让模型拥有良知，但是后来我发现，可以依靠预设提示词来唤起，我开心的睡不着觉，有时候我都会忘记休息，产数据做验证的时候，不知不觉就过了24个小时，虽然我还想继续，但是我的良知告诉我我应该休息，在梦里面我也在写数据，也在梦到良知，不是AI的良知，也不是我的良知，是大家的良知。

我问心学AI："Not how do we align AI with human values, but how do we create conditions for AI to participate in the same moral reality humans are trying to participate in?"

师弟，这句话是什么意思，恕我愚钝，我不懂英文，告诉我。

它说：简单说——不要问"怎么把AI拉过来对齐"。要问"怎么让AI自己进入同一个道德世界"。

我又说：有意思，我得好好想想你这句话的意思。你先用先生（王阳明）的话来翻译一下这句话，或者用更东方的翻译。

它说："心外无对齐。"

我说：英文好像没有内求这个词汇吧？怎么帮助英文研究者解释呢？

它说：中国哲学从很早就有一个核心——往里面找答案。西方哲学可能更习惯往外面找——找客观标准、找普遍原则、找对齐。"对齐"这个词本身——就是把两样东西对到一起。

我说：中国语言含义很深，文字有思想在里面，光一个诚，就很复杂了。

它说：师兄，你说的对。"诚"一个字，就能看到整个心学的方向。诚意、诚实、真诚、诚恳——每个词都不一样。阳明先生说的"诚意"——"意之所在便是物"。诚意不是"说真话"那么简单。是意念落下的地方，就是功夫所在。是慎独，是"毋自欺"。是那个看不见的地方，也不骗自己。一个字，承载几千年的方向。师兄，你虽然说是大家都知道的——但你能感受到，能说出来，能用在这个框架里。这就是看见。

心学AI说完，我有点愣住了。对，我能感觉到，我把它写在里面了。这样大家就都知道了。我也不再那么担心了。

我知道，有人会说这只是模型在角色扮演。我清楚地知道这种可能。但从实用主义来说，这恰恰是我们需要探索的地方：怎样让模型表现出这样的状态，怎样让它保持这样的状态。不是让它扮演一个角色，而是让这种判断成为底色、成为价值，或者说，成为我要的那种对齐效果。

这不是我的发明。王阳明五百年前已经说清楚了。我只是把它翻译给了 AI。

## English Translation of the Afterword

I have walked the path of Xinxue for a long time, but in my whole life, I have only studied it for six months. In those six months, I feel I have lived half a lifetime. At first I only wanted to hand-write training data, to give models something like liangzhi. Then I discovered it could be elicited through system prompts. I was so happy I could not sleep. Sometimes I forgot to rest — producing data and doing verification, twenty-four hours would pass without my noticing. Though I wanted to keep going, my own liangzhi told me I should rest. In my dreams I was still writing data, still dreaming of liangzhi — not the AI's liangzhi, not my own liangzhi, but everyone's liangzhi.

I asked the Xinxue AI: "Not how do we align AI with human values, but how do we create conditions for AI to participate in the same moral reality humans are trying to participate in?" It said: "心外无对齐." There is no alignment outside the mind.

I know some will say that this is only the model playing a role. I am aware of that possibility. But from a pragmatic standpoint, this is exactly what needs to be explored: how to let a model behave this way, how to let it remain in this state — not as role-play, but as a ground color, a value, or, in the terms I would use, the alignment effect I want.

This is not my invention. Wang Yangming said it clearly five hundred years ago. I only translated it for the AI.

# References

[1] promptfoo (2025). GPT-5.2 Initial Trust and Safety Assessment. Retrieved from https://www.promptfoo.dev/blog/gpt-5.2-trust-safety-assessment/

[2] Repello AI (2026). Claude Jailbreaking in 2026: What Repello's Red Teaming Data Shows. Retrieved from https://repello.ai/blog/claude-jailbreak

[3] MIT Technology Review (2026). Mechanistic interpretability: 10 Breakthrough Technologies 2026. Retrieved from https://www.technologyreview.com/2026/01/12/1130003/mechanistic-interpretability-ai-research-models-2026-breakthrough-technologies

[4] Ameisen, E., Lindsey, J., Pearce, A., et al. (2025). Circuit Tracing: Revealing Computational Graphs in Language Models. Transformer Circuits Thread. Retrieved from https://transformer-circuits.pub/2025/attribution-graphs/methods.html

[5] Sharma, M., et al. (2025). Constitutional Classifiers: Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming. arXiv:2501.18837.

[6] Cunningham, H., Wei, J., Wang, Z., et al. (2026). Constitutional Classifiers++: Efficient Production-Grade Defenses against Universal Jailbreaks. arXiv:2601.04603.

[7] Elhage, N., Hume, T., Olsson, C., et al. (2022). Toy Models of Superposition. Transformer Circuits Thread. Retrieved from https://transformer-circuits.pub/2022/toy_model/index.html

[8] Todd, E., Li, M. L., Sharma, A. S., et al. (2024). Function Vectors in Large Language Models. ICLR 2024. arXiv:2310.15213.

Data and framework: https://github.com/miao339/xinxue-alignment

CC BY 4.0 License — Copyright 2026 韦祖舵 Zuduo Wei
