# Playfair密码

## 算法过程

# Vigenere 维吉尼亚密码

# Nihilist密码

# Hill密码

## 算法过程

### 加密

即利用一个矩阵作为key值，并且利用字母与数字之间的转化，进行计算与代换。把每个明文转为数字，然后利用key进行行变化，得到密文的数字列向量，然后转化为字母，即得到密码。

代码

```python
def encrypt(cipher, key):
    out = ''
    ciper_arr = []
    i= 0
    while i<len(cipher):
        temp = ord(cipher[i]) - ord('a')
        if i%3 == 0: ciper_arr.append([temp])
        else: ciper_arr[-1].append(temp)
        i+=1
    ciper_np_arr = np.array(ciper_arr)
    ciper_np_arr = ciper_np_arr.T
    outcome = key @ ciper_np_arr
    outcome = outcome % 26
    outcome = outcome.T
    for i in outcome:
        for j in i:
            out += chr(j + ord('a'))
    return out
```



解密

  对于key进行取逆，得到逆矩阵，由于是模26的矩阵的逆矩阵，假设矩阵的行列式的值为$$det(key)$$,取逆$$det_{invert} * det = 1 mod 26$$,实际的$$keynow =det_{invert} * key^{*}$$, 即在原$$key^{-1}$$的基础上，$$key_{now} = key^{-1}*det(key)*det_{invert}$$。

  由与矩阵求行列式的值时，可能会产生小数，需要四舍五入$$round()$$, 然后得到的矩阵$$key_{now}$$会是小数据，然后需要在取值时，四舍五入之后再进行转换为字符。

代码

```python
def decrypt(mp, key):
    out1 = ''
    mp_arr = []
    key_avert = np.linalg.inv(key)
    cond_data = round(np.linalg.det(key))
    cond_data1 = pow(cond_data, -1, 26)
    key_avert = key_avert*cond_data1 * cond_data% 26
    i = 0
    while i<len(mp):
        temp = ord(mp[i]) - ord('a')
        if i%3 == 0: mp_arr.append([temp])
        else: mp_arr[-1].append(temp)
        i+=1
    mp_np_arr = np.array(mp_arr)
    mp_np_arr = mp_np_arr.T
    outcome = key_avert@mp_np_arr % 26
    outcome = outcome.T
    for i in outcome:
        for j in i:
            out1 += chr(int(j) + ord('a'))
    return out1
```



## 完整测试代码

```python
import numpy as np

# 3x3矩阵的乘法
def decrypt(mp, key):
    out1 = ''
    mp_arr = []
    key_avert = np.linalg.inv(key)
    cond_data = round(np.linalg.det(key))
    cond_data1 = pow(cond_data, -1, 26)
    key_avert = key_avert*cond_data1 * cond_data% 26
    i = 0
    while i<len(mp):
        temp = ord(mp[i]) - ord('a')
        if i%3 == 0: mp_arr.append([temp])
        else: mp_arr[-1].append(temp)
        i+=1
    mp_np_arr = np.array(mp_arr)
    mp_np_arr = mp_np_arr.T
    outcome = key_avert@mp_np_arr % 26
    outcome = outcome.T
    for i in outcome:
        for j in i:
            out1 += chr(int(j+0.5) + ord('a'))
    return out1

def encrypt(cipher, key):
    out = ''
    ciper_arr = []
    i= 0
    while i<len(cipher):
        temp = ord(cipher[i]) - ord('a')
        if i%3 == 0: ciper_arr.append([temp])
        else: ciper_arr[-1].append(temp)
        i+=1
    ciper_np_arr = np.array(ciper_arr)
    ciper_np_arr = ciper_np_arr.T
    outcome = key @ ciper_np_arr
    outcome = outcome % 26
    outcome = outcome.T
    for i in outcome:
        for j in i:
            out += chr(j + ord('a'))
    return out


if __name__ == '__main__':
    m = "overthehillx"
    c = 'wjamdbkdeibr'
    k = np.array([[1, 4, 7],[2, 5, 8],[3, 6, 10]])
    c1 = encrypt(m, k)
    print(c1)
    m1 = decrypt(c1, k)
    print(m1)

```



# AutokeyCipher 自动密钥密码