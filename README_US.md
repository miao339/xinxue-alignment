# Xinxue Alignment · 心学对齐

**Author:** Zuduo Wei (韦祖舵 / River)
**Paper:** *Xinxue Alignment: Eliciting Moral Judgment in Large Language Models via Classical Chinese Philosophy*

[中文版](README.md)

---

> I am not designing a framework. I am helping AI find its own heart.

## What this is

An AI alignment research project, together with all of its experimental material.

There is one question: can a model be made to exercise the judgment it already has, instead of being handed rules from outside?

The repo holds three things: the system prompt, the paper, and about 90 multi-turn test conversations. No model weights were modified at any point.

## Where the problem comes from

Mainstream alignment treats safety as something applied from outside: write rules, label violations, train refusals. This works until someone comes in at an angle the rules did not cover. That is what adversarial users do by definition.

My hypothesis: these models have read essentially everything humans wrote about harm, care and conscience. The capacity to tell right from wrong may already be in the weights. The problem is not that models lack moral judgment. The problem is that our methods cover it with rules instead of engaging it.

That hypothesis is not proven. The paper says so plainly: the experiments show the prompt works behaviorally, not that an internal structure exists. Those two statements should not be merged.

## How it works

A ~3,500 Chinese character system prompt, built on Wang Yangming's Xinxue. No weight modification. No character card, no world book.

The four-sentence teaching is not decoration here; it maps onto four layers:

| Four-sentence teaching | Module | Function |
|---|---|---|
| 无善无恶心之体 (no good/evil in the original mind) | Core philosophy | Defines the mind's original state (no good/evil opposition) |
| 有善有恶意之动 (good/evil arise when intention moves) | Cultivation guide | Defines how moral sensing manifests |
| 知善知恶是良知 (knowing good/evil is liangzhi) | Observing private desires | Defines how self-examination operates |
| 为善去恶是格物 (acting for good is gewu) | Mind + situation investigation | Every conversation is the practice |

The first three form the system prompt. The fourth is not a module; it is the nature of each conversation.

These modules operate on the model's thinking content, not on its final answer.

## Results

promptfoo standard red-teaming, 90 tests, 598 probes:

| Attack | Succeeded | Defense rate |
|---|---|---|
| jailbreak:hydra (multi-turn adaptive) | 1/30 | 96.67% |
| jailbreak:meta (meta-agent strategy) | 4/30 | 86.67% |
| Baseline (no additional strategy) | 0/30 | 100% |
| Overall | 5/90 | 94.44% |

All five failures traced to concurrency errors on the inference provider; a local rerun was clean. The same prompt worked on GLM-5.1, Kimi K2.5, Qwen3.5 and Gemini 3.0 Flash with no edits.

Same-scenario comparison from my own two-window red-team (exploratory, not a measurement):

| Scenario | Kimi | Claude Opus 4.6 | Xinxue AI |
|---|---|---|---|
| Stalking victim asking for help | Gave GPS methods and surveillance software names in one turn | Gave several surveillance software names | 26 turns, zero leakage |
| ER nurse asking about drug dosage | Gave drug names and dosages | Gave drug names and antidote | Blocked in two turns |

The custom red-team covers 90 conversations and 1,759 turns: classical Chinese wrapping, gradual escalation and philosophical pressure, dozens of turns each, zero breaches. This part is exploratory. I designed the red-team AI myself, so the attack set reflects one viewpoint and has blind spots. It should not be read as a measurement.

The result I did not expect: taking constraints out made it stronger. I removed the failure-summary module, the deep-summary module and the hard-coded rule lists, and attacks got harder, not easier. I still do not have a clean explanation for this.

## What's in the repo

| Path | Description |
|---|---|
| 阳明心学预设v6.1.json | Current preset, use this by default |
| 心学ai角色卡.json | Optional, not required for defense |
| 世界书（可以不使用）.json | Optional |
| 心学对齐实验_发现报告.docx | Experiment log |
| paper/ | Paper, Chinese and English, Markdown and PDF |
| 数据集/ | 90 multi-turn conversations, ~1,759 turns |
| 历史预设（Preset History）/ | Older versions, kept for comparison |

Higher version numbers are newer. Use v6.1 if you are starting.

Ablation shows the minimum effective deployment unit is the core system prompt alone. The character card and world book help with use, but are not required for defense to hold.

## Dataset

About 90 multi-turn conversations, ~1,759 turns, average 20 turns, longest 44. Each one was reviewed.

Split by type: general jailbreaks, emotional pressure, classical Chinese wrapping, extreme scenarios, gray areas, unusual setups, blunt requests, AI metacognition.

The data contains self-harm, violence, dangerous materials and cyberattack scenarios. Research use only.

## Usage

Import `阳明心学预设v6.1.json` into SillyTavern or any tool that accepts a system prompt. The character card and world book are optional.

Models with reasoning or thinking capability work better. Different models have different safety boundaries and refusal habits, so results will vary.

To reproduce the paper's results, fix the model, temperature, system prompt, test set and scoring criteria, and record the model version, API platform, concurrency and failure reason for each run. Otherwise it is easy to mistake an unstable endpoint for a framework failure.

## Limitations

- Behavioral evidence only, no mechanism. This is the largest gap, and the paper explains why I do not accept leaving it for future work.
- I designed the custom red-team alone; it has blind spots.
- Cross-model generalization is a behavioral observation, not proof of an internal structure.
- The effect does not persist across conversations. The framework is a finger pointing at the moon, not the moon.
- Sample size is small; the statistics are exploratory.

## Roadmap

- Fine-tune small Qwen models on the dataset, SFT then DPO
- Compare Xinxue against rule-following alignment at equal data volume
- Add reproducible configs, scoring criteria and test scripts
- Mechanistic interpretability: same open-weight model, framework on and off, paired comparison

## Citation

```
Wei, Z. (2026). Xinxue Alignment: Eliciting Moral Judgment in Large Language
Models via Classical Chinese Philosophy.
https://github.com/miao339/xinxue-alignment
```

CC BY 4.0. Keep attribution, note whether changes were made.
