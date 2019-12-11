<!-- slide -->
# 20191213勉強会

<!-- slide -->
### モデルフリーな制御
* モデルフリーな状況では、$p(s^{\prime}|s,a)$が分からない
* 価値関数の推定値$V_(s)$ から 行動価値関数の推定値$Q_t(s,a)$を求めることが難しい
* 方策改善には以下のアプローチがある
  * $Q_t(s,a)$を直接推定し、$\epsilon$-greedy法で方策改善
  * $\pi(s|a)$を直接推定して、方策を改善する

<!-- slide -->
### 方策改善の主なアプローチ
* 価値ベース($Q_t(s,a)$を直接推定)
  * SARSA
  * Q学習
* 方策ベース($\pi(s|a)$を直接推定)
  * 方策勾配法の適用
* 価値ベースと方策ベースのハイブリッド
  * Actor-Critic法

<!-- slide -->
### Q関数による制御
* Q関数が与えられた時の方策改善方法はどうやるのか？
  * 背景：
    * Q関数を完全に推定することは不可能
    * 今のQ関数で最適な行動とることが真に最適とは限らない(explorationの必要)
       $a^{\ast} \neq \arg \max Q(a) $
    * しかし、いまのQ関数を完全に無視して行動をとるのも収益最大化のめには適切ではない(exploitationの必要)
  * 課題:
    * explorationとexploitationのバランスをとる必要がある

<!-- slide -->
### ２つの探索、活用アルゴリズム
* $\epsilon$-greedy法
    * 確率$\epsilon$で無作為に行動を選択し、$1-\epsilon$でQ関数に基づいて行動を選択する
    * Mハンドルのバンディット問題のときは
    $\pi(a|s) = \begin{cases} (1 - \epsilon) / |\mathcal{A}^{\ast}(s)| + \epsilon/M  \hspace{4pt}  a \in |\mathcal{A}^{\ast}(s)|  \\ \epsilon/M \hspace{4pt}  otherwise  \end{cases} $
    $|\mathcal{A}^{\ast}(s)|  =\{ a_{\ast} \hspace{4pt} s.t. \hspace{4pt} a_{\ast} = \arg\max Q(s,a) \}$
* Boltzman exploration
    * 方策を下記に基づいて更新する
    $\pi(a|s) = \frac{\exp(\beta Q(s,a))}{\sum_{a^{\prime}} \exp(\beta Q(s,a^{\prime}))}$
    * $\beta$ $\rightarrow$ $\infty$ で$\epsilon$-greedy法に一致する

<!-- slide -->
### 価値関数推定のアプローチ

<!-- slide -->
### SARSA
* 更新アルゴリズムは以下のとおり
    * $\delta_{t + 1} = R_{t + 1} + \gamma Q_t(S_{t+1}, A_{t+1}) -  Q_t(S_{t}, A_{t})$
    * $Q_{t+1}(s,a) = Q_{t}(s,a) +  \alpha \delta_{t + 1} \bold{1}(S_{t} = s, A_{t} = a)$
* 導出については$V_t(s)$の更新式を導出したのと同じ議論を
    * $Q_{t+1}(s,a) = \frac{1}{N_{t+1}(s,a)}\sum_{k = 0}^{t} G_k \bold{1}(S_{t} = s, A_{t} = a)$
    * $N_{t+1}(s,a) = \sum_{k = 0}^{t} \bold{1}(S_{t} = s, A_{t} = a)$
について行えばいい。

<!-- slide -->
### SARSAの一般化
* $V_t(s)$の議論と同様にしてn-step学習、$TD(\lambda)$法に拡張可能
* SARSA$(\lambda)$の更新式は下記で与えられる
    * $Q_{t+1}(s,a) = Q_{t}(s,a) +  \alpha \delta_{t + 1} E_{t+1}(s, a)$
    * $E_{t+1}(s, a) = \lambda \gamma E_{t+1}(s, a) +  \bold{1}(S_{t} = s, A_{t} = a)$
* SARSAは実際の状態、行動のサンプル列をそのまま更新に使用しているのでPolicy-On学習といわれる

<!-- slide -->
### Q学習:Policy-Off学習


<!-- slide -->
### Actor-Critic法
* Actorのモデル化
* Criticのモデル化
