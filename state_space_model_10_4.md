---
marp: true
---

# 補講：Stanの基本

教科書ではほぼ説明がないまま、唐突にStanコードが登場する。
教科書を理解するために必要な程度の、ごくごく基本的なことだけを述べる。
といっても、この程度の理解でも結構なことができるはず。
どんどん書いてみるのが吉。

---

## Stanのデータ型

- `real`：浮動小数点値（C++の`double`）
- `int`：整数値（C++の`int`）
- `int[N]`：`int`の1次元配列（C++の`std::vector<int>`）
- `vector`：`real`の列ベクトル（C++の`Eigen::Vector`）
- `matrix`：`real`の行列（C++の`Eigen::Matrix`）
- `cov_matrix`：対称正定値行列
- `corr_matrix`：対角成分が1の対称正定値行列

注：Stanコード上はインデックスが1始まり（Rと同じ）

---

## Stanのブロック

Stanのブロックで必須なのは次の3つ。
他にもある（全部で7つある）が必要になったときに覚えればよい。

- `data`：外部から入力される値
- `parameters`：Stanで推定（サンプリング）されるパラメータ
- `model`：確率モデル（事前分布含む）

注１：教科書10.5節で`transformed parameters`ブロックが登場する。
注２：`parameters`ブロックで`int`は使えない。

---

## `model`ブロックの書き方（１）

尤度と事前分布を記述することになる。
一番楽なのはチルダを使う方法（sampling statement form）。

```Stan
  y[t] ~ normal(mu, sigma);
```

上のように書けば、`y[t]`が平均`mu`、標準偏差`sigma`の正規分布から生成されることを意味する。

注：分布はいろいろある。`binomial`、`binomial_logit`、`bernoulli`、`bernoulli_logit`、`lognormal`、`beta`、`gamma`、`cauchy`、`poisson`、`poisson_log`、などなど。

---

## `model`ブロックの書き方（２）

`target`を使う方法。

```Stan
  target += normal_lpdf(y[t] | mu, sigma);
```

上のように書けば、`target`（内部では`lp__`）に対数尤度`log p(y[t]|mu, sigma)`を足すことを意味する（explicit increment form）。
基本はチルダで書けばよいが、こちらが必要になる局面も稀にある。

Stan内部では尤度も事前分布も対数を取って加算して、（定数分ずれている）対数事後密度`lp__`を計算している、ということは知っておくべし。

注１：lpdfはlog probability density function（対数確率密度関数）の意味
注２：MCMCでは周辺尤度は計算せずに尤度×事前分布に比例するようにサンプリング

---

## 呼び出し側の基本

R側では`sampling`関数を使う。
収束しないときは`iter`や`warmup`を増やすと解決する・・・こともある。

- `iter`：サンプリング数（ウォームアップ含む）。デフォルトは2000。
- `warmup`：ウォームアップ（バーンイン）期間。デフォルトは`iter`の半分。簡単なモデルならば、100～500程度に減らしても大丈夫なことが多い。
- `chain`：マルコフ連鎖の数。デフォルトは4。コア数が多いとき、収束性が気になるとき（Rhatを精緻に知りたいとき）は増やしてみる。
- `init`：パラメータの初期値。デフォルトはランダム。トレースプロットで`lp__`の低いchainが局所最適に嵌ってるときは、設定すると抜け出せることもある。
- `seed`：再現性を維持するために常に指定することを推奨。

---

## 結果の見方（１）

以下の関数は`ggplot`でプロット（可視化）してくれる。
上4つはMCMCの収束を確認するために使う。

- `stan_rhat`：Rhatのヒストグラム（全部1.10以下に収まっていればOK）
- `stan_trace`：トレースプロット（混ざっていればOK）
- `stan_dens`：chainごとの事後分布の密度関数（ほぼ同じならOK）
- `stan_ac`：自己相関（自己相関が無ければOK）
- `stan_hist`：事後分布のヒストグラム（収束確認後に使うこと）
- `stan_plot`：事後分布を１枚に要約（収束確認後に使うこと）

`$data`を付けるとグラフではなくてデータが取れる。

---

## 結果の見方（２）

