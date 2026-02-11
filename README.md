# Lab 3 Report: Contextual Bandit-Based News Article Recommendation

## 1. Introduction
This project implements a **Contextual Multi-Armed Bandit (CMAB)** system to recommend news articles to users. Unlike standard Multi-Armed Bandits, which treat all users identically, this system utilizes "side information" (Context) to tailor recommendations.

* **Contexts:** User Categories (`User1`, `User2`, `User3`) derived from a Decision Tree Classifier.
* **Arms:** News Categories (`Entertainment`, `Education`, `Tech`, `Crime`).
* **Goal:** Learn the policy $\pi(context) \rightarrow arm$ that maximizes the expected cumulative reward (user engagement) over $T=10,000$ steps.

## 2. Model Analysis & Hyperparameter Sensitivity

We implemented and compared three distinct exploration strategies: **Epsilon-Greedy**, **Upper Confidence Bound (UCB)**, and **Softmax**.

### 2.1 Epsilon-Greedy ($\epsilon$-Greedy)
The $\epsilon$-Greedy algorithm balances exploration and exploitation by choosing a random arm with probability $\epsilon$ and the current best arm with probability $1-\epsilon$.

* **Hyperparameters Tested:** $\epsilon \in \{0.01, 0.05, 0.1\}$
* **Observations:**
    * **High $\epsilon$ (0.1):** Demonstrated the **fastest initial learning rate**. The agent quickly identified high-reward arms. However, due to the fixed 10% exploration rate, it continued to pull suboptimal arms, preventing the average reward from reaching its theoretical maximum (constant regret).
    * **Low $\epsilon$ (0.01):** Learned significantly slower. The agent spent too much time exploiting early, potentially suboptimal estimates before converging.
    * **Conclusion:** $\epsilon=0.1$ provided the best performance within the 10,000-step horizon, though $\epsilon=0.05$ offered a more stable long-term trajectory.

### 2.2 Upper Confidence Bound (UCB)
UCB follows the principle of "optimism in the face of uncertainty." It selects arms based on their estimated value plus an exploration bonus proportional to the uncertainty (confidence interval).

* **Hyperparameters Tested:** $c \in \{0.5, 1.0, 2.0\}$
* **Observations:**
    * **Low $c$ (0.5):** Behaved almost greedily. It locked onto the optimal arm very quickly but risked getting stuck in local optima if the initial samples were noisy.
    * **High $c$ (2.0):** Explored aggressively. The confidence bounds remained wide for longer, causing the average reward to fluctuate before stabilizing.
    * **Stability:** UCB demonstrated more stability than Epsilon-Greedy. As the number of samples ($N$) increased, the exploration term naturally decayed, allowing the agent to transition to full exploitation without manual tuning.

### 2.3 Softmax
Softmax selects arms probabilistically based on their estimated values, using a Boltzmann distribution controlled by temperature $\tau$.

* **Hyperparameters Tested:** $\tau \in \{0.5, 1.0, 2.0\}$
* **Observations:**
    * **High $\tau$ (2.0):** The probability distribution became nearly uniform, resulting in essentially random selection and poor performance.
    * **Low $\tau$ (0.5):** The distribution peaked sharply at the highest estimated value, acting effectively like a greedy algorithm.
    * **Sensitivity:** Softmax was highly sensitive to the temperature parameter. Unlike UCB, which adapts to variance, Softmax required precise tuning of $\tau$ relative to the scale of the rewards.

## 3. Comparative Performance

Comparing the three algorithms across the user contexts:

1.  **Convergence Speed:**
    * **Winner:** **UCB ($c=0.5$ or $1.0$)**.
    * **Analysis:** UCB identified the optimal arm efficiently by explicitly targeting under-explored options. Epsilon-Greedy ($\epsilon=0.1$) also converged quickly but with higher variance.

2.  **Average Reward (Stability):**
    * **Winner:** **UCB**.
    * **Analysis:** Epsilon-Greedy never stops exploring, leading to a permanent "regret gap." UCB's exploration bonus decays over time, allowing it to achieve a higher final average reward as it converges to the optimal policy.

3.  **Overall Best Model:**
    * **Conclusion:** The **Upper Confidence Bound (UCB)** model proved to be the most robust. It outperformed Epsilon-Greedy in stability and Softmax in ease of tuning.

## 4. Learned Policy
The Contextual Bandit successfully learned to map specific user demographics to their preferred content types. Based on the final Q-values, the system identified distinct preferences for each user context, confirming that the system successfully leveraged the "Context" (User ID) to maximize engagement.






































# Student Submission Checklist (Lab 3)










Before submitting your Lab 3 assignment, ensure that **all items below are completed**. Submissions that do not follow this checklist may receive partial or no credit.

---

## 🔹 Repository and Branching

* [ ] The repository is correctly created on GitHub.
* [ ] All work is committed to **exactly one branch** named
  `firstname_U20230xxx`.
* [ ] **No work is pushed to `master`**.
* [ ] The correct branch is pushed to GitHub.

---

## 🔹 Notebook Submission

* [ ] Exactly **one** Jupyter Notebook (`.ipynb`) is submitted.
* [ ] The notebook is placed at the **root of the repository**.
* [ ] The notebook is named **exactly**:
  `lab3_results_<roll_number>.ipynb`.
* [ ] The notebook runs **top to bottom without errors**.
* [ ] All outputs (plots, tables, metrics) are visible in the notebook.

---

## 🔹 Sampler Usage

* [ ] The provided `sampler` package is used **without modification**.
* [ ] The sampler is initialized using your correct roll number `i`.
* [ ] Rewards are obtained **only** via `sampler.sample(j)`.
* [ ] No hard-coded or synthetic rewards are used.

---

## 🔹 Contextual Bandit Implementation

* [ ] User category is treated as the **context**.
* [ ] News category is treated as the **bandit arm**.
* [ ] The arm index mapping follows the specification in the lab handout.
* [ ] All three algorithms are implemented:

  * Epsilon-Greedy
  * Upper Confidence Bound (UCB)
  * SoftMax

---

## 🔹 Evaluation and Plots

* [ ] Classification accuracy is reported on `test_users.csv`.
* [ ] Reinforcement learning simulation is run for **T = 10,000 steps**.
* [ ] Plots include:

  * Average Reward vs. Time (per context)
  * Hyperparameter comparison plots
* [ ] All plots have labeled axes, legends, and titles.

---

## 🔹 README.md Requirements

* [ ] README.md is present at the repository root.
* [ ] It explains the overall approach and design decisions.
* [ ] It summarizes key results and observations.
* [ ] It includes clear instructions to reproduce the experiments.
* [ ] All external references (if any) are properly cited.

---

## Important Note

> Submissions that do not follow the specified branch name, notebook naming convention, or sampler usage rules may not be evaluated.
