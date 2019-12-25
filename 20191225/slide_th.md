<!-- slide -->
# 20191225勉強会

<!-- slide -->
### モデルフリーな制御

* 価値ベース($Q_t(s,a)$を直接推定)
  * 価値関数を学習
  * 方策改善は $\epsilon$-greedy法などで間接的に実施
    * SARSA
    * Q学習
* 方策ベース($\pi(s|a)$を直接推定)
  * 価値関数の推定はしない
  * 方策勾配法で方策を直接学習
* 価値ベースと方策ベースのハイブリッド
  * Actor-Critic法

<!-- slide -->
### 方策ベース法の改善点
* 方策内のパラメータ更新につかう、行動価値関数$q_{\pi}(s,a)$を、期待収益$G_t$で置き換えると分散が大きい
    * $\theta_{t+1} = \theta_{t} + \alpha \nabla_{\theta}J(\theta_t)$
    * $J(\theta) = v_{\pi}(s_0) \equiv E[ G_t |S_t = s_0]$
    * $\nabla_{\theta}J(\theta_t) = E_{\pi}[((\nabla_{\theta}\log \pi(a|s,\theta))q_{\pi}(s,a))]$    
$\Rightarrow$ 行動価値関数/価値関数もパラメータ付した関数で近似

<!-- slide -->
### Actor-Critic
* 2種類のパラメータセット$(\omega, \theta)$を更新しながら学習する
    * Critic :  行動価値関数$q_{\omega}(s,a)$/価値関数$V_{\omega}(s)$を$\omega$でパラメータ付し、このパラメータを更新する
    * Actor : 方策のパラメータ$\theta$をCriticをつかって勾配によって更新する
* 更新順序は以下のとおり
    * $S_t$ $\rightarrow$ $\pi(a|s,\theta)$ $\rightarrow$ $A_t$ $\rightarrow$ $(S_{t+1}, R_{t+1})$ $\rightarrow$ $V_{{\omega}^{\prime}}(s)$ $\rightarrow$  $\pi(a|s,\theta^{\prime})$
<!-- slide -->
### Actorのモデル化の例
* ギブス分布
    $\pi(a|s, \theta) = \frac{\exp(\theta \cdot \xi(s,a))}{\sum_{a^{\prime}}\exp(\theta \cdot \xi(s, a^{\prime}))}$
    ここで$\xi$は自分で決める特徴量
* ガウス分布
    $\pi(a|s, \theta) \propto$
     $\frac{1}{\sqrt{\det\Sigma_{\theta}(s)}} \exp ( -\frac{1}{2}(a - \mu_{\theta}(s))^{T}\Sigma_{\theta}(s)^{-1}(a - \mu_{\theta}(s))$
* RNN/LSTM 

<!-- slide -->
### Criticのモデル化と更新
* 関数系$V_{\omega}(s)$は自分で適切なものを選ぶ必要あり。4章ではNNをつかった近似を採用
* $V_{\omega}(s)$の更新には2.3章で扱った手法がいずれも適用可
* TD(0)を使った場合の更新式は
    * $\delta_{t+1}(\omega) = R_{t+1} + \gamma V_\omega(S_{t+1}) - V_{\omega}(S_t)$
    * $\mathcal{L}_{critic}(\omega) = \sum_{t=0}^{T -1} |\delta_{t+1}(\omega)|^2$を最小化する$\omega$

<!-- slide -->
### Actorの更新
* Actorの更新には、方策勾配法を使用
* TD(0)をCriticに使用した場合、Actorの損失関数がTD誤差を用いて以下のように書ける
$\mathcal{L}_{actor} \equiv -J(\theta) \approx - \frac{1}{T}\sum_{t=0}^{T-1}(\log(\pi(A_t|S_t, \theta)))\delta_{t+1}(\omega)$
* n-step TDやTD($\lambda$)誤差の場合への拡張も可能。4.3にて扱う予定
