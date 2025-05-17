# 1. 低私钥指数攻击

## 攻击原理(条件)

已知RSA的$$<N, e>$$, 其中 $$e$$ 比较小，满足条件$$e < \left(\frac{1}{3}\right) \cdot N^{\frac{1}{4}}$$,即可通过连分数的计算，以良好的逼近性$$(|\frac{e}{n} - \frac{k}{d}|<\frac{1}{2d^2})$$。可以通过$$\frac{e}{n}$$的连分数展开式，得到$$d$$, 前n项的连分数的结果，分母就有一个为d, 可以通过公式验证。

# 2. Coppersmith’s Method Attack