`rstan::extract`でパラメータごとの全サンプルを抽出する。
`extract`関数は`tidyr`など他のパッケージにもあるので、衝突回避のために`rstan::`を付けることを推奨。
次のコードは95%ベイズ信頼区間を計算するコード。

```R
fit <- sampling(...)
ms <- rstan::extract(fit)
quantile(ms$x, props=c(0.025, 0.975))
```

注：`ms`は名前付きリスト、`ms$x`はMCMCサンプルの長さの1次元配列。

---

## 結果の見方（３）

ベイズ信頼区間が欲しいだけなら`rstan::summary`を使うのが楽。

```R
fit <- sampling(...)
s <- rstan::summary(fit, probs = c(0.025, 0.975))
s$summary  # all chains merged
s$c_summary  # individual chains
```

パラメータごとのRhatが欲しければ（`stan_rhat(fit)$data`でもよい）
```R
s$summary[,"Rhat"]
```

事後分布での期待値が欲しければ（`get_posterior_mean(fit)`でもよい）
```
s$summary[,"mean"]
```

---

## MCMCが収束しないときはどうするの？

以下を参照せよ。
演者の力量を超えるため多くを語らないことにする。

- [松浦「StanとRでベイズ統計モデリング」](https://www.amazon.co.jp/dp/4320112423)の10章
- Stan users guideの[Problematic Posteriors](https://mc-stan.org/docs/2_27/stan-users-guide/problematic-posteriors-chapter.html)
- Stan Wikiの[Prior-Choice-Recommendations](https://github.com/stan-dev/stan/wiki/Prior-Choice-Recommendations)
---

# 10.4 一般状態空間モデルにおける推定例

10.3節の例はパラメータが既知（外から与える）の線形ガウス型だったのでカルマンフィルタでも解ける問題だったが、
本節ではパラメータが未知の線形ガウス型（カルマンフィルタで解けない）を考える。

---

## 状態とパラメータを同時推定する

10.3節ではパラメータを外から与えたかったので`data`ブロックに記述した。
状態と同時推定したい場合は`parameters`ブロックに推定したいパラメータを移動する。
Stanでは事前分布を明示的に書かなければ、無情報事前分布となる。

```Stan
parameters{
  real           x0;       // 状態[0]
  vector[t_max]   x;       // 状態[1:t_max]

  cov_matrix[1]   W;       // 状態雑音の分散
  cov_matrix[1]   V;       // 観測雑音の分散
}
```

---

## 呼び出し側コード（R側）

`data`から`W`や`V`を除去して、`pars`に追加するだけ。

```R
# 平滑化：実行（サンプリング）
fit_stan <- sampling(object = stan_mod_out,
                     data = list(t_max = t_max, y = y, 
                                 m0 = mod$m0, C0 = mod$C0),
                     pars = c("W", "V", "x"),
                     seed = 123
            )
```

---

## 結果（`fit_stan`）を眺めてみよう

今回のデータは人工的に作ったデータなので答え（パラメータの真値）が分かっている。
真値は`W`が1.0、`V`が2.0であるのに対して、ベイズ推定の期待値は0.97、2.07であり、良好であろう。
ただ10.3節と比べると`n_eff`や`Rhat`は少しだけ悪くなっているのがわかるだろう。
これは推定の困難さが増していることを意味している。

演者注：`n_eff`（実効的サンプル数）が100未満になってしまうときは、推定された分布や統計量を信頼することは難しいだろう（`iter`を増やしたほうが良いだろう）。

---

## トレースプロットを眺めてみよう

`W`と`V`のトレースプロットを目視で見る分には特段の問題はなさそう。

演者注：`stan_trace(fit_stan, pars = c("W", "V"))`などとするとトレースプロットが見える。

---

## パラメータ既知のカルマン平滑化と比較してみよう

教科書の図10.5で、ほぼ一致することを確認。
ただし、この例では一致しているが、原理的にはすべての例で一致するとは限らないことに注意しよう。

演者注：現実の問題ではパラメータは未知である。パラメータも含めてベイズ推定するMCMCと、（パラメータ未知で）パラメータを最尤推定するカルマン平滑化とは本質的に異なるもの、と考えておいたほうが良いだろう（ベイズ推定と最尤推定の違いに過ぎないが）。
