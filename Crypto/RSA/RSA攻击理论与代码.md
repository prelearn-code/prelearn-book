# 1. 低私钥指数攻击

## 攻击原理

已知RSA的$<N, e>$, 其中 $e$ 比较小，满足$e < \left(\frac{1}{3}\right) \cdot N^{\frac{1}{4}}$, 即可理实现恢复私钥d

$\left(\frac{1}{3}\right) \cdot N^{\frac{1}{4}}$