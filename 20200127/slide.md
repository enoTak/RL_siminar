<!-- slide -->
# 20200127勉強会

## Deep Q Network (DQN)

<!-- slide -->

### Pendulum v0で実験してみよう

|設定  |概要  |範囲  |
|---|---|---|
|状態($s$)  |振り子角度・角速度  |3-dim  |
|行動($a$)  |振り子に作用する力 |-2~2 |
|報酬($r$)  |同時刻における振り子の状態・行動から計算 | -16.2736 ~ 0|
|終了条件 |最大ステップ数に到達したか |True/False|

* 状態: $s=(\cos \theta, \sin \theta, \dot \theta)$
* 報酬: $r=\theta^2 + 0.1 \dot \theta^2 + 0.001 a^2$

<!-- slide -->
## DQNアルゴリズム

<!-- slide -->
### どんなアルゴリズム?

* Q学習 + Q関数をDNNで近似

* ここまでのQ関数: 離散インプット→テーブルで関数データを持つ
・・・連続インプット難しかった

* 画像やテキスト: 状態としては扱い難しい
→DNNにより直接入力値にできる

* 当然計算量莫大になるので工夫する

<!-- slide -->
### 工夫1: ネットワーク構造

* xxx

<!-- slide -->
### 工夫2: Experience replay

* xxx

<!-- slide -->
### 工夫3: Reward clipping

* xxx

<!-- slide -->
### 工夫4: Double network

* xxx

<!-- slide -->
### 全体でのアルゴリズム

#### 価値ベース

* 以下をループ:

  1. 今の状態から行動選択
  1. 次の状態と報酬を取得/報酬はreward clipping
  1. 経験をメモリに保存
  1. メモリからサンプリング
  1. サンプリング値を使ってネットワーク更新

<!-- slide -->
## 実装例(DQN)

<!-- slide -->
## Actor-Critic法の応用

<!-- slide -->
### Actor

* xxx

<!-- slide -->
### Critic

* xxx

<!-- slide -->
## 実装例(DQN/Actor-Critic)


Go to this URL in a browser: https://accounts.google.com/o/oauth2/auth?client_id=947318989803-6bn6qk8qdgf4n4g3pfee6491hc0brc4i.apps.googleusercontent.com&redirect_uri=urn%3aietf%3awg%3aoauth%3a2.0%3aoob&response_type=code&scope=email%20https%3a%2f%2fwww.googleapis.com%2fauth%2fdocs.test%20https%3a%2f%2fwww.googleapis.com%2fauth%2fdrive%20https%3a%2f%2fwww.googleapis.com%2fauth%2fdrive.photos.readonly%20https%3a%2f%2fwww.googleapis.com%2fauth%2fpeopleapi.readonly

Enter your authorization code:
··········
Mounted at /content/drive
