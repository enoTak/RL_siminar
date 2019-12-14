<!-- slide -->
# 20191213勉強会

<!-- slide -->
### モデルフリーな制御
* モデルフリーな状況では、$p(s^{\prime}|s,a)$が分からない
* 価値関数の推定値$V_t(s)$ から 行動価値関数の推定値$Q_t(s,a)$を求めることが難しい
* 方策改善には以下のアプローチがある
  * $Q_t(s,a)$を直接推定し、$\epsilon$-greedy法等で方策改善
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
### ご参考
* 強化学習の主要なアルゴリズム(2017まで)
https://qiita.com/sugulu/items/3c7d6cbe600d455e853b
* 今回扱うものはDeep learning登場以前のもの
* 深層学習分は4章

<!-- slide -->
### Q関数による制御
* Q関数が与えられた時の方策改善方法はどうやるのか？
  * 背景：
    * Q関数を完全に推定することは不可能
    * 今のQ関数で最適な行動とることが真に最適とは限らない(explorationの必要)
       $a^{\ast} \neq \underset{a}{\arg \max} Q(a) $
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
    * $\beta$ $\rightarrow$ $\infty$ でgreedy法に一致する

<!-- slide -->
### 価値関数推定のアプローチ
価値関数推定に使われるアイデアはTD誤差の更新方針に従って以下の２つ
* On-Policy 学習 : 未来の行動が、現在の戦略に従って選ばれると想定して学習する
    * 代表例 : SARSA
* Off-Policy 学習 : 未来の行動が、現在の戦略に関係なく常にその時に最善のものが選ばれると想定して学習する
    * 代表例 : Q-学習
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
* $V_t(s)$の議論と同様にしてn-step学習、$TD(\lambda)$法に拡張可能。n-step収益が下記で与えれられることより従う
    * $G_t^{(n)} = R_{t+1} + \gamma R_{t+2} + \cdots + \gamma^{n-1}R_{t+n} + \gamma Q_t(S_{t+n}, A_{t+n})$
* SARSA$(\lambda)$の更新式は下記で与えられる
    * $Q_{t+1}(s,a) = Q_{t}(s,a) +  \alpha \delta_{t + 1} E_{t+1}(s, a)$
    * $E_{t+1}(s, a) = \lambda \gamma E_{t+1}(s, a) +  \bold{1}(S_{t} = s, A_{t} = a)$
* SARSAは実際の状態、行動のサンプル列をそのまま更新に使用しているのでPolicy-On学習といわれる

<!-- slide -->
### Q学習
* TD誤差を、現在のQ関数についてgreedyな行動をとったとして計算する手法
* 更新アルゴリズムは以下のとおり
    * $\delta_{t + 1} = R_{t + 1} + \gamma \underset{a}{\max} Q_t(S_{t+1}, a) -  Q_t(S_{t}, A_{t})$
    * $Q_{t+1}(s,a) = Q_{t}(s,a) +  \alpha \delta_{t + 1} \bold{1}(S_{t} = s, A_{t} = a)$


<!-- slide -->
### Q学習の特徴
* 価値関数$V_t(s)$の推定の$TD(0)$と同じく、1-stepの結果の影響を強く受けるためバイアスが大きくなりがち
* Off-Policyなので、$TD(\lambda)$方への拡張は非自明
$\rightarrow$ experience replay : 過去の状態遷移$(S_t, A_t, R_t, S_{t+1})$を保存しておき、Q関数の学習にランダムに選択して使用。詳細は4.2

<!-- slide -->
### 価値ベースの手法問題点
* これまではQ関数の推定$\rightarrow$方策$\pi$の改善$\rightarrow$Q関数更新、と方策反復法を行って学習していた
* 問題点
    * 行動空間が高次元、もしくは連続の場合に$Q(s,a)$を最大化するのが難しい。すべての状態、行動空間のQ値を求めるのは不可能
    * 方策関数の選べる範囲が限られている。$\epsilon$-greedy, Boltzman exploration など、関数形が限定的

<!-- slide -->
### 方策ベースの手法
* 方策$\pi(a|s)$を直接モデル化することを考える$\rightarrow$方策ベース手法
    * 価値ベースの手法の問題点を克服
    * 本手法の問題点
        * 方策のモデル化がうまくないと十分なサンプリングが行われずに、局所解に陥りやすい
        * Q関数について最適ではないのでバリアンスが大きく収束が遅い

<!-- slide -->
### 方策ベースの手法の課題設定
* どう方策$\pi(a|s)$をモデル化するか？
$\rightarrow$ ギブス分布、ガウス分布など。NNを使えば
$\rightarrow$ NNをつかうのは４章(NNの万能近似定理より、原理的はすべての分布をカバー：ご参考　https://www.slideshare.net/trinmu/ss-161240890)
* どうやって方策を学習するか？
$\rightarrow$　方策勾配法など。



<!-- slide -->
### 方策のモデル
* Q関数の観測結果がないので$\pi$を計算することができない$\rightarrow$ 自分で関数形を選定
* 代表例
    * 離散の場合 : ギブス分布
    $\pi(a|s, \theta) = \frac{\exp(\theta \cdot \xi(s,a))}{\sum_{a^{\prime}}\exp(\theta \cdot \xi(s, a^{\prime}))}$
    ここで$\xi$は自分で決める特徴量
    * 連続の場合 : ガウス分布
    $\pi(a|s, \theta) \propto$
     $\frac{1}{\sqrt{\det\Sigma(s)}} \exp ( -\frac{1}{2}(a - \mu(s))^{T}\Sigma(s)^{-1}(a - \mu(s))$

<!-- slide -->
### 方策勾配法
* 最適方策$\pi_{\ast}$は最適な価値関数$v_{\ast}$を実現しているはず。
* よって方策パラメータ$\theta$の更新方法を以下で定める
    * $\theta_{t+1} = \theta_{t} + \alpha \nabla_{\theta}J(\theta_t)$
    * $J(\theta) = v_{\pi}(s_0) \equiv E[ G_t |S_t = s_0]$
* 勾配方策勾配法より、以下が成立している
    * $\nabla_{\theta}J(\theta_t) = E_{\pi}[((\nabla_{\theta}\log \pi(a|s,\theta))q_{\pi}(s,a))]$    

<!-- slide -->
### 方策勾配法の実装
* 実装においては方策勾配定理内のQ関数のバリアンスをおさえる工夫がいる
* 状態空間のバリアンスを吸収する方法$\rightarrow$アドバンテージ関数の導入
   