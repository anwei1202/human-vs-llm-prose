# human-vs-llm-prose
README – Final Project Replication Package
==========================================
# Distinguishing Human and LLM-Generated Text Using Linguistic Features

Course project for 36-315 Statistical Graphics & Visualization, Carnegie Mellon University.

## Summary

As AI-generated content becomes more prevalent, understanding how machine-generated language differs from human writing matters for detection, evaluation, and broader NLP applications. This project asks whether a standard set of quantitative linguistic features can distinguish human-written prose from large language model output, and whether that difference is uniform or concentrated in specific features.

Using the Human–AI Parallel English Corpus (HAP-E), we analyzed 66 linguistic features spanning tense and aspect, pronouns and reference, syntactic subordination, lexical complexity, and discourse markers. The short version: authorship does leave a real signal in the grammar, but it is built from many small, feature-specific effects rather than one dominant pattern. Pronoun usage and subordination structure carry genuine signal; tense and aspect barely do. Supervised models pick that signal up, while unsupervised methods cannot cleanly separate the two sources.

## Data

The [HAP-E corpus](https://cmustatistics.github.io/data-repository/language/hap-e.html) (CMU S&DS Data Repository) is built from 12,000 original texts collected from a variety of real-world sources. For each text, two roughly 500-word chunks were extracted: the first was used as a prompt asking an LLM to continue in the same style, and the second, the original human continuation, anchors a direct parallel comparison. Each observation is one 500-word chunk.

LLM continuations come from several models including GPT-4o and Llama, in both pretrained and instruction-tuned versions. Features are expressed as rates per 1,000 words, following Biber's (1988) multidimensional register analysis framework.

## Research questions and findings

**RQ1 — Can linguistic features distinguish human- from LLM-generated text?**
Yes, but unevenly. Pronoun choice and subordination structure carry substantial signal, while tense and aspect are weak predictors on their own: group means differ by less than one occurrence per 1,000 words, and the difference lives more in variability than in central tendency. Logistic regression coefficients show first- and third-person pronouns, indefinite pronouns, present participles, and that-subject constructions pushing toward the LLM class, while demonstratives, pro-verb *do*, *because*, and WH-object constructions push toward the human class. Substantively, LLM text trends structurally complex and generalized; human text trends context-grounded and referentially direct.

**RQ2 — Can unsupervised methods recover the same distinction?**
No. Principal component analysis on all 66 features (downsampled to balance class sizes) found no dominant component: PC1 explains only 15.4% of variance. Sources differ significantly on every component by ANOVA, yet the point clouds overlap heavily, and hierarchical clustering interleaves human and LLM texts across branches rather than splitting them into clean groups. These features are built for supervised classification and interpretation, not unsupervised discovery.

**RQ3 — Does instruction tuning leave a linguistic signature?**
Yes, a modest one. Comparing four Llama-3 checkpoints (8B and 70B, base versus Instruct), instruction-tuned models lean toward possibility modals, which produce hedged, flexible language, while base models lean toward necessity modals and more certain phrasing. Instruction-tuned checkpoints also use slightly less of nearly every pronoun type. Instruction tuning nudges a model's register toward lower certainty rather than overhauling its grammar.

## My contribution

This was a three-person course project. I was responsible for **RQ1**, the supervised analysis: designing the feature-group comparisons across tense and aspect, pronouns, and subordination; building and interpreting the logistic regression models; and producing the exploratory and model-based visualizations for that section. Ting Chen led RQ2 (PCA and hierarchical clustering) and Kevin Bao led RQ3 (instruction-tuning comparison). The report credits each section's author inline.

## Team

Anwei Yan · Ting Chen · Kevin Bao

## Reference

Biber, D. (1988). *Variation Across Speech and Writing.* Cambridge University Press.

