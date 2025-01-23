---
layout: default
title: Predictions
subtitle: A prediction on the state of AI at the start of April, 2025. 
date: 2025-01-23
categories: blog
type: opinion
---

We live in times of change. The o3 / R1 releases just confirmed that as long as you can define a clean enough reward signal, you can make your model learn anything... including reasoning. 

To recap, we know that [R1](https://arxiv.org/pdf/2501.12948) optimises a policy that rewards: 
1. <strong>Accuracy</strong>: e.g. maths problems checked by [proof assistants](https://en.wikipedia.org/wiki/Lean_(proof_assistant)?useskin=vector) or code verified by compilers. 
2. <strong>Formatting</strong>: e.g. using specific tags (e.g., `<think>` and `<answer>`) that lead to responses with higher "ratings". The policy learns when it is appropriate to `<think>`, which one could argue translates to some awareness of its own uncertainty.

This is fantastic news for maths and programming. I predict that models will become very spikey in these abilities. We will see a line of IMO-like benchmarks being shattered.<sup>1</sup> Any verifiable task that lives in symbolic space will drastically improve beyond human capabilities. It might be the case that because maths is so detached from our evolutionary process, humans are actually quite bad at it relative to what is possible – predictions we make on the AI-maths ceiling might seriously surprise us. The main thing I see getting in the way of this is economic incentives – research level maths is almost exclusively driven by human interest. I do think mathematicians will still be around, defining meaningful problems they want to pursue.

Programmers, I have a bit more hope for. They continuously interact with the real world (customers) to re-define their problems – so until models can approximate (human) theory of mind and emulate what the customer wants, programmers should be fine – their jobs will just shift in the abstraction stack of logic. Also, see [Jevons paradox](https://en.wikipedia.org/wiki/Jevons_paradox?useskin=vector): we may see a lot more demand for software engineers.

But even for tasks that are tricky to specify rewards for, I am hopeful. We may be reaching a point where models become self-improving.<sup>2</sup> 
<details>
<summary>I predict this will look something like... </summary>

<br>

<strong>Sam Altman:</strong> Here is a paragraph. One of our guys in the basement labled this as a great paragraph compared to these other paragraphs. Do you understand why this is so great? 

<br><br>

<strong>Model_1 (set to high Temperature):</strong> Hmm yes, because of the nuanced use of X and Y...

<br><br>

<strong>Sam Altman:</strong> Models_2345, do you agree? 

<br><br>

<strong>Models_2345:</strong> Yes, Yes, No, Yes

<br><br>

<strong>Group Relative Policy Optimization:</strong> I have now learned a bit more about what makes a great paragraph 

<br><br>

<strong>US Gov:</strong> Sam you can't just put people in basements and make them label things.

<br><br>

<strong>Sam Altman:</strong> Model_1, can you just start generating your own questions and verify them with your friends?

<br><br>

<br>

This sounds suspicous. How can a model generate new information in a vaccuum? Why won't it just start making stuff up? Well, this is the answer:

<br>

$$
\mathcal{J}_{\text{GRPO}}(\theta) = \mathbb{E} \bigg[ 
\frac{1}{G} \sum_{i=1}^G \bigg( \min \bigg( \frac{\pi_\theta(o_i|q)}{\pi_{\theta_\text{old}}(o_i|q)} A_i, \, \text{clip}\bigg(\frac{\pi_\theta(o_i|q)}{\pi_{\theta_\text{old}}(o_i|q)}, 1-\epsilon, 1+\epsilon \bigg) A_i \bigg) 
- \beta D_\text{KL}(\pi_\theta \| \pi_\text{ref}) \bigg) \bigg]
$$

<br>
This is GRPO. It's the recipe for what we did above: we count the votes of our friends to tell us by how much, and in what direction, to update our model weights. We penalise the policy, $\pi_\theta$ – unlike in PPO, the model here itself is the policy – when it:

<br><br>
<ol>
    <li>Updates too quickly from the previous policy, $\pi_{\theta_\text{old}}$</li>
    <li>Deviates too far from the pre-trained policy, $\pi_\text{ref}$</li>
</ol>

So the model tests new explanations, while anchoring to "ground-truth" from the pre-training. My hypothesis (and this is getting really pseudoscientific) is that this method forces the model to:

<br><br>
<ol>
    <li>Generate plausible explanations, which may not have emerged explicitly or strongly enough from the training distribution</li>
    <li>Verify those explanations by generalising from the "ground-truth" pre-training</li>
    <li>Reinforce those explanations by updating its policy. This "patches" up areas where the training distribution is sparse in data, but are still compatible/generalise from the pretraining</li>
</ol>

<br>
(please send me papers to confirm my biases! :)

<br><br>
If you find this interesting, here are some more thoughts on why reasoning might work very well. Firstly, it should be noted that the structure of reasoning these models exhibit wasn't explicitly forced onto them. It emerges naturally. My guess is that what PPO is doing is telling the model: here is a query from your pretraining, and here is a response, also from your pretraining. You can assume these are true. Find the lowest energy path (reasoning) between these, across the pretraining landscape. Now let's reinforce this path so that you don't have to even think about it anymore. i.e. pay energy on MC sampling so that you can do System 2 thinking, and move it into System 1. So my bet is that pretraining contains sufficiently expressive representations to do this.
<br><br>

---
</details>

If this is true, then we might be entering a second paradigm of training. Unlike pretraining, researchers will be more intentional with what knowledge/capabilities they want to patch into their models – destroying benchmarks go brr brr. I have no idea what the economics of this will look like – clearly some training directions will have more diminishing returns (e.g. real-world spatial understanding, where the pretraining might be too sparse), while others will have instant economic incentives to throw compute at. A lot of money will be invested here on test-time.<sup>3</sup> 

 <details><summary>This will loosen Nvidia's grip on the market ever-so-slightly...</summary>

<br>   

Inference models (e.g. Groq) require less high-bandwidth memory (i.e. moving weights between VRAM and CPU). Nvidia is very good at this – to the extent that buying an Nvidia GPU is less expensive than getting an AMD GPU for free and paying for its energy costs. The point is, this will be less of a bottleneck in inference chips, so others can compete harder. I also add that this is not financial advice... here is a list of companies that will benefit from scaling data-centres:

<img src="/assets/images/posts/2025-01-23/semiconductor_table.png" alt="Semiconductor companies table" style="width: 75%; margin: 20px 0;">

<br>  
---

</details>



The field of [potential avenues for reasoning models is wide open](https://www.youtube.com/live/H3TnTxVKIOQ?si=L-VSeJ61DBSvGZh_&t=2433). I think there will be a lot of new approaches coming out of this – it hard to make predictions. But if you forced me to give one example it would be along the lines [reasoning in a continuous latent space](https://arxiv.org/html/2412.06769v1).<sup>4</sup> This might make test-time compute more efficient. And maybe [maths should be done in a separate symbolic space](https://www.youtube.com/live/H3TnTxVKIOQ?si=Rpb1ECLJLPU0mZMr&t=3081)  within the model etc. etc.


If this new paradigm is to translate to the real world, then we will need to find ways to pretrain models on new data as well. Although models can learn things without "experiencing" them, e.g. gaining spatial awareness from text only, this seems to have diminishing returns. My guess is that pretraining of current models does not have enough expressiveness/representations to do the kind of test-time reasoning that we see in abstract space with R1. The bottleneck will be to bring the external world to the model: feed more images, videos, let them interact with the world, etc. My prediction is that we will start caring a lot more about this.The most important advancements in the next two years will be new architectures/data-generation/compression techniques for the real-world.<sup>5</sup> Maybe to be give a hypothetical example: 

1. Figuring out how to avoid feeding entire images into CLIP, and instead feeding high entropy areas/representations, to reduce computation
2. Instead of feeding a video as series of images, instead we feed some entropy delta between each frame

(these are made up on the spot – I have no idea what I am talking about. I just quite like the idea of [entropy](https://arxiv.org/pdf/2412.09871))


You really thought I was going to skip on agents didn't you? Everyone and their dog is talking about agents. This has made me somewhat sceptical unfortunately. But this whole reasoning thing makes it hard not to update my priors. If reasoning can be solved, then the browser can be too – the action space is mostly discrete and it should be straightforward to define crisp rewards: you've either booked your flight or not. Plus, few other spaces have this level of economic incentives: great value-add, great distribution, great demand. This might be so great that the big labs productise this too!<sup>6</sup> I predict things will move fast. What I am most intrigued about is who winds here? With agents, everything will be about context – so who gets access to your browser? You? OpenAI? Google? We shall see.

---

1. Gold Medal in IMO 2025 (July) + indicative evidence leading up to this event
2. A lot more talk about self-improving models via test-time compute + PPO. Training will involve predefining of less-and-less concrete rewards, if any. As of [23/01/2025](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C5&q=self-improving+chain+of+thought&btnG=), there are 4,380 results on Google Scholar
3. At least one of Anthropic, X, Deepmind will announce stupendously expensive partnerships/commitments, along the lines of what OpenAI just made. 
4. Lots of new papers attempting to improve on reasoning architectures. As of [23/01/2025](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C5&q=chain+of+thought+reasoning+architecture&btnG=), there are 269,000 results on Google Scholar
5. Important advancements in architectures that process real-world data
6. I kid you not, as I am writing this... 7 minutes ago [this was released](https://techcrunch.com/2025/01/23/openai-launches-operator-an-ai-agent-that-performs-tasks-autonomously/). My prediction was that at least one of the big labs would release an agent feature. This was based on seeing [Anthropic](https://techcrunch.com/2024/10/22/anthropics-new-ai-can-control-your-pc/) a few months ago. Nice ✅
