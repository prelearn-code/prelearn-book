# Playfair密码（替换密码）

## 算法过程

1. 选取一串英文字母，除去重复出现的字母，将剩下的字母逐个逐个加入 5 × 5 的矩阵内，剩下的空间由未加入的英文字母依 a-z 的顺序加入。注意，将 q 去除，或将 i 和 j 视作同一字。
2. 将要加密的明文分成两个一组。若组内的字母相同，将 X（或 Q）加到该组的第一个字母后，重新分组。若剩下一个字，也加入 X 。
3. 在每组中，找出两个字母在矩阵中的地方。
   - 若两个字母不同行也不同列，在矩阵中找出另外两个字母（第一个字母对应行优先），使这四个字母成为一个长方形的四个角。
   - 若两个字母同行，取这两个字母右方的字母（若字母在最右方则取最左方的字母）。
   - 若两个字母同列，取这两个字母下方的字母（若字母在最下方则取最上方的字母）。

## 例子

```python
# 密钥

k = [[P L A Y F]
     [I R E X M]
     [B C D G H]
     [K N O Q S]
     [T U V W Z]]

# 明文
m = HI DE TH EG OL DI NT HE TR EX ES TU MP
# 密文
c = BM OD ZB XD NA BE KU DM UI XM MO UV IF
```



# Polybius密码（棋盘密码）

## 算法过程

  将数字或者字母作为棋盘盘号，一般为5行5列，将明文作为输入，将行列组合作为输出，解密时反之。

## 特点

  由于是棋盘密码，行号、列号表示的字母或者数字相同，并且为5个字符，密文中只有5种字符或者数字的加密方法可以考虑，并且密文长度肯定是2的倍数。

## 例子（安恒杯 9月赛题）

### 题目

>密文：ilnllliiikkninlekile
>
>压缩包给了一行十六进制：546865206c656e677468206f66207468697320706c61696e746578743a203130
>
>请对密文解密

### 结果

```python
import itertools


# 统计字符，并得到所有可能的组合
def get_all_keys(c):
    keys = []
    out = ''
    for i in c:
        if i not in out: out+=i
    for j in itertools.permutations(out):
        keys.append(j)
    return keys

# 常规解密
# 按顺序排列a-z
def decrypt(cipher, key):
    out = ""
    i = 0

    while i < len(cipher):
        x = 0
        y = 0
        for j in range(5):
            if cipher[i] == key[j]:
                x = j
                break
        for k in range(5):
            if cipher[i+1] == key[k]:
                y = k
                break
        i+=2
        num = x*5 + y
        if num >= 10: num+=1
        out += chr(ord('a')+num)
    return out

if __name__ == '__main__':
    c = "ilnllliiikkninlekile"
    keys = get_all_keys(c)
    for key in keys:
        m = decrypt(c, key)
        if "flag" in m: print(key,m)
        if "ctf" in m: print(key,m)
    
    # 输出
    # ('l', 'i', 'n', 'k', 'e') flagishere
    # ('l', 'i', 'n', 'e', 'k') flagjxhdwd
```



# Vigenere 维吉尼亚密码

## 算法过程

 首先生成一个代换表，行号、列号为a-z, ，如下图的密码代换表，然后输入明文与密钥，对于明文进行分组，然后就以明文的字符为横坐标，密钥的字符为纵坐标，然后查表到结果即是密文。

![维吉尼亚表格](./pic/vigenere1.jpg)

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

