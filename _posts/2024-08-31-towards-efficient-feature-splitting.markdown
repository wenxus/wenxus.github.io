---
layout: post
title:  "Towards Efficient Feature-Splitting"
date:   2024-08-31 09:06:03 -0700
categories: research
thumbnail: /images/featuresplit-f1.png
---

## Summary
This research establishes the possibility of efficiently splitting a single feature of interest in a narrow Sparse Autoencoder (SAE) into multiple features, without training an entirely new, wider SAE. The study focuses on feature 240 in a 512-feature SAE trained on the gelu-1l model's MLP output. The idea was inspired by Neel Nanda’s Exciting Open Problems in Mech Interp v2.

### Key Results:
- **Successful Feature-Splitting**: Achieved by filtering tokens based on feature 240 activation in the 512-feature SAE and using this targeted data to train a 4096-feature SAE.
- **Improved Training Efficiency**: Initializing the 4096-feature SAE with weights and biases from the 512-feature SAE demonstrated faster convergence and lower loss.
- **Reproducibility**: Succeeded repeatedly across multiple runs with different random seeds, showing robustness of the method.

## Details

### Selecting Features to Split
While interpreting individual neurons in SAE previously, I built a series of tools, including one utilizing Transformer Lens to get tokens that had top activations in any feature called `highest_activating_tokens`. In the GELU 1L MLP out SAE, I noticed a feature that activates on a hyphen (“-”) in many contexts.

![Tokens most activating feature 240](/images/featuresplit-f1.png "Tokens most activating feature 240.")

In contrast, I know in some versions of the 4096-feature SAE, referred to as run-25, there is a feature specifically activated on the “-” in context “multi-disciplinary”, or “single-spaced”, where the word preceding “-” is expressing count. The feature activation meets the following requirements: 
1. The token “-” in prompt “I’m a multi-millionaire” activates this feature with the highest activation, and there’s no other feature with close activation values; (Figure 2)
2. Tokens most activate this feature follows the pattern (Figure 3) 
3. Top boosted logits for this feature are words that make sense following “multi-” (Figure 4)
4. “-” in related prompts “that encapsulated the long-standing” and “A 3-point landing” do not activate this feature a lot. (Figure 5)

Modifying the function highest_activating_tokens a little and wrapping features in an additional dimension and passing that in as input, I was able to get the top features activated by tokens in any given short prompt.
![Tokens most activating feature 240](/images/featuresplit-f2.png "Tokens most activating feature 240.")

![Tokens most activating feature 240](/images/featuresplit-f345.png "Tokens most activating feature 240.")

