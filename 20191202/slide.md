<!-- slide -->
# 20191202勉強会

<!-- slide -->
## Bellman方程式の解法(2)

<!-- slide -->
### 強化学習でのタスク

* 価値関数$v(s|\pi)$を最大化するような方策$\pi$を求める

* 環境モデルのMarkov Dicision Process $p(s', r | s, a)$が与えられた場合は動的計画法によって$\pi$を求めることができる
  * Policy iteration
  * Value iteration

<!-- slide -->
### 環境モデルが使えない場合

* 環境モデルが明らかでない
* 環境モデルが大きすぎて直接計算が難しいときは?
  * モンテカルロ法
  * TD学習 (Temporal Difference)

<!-- slide -->
## モンテカルロ法

<!-- slide -->
### 強化学習でのモンテカルロ法

* $v_{\pi}(s) = E(G_t|S_t = s)$を計算したい
  * 期待値計算にモンテカルロを使う!
* エピソードを一つ作ると, その途中から始めたパスも一つのエピソード
⇒これも利用する
  * 各初期状態$s$に対しての条件付期待値を計算したい
  * ので、途中経過も利用できる
  * 状態が多すぎると必要なエピソード多くなる?

<!-- slide -->
### アルゴリズムイメージ

1. エピソード $\{S_t, R_t |t=0, 1, \cdots, T \}$を1つ生成する
1. 各時刻$t$での$G_t = \sum_{l=1} \gamma^{l-1}R_{t+l}
$を計算
1. 時刻$t+1$において ($t \geq 0$)
  $$ \begin{aligned}
  V_{t+1}(s) &= \frac{1}{N_{t+1}(s)} \sum_{k=0}^{t} G_k \chi(S_k = s) \\
  N_{t+1}(s) &= \sum_{k=0}^{t} \chi(S_k = s)
  \end{aligned}$$を計算
1. 次のエピソードへ?

<!-- slide -->
### 漸化式で書き換えると
  $$ \begin{aligned}
  V_{t+1}(s) &= V_t(s) + \frac{1}{N_{t+1}(s)} (G_t - V_t(S_t)) \chi(S_k = s) \\
  N_{t+1}(s) &= N_t(s) + \chi(S_t = s)
  \end{aligned}$$

* これを元に, $\frac{1}{N_{t+1}(s)}$のところを$\alpha_t$とした漸化式を使ってもOK
  * Robbins-Monro条件を満たす限りは収束性が保証される
  * 実用的には小さな定数$\alpha_t = \alpha$でもOK

<!-- slide -->
### モンテカルロ法の欠点

* オンライン学習できない
  * $G_t$の計算にエピソード終点まで用いるから
* 各エピソードが有限の長さ(エピソディック)なMDPでないといけない
* バイアスは小さいがバリアンスが大きい

**TD学習でこれをクリアできる**

<!-- slide -->
## TD学習

<!-- slide -->
### アイデア

* Bellman方程式
  $$ v_{\pi}(s) = \sum_{a} \pi(a|s) \sum_{s'}\sum_{r}p(s', r | s, a) (r+\gamma v_{\pi}(s'))$$
* ここの $r+\gamma v_{\pi}(s')$ は現状態$s$においての次の状態$s'$に対する$v_{\pi}$の推定値と見なせそう
  ($G_t = R_{t+1} + \gamma G_{t+1}$に注意!!)
* $G_t \approx R_{t+1} + \gamma V_t(S_{t+1})$としてモンテカルロ更新式を書き直す

<!-- slide -->
### TD学習の更新式

* 更新式

  $$ \begin{aligned}
  V_{t+1}(s) &= V_t(s) + \\ &\alpha (R_{t+1} + \gamma V_t(S_{t+1}) - V_t(S_t)) \chi(S_k = s)
  \end{aligned}$$

* TD誤差$\delta_{t+1}$を用いると

  $$ \begin{aligned}
  \delta_{t+1} &= R_{t+1} + \gamma V_t(S_{t+1}) - V_t(S_t) \\
  V_{t+1}(s) &= V_t(s) + \alpha \delta_{t+1} \chi(S_t = s)
  \end{aligned}$$

* 時刻$t$で分かっている情報($V_t$, $S_t$, $S_{t+1}$, $R_{t+1}$)だけで計算可. つまりオンライン化ができる

<!-- slide -->
### TD学習の欠点

* 1ステップ先の情報だけで更新してしまう
  * バリアンスは小さくなる
  * 代わりにバイアスが大きくなる

**$n$-ステップTD学習/TD($\lambda$)法で解消したい**

<!-- slide -->
## $n$-ステップTD学習とTD($\lambda$)法

<!-- slide -->
### アイデア

* モンテカルロ法での更新式(再掲)

  $$V_{t+1}(s) = V_t(s) + \alpha_t (G_t - V_t(S_t)) \chi(S_k = s)$$

  * $G_t = \sum_{l=1} \gamma^{l-1}R_{t+l}$は終端状態まで使って計算

* TD学習
  $G_t^{(1)} = R_{t+1} + \gamma V_t(S_{t+1})$を使って$G_t^{(1)} \approx G_t$と書き換える

* $n$-ステップTD・TD($\lambda$)法
  $G_t$を別の形で近似する

<!-- slide -->
### 各方法での更新式

* $n$-ステップTD学習
  $$G_t^{(n)} = R_{t+1} + \gamma R_{t+2} + \cdots + \gamma^{n-1}R_{t+n} + \gamma^{n} V_t(S_{t+n})$$
  * $n \to \infty$がモンテカルロに対応

* TD($\lambda$)法
  $$G_t^{\lambda} = (1-\lambda) \sum_{n=1}^{T-t} \lambda^{n-1}G_t^{(n)} + \lambda^{T-t} G_t$$
  * $\lambda = 0$は1-ステップTD
  * $\lambda = 1$はモンテカルロ

<!-- slide -->
### まだ問題あり

* どちらも将来複数ステップが必要なのでオンライン学習できない

* 近似的にオンライン化できないか?

<!-- slide -->
## TD($\lambda$)法のオンライン学習

<!-- slide -->
### n-ステップ誤差の分解

$$ \begin{aligned}
G_t^{(n)} - V_t(S_t) &= \sum_{k=t}^{t+n-1} \gamma^{k-t}R_{k+1} + \gamma^n V_t(S_{t+n}) - V_t(S_t) \\
\delta_{t+1} &= R_{t+1} + \gamma V_t(S_{t+1}) - V_t(S_t)
\end{aligned}$$
を組み合わせると...

<!-- slide -->
### n-ステップ誤差の分解(2)

$$ \begin{aligned}
G_t^{(n)} - V_t(S_t) &=
\\ &\sum_{k=t}^{t+n-1} \gamma^{k-t} \delta_{k+1}
\\ &- \sum_{k=t}^{t+n-2} \gamma^{k-t+1}\Big(V_k(S_{k+1}) - V_{k+1}(S_{k+1}) \Big)
\\ &-\gamma^{n} \Big( V_{t+n-1}(S_{t+n}) - V_t(S_{t+n})\Big)
\end{aligned}
$$

* 第2, 3項は価値関数の差分なので高々$O(\alpha)$の項

<!-- slide -->
### TD($\lambda$)誤差の分解

* 先の結果とTD($\lambda$)の定義式を合わせると

  $$ G_t^{\lambda} - V_t(S_t) = \sum_{k=t}^{T-1}(\lambda \gamma)^{k-t} \delta_{k+1} + O(\alpha)$$

<!-- slide -->
* $\chi(S_t = s)$をかけて和をとると

  $$ \begin{aligned}
  \sum_{t=0}^{T-1} \Big( G_t^{\lambda} - V_t(S_t) \Big) \chi(S_t = s) = \\
  \sum_{k=0}^{T-1} \delta_{k+1} E_{k+1}(s)+ O(\alpha) \end{aligned}
  $$

* ここで$E_{k+1}(s)$は適格度トレースと呼ばれる量

  $$ E_{k+1}(s) = \sum_{t=0}^{k} (\lambda \gamma)^{k-t} \chi(S_t = s) $$

<!-- slide -->
### 前方観測と後方観測

* TD($\lambda$)の誤差評価

  $$ \sum_{t=0}^{T-1} \Big( G_t^{\lambda} - V_t(S_t) \Big) \chi(S_t = s) \approx \sum_{k=0}^{T-1} \delta_{k+1} E_{k+1}(s) $$

* 左辺の添え字$t$
  各$t$で$t$**以降**の和を考える (前方)

* 右辺の添え字$k$
  各$k$で$k$**以前**の和を考える (後方)
  => 過去の情報だけで計算できる!!

<!-- slide -->
### TD($\lambda$)法のオンライン学習更新式

* 各エピソード毎での誤差積み上がりが変わらないようにする

* というアイデアの下, 更新式は

$$ \begin{aligned}
V_{t+1}(s) &= V_t(s) + \alpha \delta_{t+1} E_{t+1}(s) \\
E_{t+1}(s) &= \lambda \gamma E_t(s) + \chi(S_t = s)
\end{aligned} $$

<!-- slide -->
## まとめ

<!-- slide -->
### 更新式

  $$V_{t+1}(s) = V_t(s) + \alpha_t (\tilde{G}_t - V_t(S_t)) \chi(S_k = s)$$

### 各手法

| 手法 | $\tilde{G}_t$ |
| ------- | ------------- |
| MC | $G_t = \sum_{l=1}^{T-t} \gamma^{l-1}R_{t+l}$ |
| $1$-TD | $G_t^{(1)} = R_{t+1} + \gamma V_t(S_{t+1})$ |
| $n$-TD | $G_t^{(n)} = \sum_{l=1}^{n} \gamma^{l-1}R_{t+l} + \gamma^n V_t (S_{t+n})$ |
| TD($\lambda$) | $G_t^{\lambda} = (1-\lambda) \sum_{n=1}^{T-t} \lambda^{n-1}G_t^{(n)} + \lambda^{T-t} G_t$ |