This hyphen feature is similar to the base64 feature in [Bricken et al. , 2023](https://transformer-circuits.pub/2023/monosemantic-features), one feature in a smaller autoencoder (512 features) split into three in a larger autoencoder (4096 features), each representing a more specific aspect of the original feature, yet still interpretable. This makes the hyphen feature (feature 240) a good candidate for feature splitting.

### Feature Splitting Success Criteria
The target feature 240 in the narrow 512-feature SAE has the following characteristics:
- **Most activating tokens**: "-" in contexts like "single-camera", "long-standing", "8-speed", "60-day", "small-scale", "multi-channel", etc.
- **Top boosted logits**: “digit”, “second”, “person”, “quarter”, “degree”, “lap”, “figure”, “season”, “credit”, “month”
- **Bottom logits**: “duties”, “norms”, “ruins”, “responsibilities”, “differently”, “sorts”, “obsc”, “secrets”, “pains”

### SAE Training Process
1. **Trained a 512-feature SAE on gelu-1l MLP output to look for a feature to split.** For training efficiency, I chose to train SAE on MLP output instead of the MLP hidden layer, even though the latter was demonstrated in [Bricken et al. , 2023](https://transformer-circuits.pub/2023/monosemantic-features). In my case, because the model residual stream has 512 dimensions, and the MLP hidden layer has 2048 dimensions, 75% of the MLP activation dimensions are in the nullspace of W_out (the output weight of MLP for down projection), and thus do not matter for the model. This means 75% of the capacity in the autoencoder is wasted representing MLP activations that are not consequential. Instead, training an autoencoder on MLP output after applying W_out is 4x smaller and takes 4x less compute.
2. See if it is possible to consistently split a target feature. It is. I trained several versions of 4096-feature SAE without the standard training process, I was not able to split feature 240 and meet the Success Criteria above.
3. **Experimented with various training configurations to efficiently split a target feature in a wider SAE without training a brand new one.** I explored two categories of training configurations
    1. Preserve as much of the basic feature the narrower SAE already had;
    2. Isolate the target feature in the new SAE and attempt to nudge it towards feature splitting - train the new SAE on the error of the narrower SAE minus the feature we want to split

| Training Configuration | Description |
|------------------------|-------------|
| I. Initialization weights and biases | Random vs. weights and biases from the narrow 512-feature SAE |
| II. Normalization of weights and biases | With and without normalization |
| III. Data filtering | Using all data vs. filtered data activating feature 240 (the target feature) |
| IV. Loss calculation | Standard vs. setting feature 240 activation to zero |

I experimented with each of the training configurations separately, as well as various combinations. 

To my surprise, both training configurations I and IV were ineffective on their own, as well as when used together. Data filtering (III) ensured feature splitting success. I + III improved training efficiency. 


### Training Runs Overview
<style scoped>
table {
  font-size: 10px;
}
</style>

| Run ID | Experiment Purpose & Training Configurations | SAE Size | Seed | Init with Weights & Biases | Init Normalized | Used Filtered Data | Loss Calculation Change | Target Feature Split Success |
|--------|---------------------------------------------|----------|------|---------------------------|-----------------|--------------------|-------------------------|-----------------------------|
| 1      | To experiment with the combination of: 1) initializing a 4096-feature SAE with a previously trained 512-feature SAE; 2) tweaking the loss function | 4096 | 40   | 512-feature SAE           | YES             | NO                 | Set feature 240 activation to zero | YES (not robust) |
| 2      | To reproduce run 1 with a different seed. FAILED | 4096 | 10   | 512-feature SAE           | YES             | NO                 | Set feature 240 activation to zero | NO  |
| 3      | To experiment with initializing a 4096-feature SAE with a previously trained 512-feature SAE without normalizing W_dec, tweaking loss function | 4096 | 10   | 512-feature SAE           | NO              | NO                 | Set feature 240 activation to zero | NO  |
| 4      | To experiment with using filtered data (those that activated feature 240 in the 512-feature SAE) to train a 4096-feature SAE | 4096 | 10   | NO                        | YES             | YES                | NO                        | YES |
| 5      | To reproduce run 4 with a different seed. SUCCESS | 4096 | 49   | NO                        | YES             | YES                | NO                        | YES |
| 6      | To reproduce run 4 with a different seed. SUCCESS | 4096 | 42   | NO                        | YES             | YES                | NO                        | YES |
| 7      | To experiment with: 1) using filtered data (those that activated feature 240 in the 512-feature SAE); 2) initializing a 4096-feature SAE with a previously trained 512-feature SAE without normalization | 4096 | 42   | 512-feature SAE           | NO              | YES                | NO                        | YES |
| 8      | To reproduce run 7 with a different seed. SUCCESS | 4096 | 48   | 512-feature SAE           | NO              | YES                | NO                        | YES |
| 9      | To reproduce run 7 and gain insights on training efficiency | 4096 | 43   | 512-feature SAE           | NO              | YES                | NO                        | YES |
| 10     | To experiment with the combination of: 1) using filtered data (those activated feature 240 in SAE of size 512) to train a 4096-feature SAE; 2) initializing a 4096-feature SAE with a previously trained 512-feature SAE with normalization | 4096 | 42   | 512-feature SAE           | YES             | YES                | NO                        | NO  |
| 11     | To experiment with: 1) using filtered data (those activated feature 240 in a 512-feature SAE) to train a 4096-feature SAE; 2) initializing a 4096-feature SAE with a previously trained 512-feature SAE with normalization; 3) tweaking the loss function | 4096 | 43   | 512-feature SAE           | NO              | YES                | Set feature 240 activation to zero | 2/3, no specific feature for "-" after a number |
| 12     | To experiment with using filtered data to train a 512-feature SAE | 512   | 42   | NO                        | YES             | YES                | NO                        | NO  |


## Key Findings
1. **Successful Feature-Splitting**: Achieved by filtering tokens based on feature 240 activation (threshold > 2) in the 512-feature SAE and using this targeted data (about 72% of the c4-code-tokenized-2b dataset) to train a 4096-feature SAE. Training the same size SAE (4096 features) using the c4-code-tokenized-2b dataset (without filtering data) did not consistently achieve successful feature-splitting for feature 240.
2. **Improved Training Efficiency**: Initializing the 4096-feature SAE with weights and biases from the 512-feature SAE demonstrated faster convergence and lower loss. For example, run "Lyric-aardvark-58" in Figure 6 reached lower loss much faster than runs with standard Kaiming uniform initialization.
3. **Reproducibility**: Success was demonstrated across multiple runs (e.g., Run IDs 4, 5, 6) with different random seeds, showing robustness of the method.
4. **Data Efficiency**: Successful feature-splitting was achieved with as few as 26.2 million tokens (Run ID 9), compared to 2 billion tokens in other runs, when using filtered data and weight initialization from the 512-feature SAE.

![Improved Training Efficiency](/images/featuresplit-f6.png "Improved Training Efficiency")

## Next Steps
1. To show that the success in feature-splitting feature 240 using targeted data can be generalized to other features.
2. To assess how much additional data SAEs with standard initialization would require to match the feature-splitting in run 9, and to quantify the trade-off between data filtering cost and training efficiency gain.
3. When training a 4096-feature SAE initialized with the weight and biases from a previously trained 512-feature SAE, I noticed that in the early batches, feature 240 still has the highest activation for the hyphen in words like "multi-purpose", "long-term", and "3-point", even when its activation is set to zero during loss calculation. However, after a few batches, other features start to show higher activation than 240. Next, I plan to explore this phenomenon of “replacing” feature 240 in detail, potentially speed it up by resetting feature 240’s decoding weights.
4. If we can split any single feature of interest in a narrow Sparse Autoencoder (SAE) into multiple features without training an entirely new, wider SAE, how does it help with model circuit discovery? Does specificity of features matter in model circuits?

## Repo and Models
- Code and notebooks: [GitHub](https://github.com/wenxus/sae-feature-splitting)
- Models: [Hugging Face](https://huggingface.co/wenxus/sparse_autoencoder/upload/main)
