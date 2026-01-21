<div style="text-align:center">
  <h1>Elliptic Curve Cryptography</h1>
</div>

Việc sử dụng đường cong Elliptic (Elliptic Curve) trong mã hóa khoá công khai được đề xuất lần đầu vào năm 1985. Sau nhiều thập kỷ chống chọi với các cuộc tấn công, đường cong Elliptic bắt đầu được sử dụng rồng rãi từ năm 2005, đem lại lợi ích vượt trội so với kiểu mã hóa khóa công khai được sử dụng trước đó là RSA.

Dù chiều dài khóa của đường cong Elliptic ngắn hơn so với RSA nhưng nó mang lại hiệu quả hơn rất nhiều so với RSA có cùng hoặc dài hơn về chiều dài khóa. Một khóa ECC dài 256 bit có tính bảo mật ngang với một khóa RSA dài 3072 bit. Hơn thế nữa, một vài thao tác sử dụng khóa của ECC có thể hiệu quả hơn về cả thời gian và bộ nhớ so với RSA. Cuối cùng, vì ECC phức tạp hơn nên nó khuyến khích các nhà phát triển nên sử dụng các thư viện ECC cài đặt sẵn thay vì tự chế ra một hệ mã ECC riêng.

Sau đây là một vài lý thuyết cơ bản về **ECC - Elliptic Curve In Cryptography**

# I. Elliptic Curve trong trường số thực
## 1. Định nghĩa 
Tập hợp tất cả các điểm trong không gian $\mathbb{R}^2$ thỏa mãn phương trình có dạng : 
$$
y^2=x^3+Ax + B
$$

được gọi là phương trình đường cong Elliptic. Phương trình loại này được gọi là phương trình **Weierstrass** dựa theo tên của một nhà toán học, người đã nghiên cứu chúng rộng rãi trong thế kỷ XIX.

Một số ví dụ về Elliptic Curve : 
$$
(E_1):\ y^2 = x^3 - 3x + 3 \quad \text{hoặc} \quad (E_2):\ y^2 = x^3 - 6x + 5
$$

Đồ thị minh họa : 

$(E_1):\ y^2 = x^3 - 3x + 3$
![{9E1B4EF4-658A-48E5-8F6A-9D677133DC29}](https://hackmd.io/_uploads/rkZKTY1CJe.png)

$(E_2):\ y^2 = x^3 - 6x + 5$
![{270634BE-B314-43DD-B2EC-50F3D94092AC}](https://hackmd.io/_uploads/ByeyAYJRyl.png)

Ta có thể vẽ được các đồ thị này bằng SageMath như sau : 
```python=
# (E) : y^2 = x^3 - 3x + 3
E = EllipticCurve(RR, [-3, 3])  
plot_E = E.plot(xmin=-10, xmax=10, ymin=-10, ymax=10)
plot_E.show()
```
![{D026CE2D-F73F-4210-9EA6-EA23BE2AB6D6}](https://hackmd.io/_uploads/Hy76LB7Wxl.png)


## 2. Phép toán cộng trên Elliptic Curve
Một đặc điểm tuyệt vời của Elliptic Curve là có một cách tự nhiên để lấy hai điểm bất kì trên Elliptic Curve và **"cộng"** chúng để tạo ra điểm thứ ba. Phép **"cộng"** ở đây là một phép toán đặc biệt vì chúng ta đang đề cập đến một phép toán kết hợp hai điểm theo cách tương tự như phép cộng thông thường có một số tính chất như : giao hoán, kết hợp, và có phần tử đơn vị nhưng nó sẽ rất khác. Cách tự nhiên nhất để mô tả phép **"cộng"** này trên các Elliptic là **sử dụng hình học**.

Trước hết ta sẽ có một số định nghĩa như sau : 
- Elliptic Curve sẽ có một điểm đặc biệt gọi là **điểm "vô cùng"** và được ký hiệu là $\mathcal{O}$.
- Điểm $\mathcal{O}$ cũng có thể được xem như là thành phần đơn vị với : 
$$
\forall P\in E,\hspace{2mm}P+\mathcal{O}=\mathcal{O}+P=P
$$
- Trong phép toán cộng, phần tử nghịch đảo của điểm $P$ thuộc $E$, kí hiệu là $-P$, là điểm đối xứng của $P$ qua trục hoành **Ox**.

Đó là những điều cơ bản mà ta cần biết, bây giờ đến với phép toán cộng trên Elliptic Curve: 
- Cho $P,Q$ là hai điểm bất kì trên Elliptic Curve $E$. Vẽ một đường thẳng $L$ đi qua hai điểm $P,Q$. Khi đó đường thẳng $L$ sẽ cắt $E$ tại hai điểm là $P,Q$ và một điểm khác là $R$.
- Hiển nhiên ta có thể thấy được một điều rằng : Elliptic Curve $E$ là một đồ thị đối xứng với nhau qua trục **Ox**. Vì vậy khi này, ta sẽ lấy đối xứng của điểm $R$ qua trục **Ox** thành điểm $R'$ và tất nhiên điểm $R'$ này cũng thuộc $E$. 
- Khi đó, điểm $R'$ này chính là kết quả của phép **"cộng"** giữa hai điểm $P$ và $Q$, được kí hiệu là : 
$$
P\oplus Q= R'
$$
>Lưu ý rằng : Dấu $\oplus$ này không mang ý nghĩa của phép toán XOR

Ta có thể lấy ví dụ sau : Cho $(E):y^2=x^3-7x+10$ với hai điểm $P(-3,-2)$ và $Q(1,2)$ thuộc $(E)$. Hiển nhiên, ta có thể viết được phương trình đường thẳng $PQ$ là : 
$$
PQ:y=x+1
$$

Bây giờ ta sẽ cần phải đi tìm ra điểm thứ ba mà $PQ$ cắt $(E)$. Để tìm ra điểm thứ ba, ta sẽ lập một phương trình hoành độ giao điểm của $PQ$ và $(E)$ có dạng như sau : 
$$
\begin{cases}
y=x+1\\
y^2=x^3-7x+10
\end{cases}
$$

$$
\Rightarrow (x+1)^2=x^3-7x+10
$$

$$
\Leftrightarrow x^3-x^2-9x+9=0
$$

$$
\Leftrightarrow (x+3)(x-1)(x-3)=0
$$

Với $x=-3$ thì ta có điểm $P(-3,-2)$ và $x=1$ thì ta có điểm $Q(1,2)$. Điểm thứ ba là điểm $R$ có tọa độ là $(3,4)$. Ta sẽ lấy đối xứng của điểm $R$ này qua trục **Ox** thành điểm $R'$ có tọa độ là $(3,-4)$. Như vậy ta sẽ có : 
$$
P\oplus Q=R'=(3,-4)
$$

Hình vẽ minh họa : 
![{5A0F48F5-58D6-4278-8521-A7EB9B61D219}](https://hackmd.io/_uploads/HJwPbW7Wxe.png)
>$R'$ ở đây được thay bằng $S$

Ngoài ra, nếu như điểm $P$ của ta ngày càng tiền gần về điểm $Q$ thì khi này, theo như những lý thuyết của giải tích, đường thẳng $PQ$ của ta sẽ là tiếp tuyến của Elliptic Curve. 

Hay nói đúng hơn là ta sẽ có : 
$$
P\oplus Q= R'
$$

$$
\Rightarrow Q\oplus Q= R'
$$

$$
\Rightarrow 2Q=R'
$$

Với điểm $Q$ là tiếp điểm giữa tiếp tuyến và $(E)$ thì ta có phương trình tiếp tuyến như sau : 
$$
L:y=-x+3
$$

Từ đây ta sẽ lập một phương trình hoành độ giao điểm của $PQ$ và $(E)$ có dạng như sau : 
$$
(-x+3)^2=x^3-7x+10
$$

$$
\Leftrightarrow x^3-x^2-9x+9=0
$$

$$
\Leftrightarrow (x+1)(x-1)^2=0
$$

Với $x=1$ thì ta có điểm $Q(1,2)$, và với $x=-1$ thì ta có điểm $R(-1,4)$. Lấy đối xứng qua trục **Ox** ta có điểm $R'(-1,-4)$
![{777615D4-A936-44AF-A5CA-5E41C315785A}](https://hackmd.io/_uploads/Bk-aAZmblg.png)


Đó là khi ta sử dụng **phương pháp hình học** để mô tả cách tính toán phép cộng trên Elliptic Curve. Còn đối với phương pháp đại số thì ta sẽ có cách tính như sau : 
- Trường hợp 1 : $P\neq Q$
    - Cho $P(x_P,y_P)$ và $Q(x_Q,y_Q)$ là hai điểm thuộc Elliptic Curve $(E):y^2=x^3+Ax+B$. Tọa độ của điểm $R$ thỏa mãn phép toán $P\oplus Q=R$ là : 
$$
\begin{cases}
x_R=\lambda^2-x_P-x_Q\\
y_R=\lambda(x_P-x_R)-y_P\\
\end{cases}
$$

$$
\text{với }\lambda=\frac{y_Q-y_P}{x_Q-x_P}
$$

- Trường hợp 2 : $P=Q$
    - Cho $P(x_P,y_P)$ là điểm thuộc Elliptic Curve $(E):y^2=x^3+Ax+B$. Tọa độ của điểm $R$ thỏa mãn phép toán $P\oplus P=2P=R$ là :
$$
\begin{cases}
x_R=\lambda^2-2x_P\\
y_R=\lambda(x_P-x_R)-y_P\\
\end{cases}
$$

$$
\text{với }\lambda=\frac{3x^2_P-A}{2y_P}
$$

- Trường hợp 3 : $P=-Q$
    - Đây là một trường hợp đặc biệt khi $P$ và $Q$ đối xứng với nhau qua trục hoành. Khi đó, đường thẳng $L$ đi qua $P$ và $Q$ sẽ cắt Elliptic Curve tại **điểm "vô cực"** $\mathcal{O}$. Hay : 
$$
P+Q=(-Q)+Q=\mathcal{O}
$$
![image](https://hackmd.io/_uploads/HyYjmz7Wgg.png)

Về bản chất đây vẫn là một phép toán cộng nên ta sẽ có các tính chất sau : 
- Có tính đóng : Với $P,Q\in E$ thì ta có $P+Q=R\in E$
- Có tính giao hoán : $P+Q=Q+P$ 
- Có tính kết hợp : $P+Q+R=(P+Q)+R=P+(Q+R)$
- Có tính nghịch đảo : $P+(-P)=\mathcal{O}$ 
- Có phần tử đơn vị : $P+\mathcal{O}=\mathcal{O}+P=P$

## 3. Phép toán nhân trên Elliptic Curve
- Chúng ta đã biết về phép cộng hai điểm trên Elliptic Curve. Đó không chỉ đơn thuần là lấy từng tọa độ các điểm cộng lại với nhau mà nó phải trải qua nhiều bước mới cho ra kết quả cuối cùng của phép cộng hai điểm bất kì trên Elliptic Curve.
- Đối với phép nhân, thật đáng tiếc khi mà **không hề tồn tại** phép nhân hai điểm bất kì nào thuộc Elliptic Curve, và cũng như không tồn tại phép chia một điểm có tọa độ $(x,y)$ với một số tự nhiên $n$ nào đó.
- Trên đường cong Elliptic, việc nhân một điểm với một hằng số không đơn thuần chỉ là lấy từng toạ độ rồi nhân, mà thay vào đó, phép nhân ở đây bản chất vẫn là phép cộng, nhưng thực hiện nhiều lần mà thôi.

- Ví dụ : Khi ta tính $3P$
    - Về bản chất, $3P=P+P+P$, vì vậy nên ta sẽ tính $P+P=2P$ trước. Áp dụng các quy tắc tính phép cộng hai điểm như đã nói ở trên, ta sẽ có được điểm $2P$.
    - Có được điểm $2P$, ta sẽ lấy điểm đó cộng thêm với điểm $P$ một lần nữa để cho ra điểm $3P$.
    - Hình ảnh minh họa : ![image](https://hackmd.io/_uploads/rJ9j0Mm-el.png)

- Do không tồn tại phép chia trên Elliptic Curve, nên khi ta có hai điểm $P$ và $Q$ thỏa mãn $Q=nP$, cách để tìm ra số $n$ thường là thử lần lượt $n=1,2,…,p-1$ đến khi tìm được kết quả $Q=nP$. Đây cũng chính là **bài toán Logarit rời rạc trên đường cong Elliptic** mà ta sẽ tìm hiểu ở phần sau.


# II. Elliptic Curve trong trường hữu hạn
Elliptic Curve trong trường hữu hạn là một phần quan trọng trong toán học và mật mã học hiện đại. Cụ thể, phép toán trên điểm của đường cong Elliptic có tính chất khó đảo ngược lại, tạo ra một cơ chế bảo mật đủ mạnh để sử dụng trong các giao thức mật mã học như mã hóa khóa công khai, **trao đổi khóa Diffie-Hellman (ECDH)** và tạo **chữ ký số (ECDSA)**,...

## 1. Định nghĩa 
Trong trường hữu hạn $\mathbb{F}_p$, với $p\geq3$ là một số nguyên tố bất kì, tập hợp các điểm thỏa mãn phương trình : 
$$
y^2\equiv x^3+Ax+B\pmod{p}
$$

là phương trình đường cong Elliptic trong trường hữu hạn $\mathbb{F}_p$, với điều kiện là : $4A^3+ 27B^2 \not\equiv 0\pmod{p}$. Điều kiện này đảm bảo rằng đường cong không có các điểm kỳ dị, điểm mà tiếp tuyến không xác định. (Mình sẽ giải thích tại sao lại có điều kiện đó, ~~nếu mình có thời gian~~ :smile:)

Ngoài ra, ta cũng có trường hợp đặc biệt cho vấn đề này, chính là Elliptic Curve trên trường hữu hạn $\mathbb{F}_2$ và $\mathbb{F}_{2^k}$. Tuy nhiên vì nó hơi phức tạp nên mình sẽ nói về nó sau.

Một số ví dụ về Elliptic Curve trong một số trường hữu hạn $\mathbb{F}_p$: 
$$
\begin{cases}
(E_1):y^2\equiv x^3-3x+3\pmod{11}\\
(E_2):y^2\equiv x^3-6x+5\pmod{17}
\end{cases}
$$

Ảnh minh họa : 
- $y^2\equiv x^3-3x+3\pmod{11}$ : 
![{EC662C9F-B5C3-486A-BE44-1D92F4E80A67}](https://hackmd.io/_uploads/rJoqBX7Zxl.png)

- $y^2\equiv x^3-6x+5\pmod{17}$ : 
![{E2C7813B-6AFB-48CF-A3AA-E068CD765F6A}](https://hackmd.io/_uploads/rJV8L7mZle.png)

Cách vẽ trong SageMath : 
```python=
# (E) : y^2 = x^3 + Ax + B (mod p)
E = EllipticCurve(GF(p), [A, B]) #thay p, A, B vào đây

points = E.points()
xs = [ZZ(P[0]) for P in points if not P.is_zero()]
ys = [ZZ(P[1]) for P in points if not P.is_zero()]
list_plot(list(zip(xs, ys)), size=20, axes=True, gridlines=True)
```


## 2. Phép toán cộng trong trường hữu hạn 
Về cơ bản, phép toán cộng hai điểm trên Elliptic Curve trong trường hữu hạn vẫn giống với cách cộng trong trường số thực, chỉ khác ở chỗ ta sẽ thêm Modulo $p$ vào các phép toán để đảm bảo rằng mọi phần tử đều nằm trong trường hữu hạn.

Ta sẽ lấy một ví dụ để thực hiện. Cho $(E):y^2\equiv x^3-7x+10\pmod{19}$

- Ta có thể in các điểm thuộc $(E)$ bằng SageMath như sau : 
```python=
# in ra các điểm thuộc (E)
E = EllipticCurve(GF(19), [-7, 10])
for P in E:
    print(P)
(0 : 1 : 0)
(1 : 2 : 1)
(1 : 17 : 1)
(2 : 2 : 1)
(2 : 17 : 1)
(3 : 4 : 1)
(3 : 15 : 1)
(5 : 9 : 1)
(5 : 10 : 1)
(7 : 0 : 1)
(9 : 7 : 1)
(9 : 12 : 1)
(10 : 3 : 1)
(10 : 16 : 1)
(12 : 1 : 1)
(12 : 18 : 1)
(13 : 8 : 1)
(13 : 11 : 1)
(16 : 2 : 1)
(16 : 17 : 1)
(17 : 4 : 1)
(17 : 15 : 1)
(18 : 4 : 1)
(18 : 15 : 1)
```

- Thực hiện phép toán cộng trên Elliptic Curve : 
```python=
E = EllipticCurve(GF(19),[-7,10]) 

#chọn điểm P(1,17) và Q(5,10) thuộc (E)
P = E(1,17)
Q = E(5,10)

#tính điểm R = P + Q
R = P + Q
print(R)
# (3 : 15 : 1)
```

```python=
E = EllipticCurve(GF(19),[-7,10]) 

#chọn điểm P(1,17) thuộc (E)
P = E(1,17)

#tính điểm Q = 2P
Q = P + P
print(Q)
# (18 : 4 : 1)
```

- Để kiểm tra một điểm có thuộc đường cong hay không thì ta sử dụng hàm `.is_on_curve(x,y)` như sau : 
```python=
E = EllipticCurve(GF(19),[-7,10]) 

#chọn điểm P(1,17) và Q(11,7)
P = (1,17)
Q = (11,7)

print(E.is_on_curve(1,17)) #true
print(E.is_on_curve(11,7)) #false
```


## 3. Phép toán nhân trong trường hữu hạn
Như đã nói, **không hề tồn tại** phép nhân hai điểm bất kì nào thuộc Elliptic Curve, và cũng như không tồn tại phép nhân hoặc chia một điểm có tọa độ $(x,y)$ với một số tự nhiên $n$ nào đó.

Về bản chất, phép nhân ở đây vẫn là phép cộng, nhưng thực hiện nhiều lần mà thôi.

Ta có thể tính toán trong SageMath như sau : 
```python=
E = EllipticCurve(GF(19),[-7,10]) 

#chọn điểm P(1,17) 
P = E(1,17)

for i in range(1, 20):
    Q = i*P
    print(f"{i} * P =", Q)
    
1 * P = (1 : 17 : 1)
2 * P = (18 : 4 : 1)
3 * P = (9 : 7 : 1)
4 * P = (7 : 0 : 1)
5 * P = (9 : 12 : 1)
6 * P = (18 : 15 : 1)
7 * P = (1 : 2 : 1)
8 * P = (0 : 1 : 0)
9 * P = (1 : 17 : 1)
10 * P = (18 : 4 : 1)
11 * P = (9 : 7 : 1)
12 * P = (7 : 0 : 1)
13 * P = (9 : 12 : 1)
14 * P = (18 : 15 : 1)
15 * P = (1 : 2 : 1)
16 * P = (0 : 1 : 0)
17 * P = (1 : 17 : 1)
18 * P = (18 : 4 : 1)
19 * P = (9 : 7 : 1)
```
Từ một điểm $P$ gốc ban đầu, thực hiện phép nhân $P\in E$ với một số tự nhiên bất kì sẽ cho ta tọa độ của một điểm mới trong $E$.

Nếu như bạn để ý kĩ thì sẽ thấy : Tại điểm $8P$ thì nó có tọa độ là $(0:1:0)$, cũng chính là tọa độ của điểm vô cực $\mathcal{O}$. 

Ngay sau đó, các điểm $9P$, $10P$,... cũng có tọa độ giống với các điểm $P$, $2P$,... Điều đó cho ta thấy được có một sự lặp lại với điểm mốc là các điểm $8P$, $16P$,... 

Đây cũng chính là phần tiếp theo mà ta sẽ tìm hiểu : **Order của Elliptic Curve.**

## 4. Order của Elliptic Curve 
Chúng ta sẽ có hai định nghĩa cho phần này : 
- **Order của một điểm trên Elliptic Curve** : Order của một điểm $P$ thuộc Elliptic Curve là một số nguyên $n$ nhỏ nhất sao cho : 
$$
nP=\mathcal{O}
$$
- **Order của Elliptic Curve** : Order của một Elliptic Curve trong trường hữu hạn $\mathbb{F}_p$ là **tổng số lượng các điểm thuộc đường cong** ấy, bao gồm cả điểm vô cực $\mathcal{O}$, được kí hiệu là $\#E(\mathbb{F}_p)$
 
Số lượng các điểm trên Elliptic Curve trong trường hữu hạn $\mathbb{F}_p$ không phải lúc nào cũng bằng $p$ (tức là số phần tử của trường $\mathbb{F}_p$). Chính vì điều đó, ta có định lý Hasse cho vấn đề này : 
- **Định lý Hasse** : Cho $E$ là một Elliptic Curve thuộc trường hữu hạn $\mathbb{F}_p$. Gọi $\#E(\mathbb{F}_p)$ là số lượng các điểm thuộc $E$, khi đó ta có : 
$$
p+1-2\sqrt{p}\leq \#E(\mathbb{F}_p)\leq p+1+2\sqrt{p}
$$

Ta sẽ lấy một ví dụ để thực hiện. Cho $(E):y^2\equiv x^3-7x+10\pmod{11}$. Dựa theo định lý Hasse, số điểm thuộc $(E)$ vào khoảng : 
$$
p+1-2\sqrt{p}\leq \#E(\mathbb{F}_p)\leq p+1+2\sqrt{p}
$$

$$
\Leftrightarrow 11+1-2\sqrt{11}\leq \#E(\mathbb{F}_p)\leq 11+1+2\sqrt{11}
$$

$$
\Leftrightarrow 5.3\leq \#E(F_p)\leq 18.6
$$


Ta có danh sách các điểm thuộc $(E)$ như sau : 
```python=
# in ra các điểm thuộc (E)
E = EllipticCurve(GF(11), [-7, 10])
for P in E:
    print(P)
(0 : 1 : 0)
(1 : 2 : 1)
(1 : 9 : 1)
(2 : 2 : 1)
(2 : 9 : 1)
(3 : 4 : 1)
(3 : 7 : 1)
(5 : 1 : 1)
(5 : 10 : 1)
(8 : 2 : 1)
(8 : 9 : 1)
(9 : 4 : 1)
(9 : 7 : 1)
(10 : 4 : 1)
(10 : 7 : 1)
```

Như ta thấy, có $15$ điểm thuộc $(E)$, bao gồm cả điểm vô cực $\mathcal{O}$. Vì vậy, ta nói bậc của Elliptic Curve lúc này là $15$, hay $\#E(\mathbb{F}_p)=15$.

Ngoài ra, khi ta chọn điểm $P(1,2)$ làm mốc, ta sẽ có : 
```python=
E = EllipticCurve(GF(11), [-7, 10])
P = E(1,2)
for i in range(20):
    Q = i*P
    print(f"{i} * P =", Q)
    
0 * P = (0 : 1 : 0)
1 * P = (1 : 2 : 1)
2 * P = (10 : 7 : 1)
3 * P = (9 : 7 : 1)
4 * P = (5 : 1 : 1)
5 * P = (3 : 4 : 1)
6 * P = (8 : 2 : 1)
7 * P = (2 : 9 : 1)
8 * P = (2 : 2 : 1)
9 * P = (8 : 9 : 1)
10 * P = (3 : 7 : 1)
11 * P = (5 : 10 : 1)
12 * P = (9 : 4 : 1)
13 * P = (10 : 4 : 1)
14 * P = (1 : 9 : 1)
15 * P = (0 : 1 : 0)
16 * P = (1 : 2 : 1)
17 * P = (10 : 7 : 1)
18 * P = (9 : 7 : 1)
19 * P = (5 : 1 : 1)
```

Dựa vào bảng này, có thể thấy $n=15$ chính là order của điểm $P(1,2)$ vì nó là số nguyên nhỏ nhất thỏa mãn : 
$$
nP=\mathcal{O}
$$

Mỗi một điểm thuộc $(E)$ đều sẽ có một order khác nhau. Bạn có thể tự thử bằng cách dùng SageMath để kiểm tra.
```python=
E = EllipticCurve(GF(11), [-7, 10])

# Kiểm tra order của E
print(E.order()) #15

# Kiểm tra order của từng điểm 
for P in E:
    print(f"Order của điểm {P} là :", P.order())
          
Order của điểm (0 : 1 : 0) là : 1
Order của điểm (1 : 2 : 1) là : 15
Order của điểm (1 : 9 : 1) là : 15
Order của điểm (2 : 2 : 1) là : 15
Order của điểm (2 : 9 : 1) là : 15
Order của điểm (3 : 4 : 1) là : 3
Order của điểm (3 : 7 : 1) là : 3
Order của điểm (5 : 1 : 1) là : 15
Order của điểm (5 : 10 : 1) là : 15
Order của điểm (8 : 2 : 1) là : 5
Order của điểm (8 : 9 : 1) là : 5
Order của điểm (9 : 4 : 1) là : 5
Order của điểm (9 : 7 : 1) là : 5
Order của điểm (10 : 4 : 1) là : 15
Order của điểm (10 : 7 : 1) là : 15
```

Nếu như bạn để ý kĩ : Order của một điểm $P$ bất kì trong $(E)$ đều là một **ước số** của Order của Elliptic Curve (theo định lý Lagrange trong lý thuyết nhóm).
```python=
E = EllipticCurve(GF(17), [-7, 10])
for P in E:
    print(f"Order của {P} là một ước số của E? -", E.order() % P.order() == 0)
```


# III. ECDLP và các ứng dụng 
## 1. ECDLP
DLP (Discrete Logarithm Problem) trên ECC (Elliptic Curve Cryptography) là một vấn đề toán học cốt lõi tạo nên tính an toàn của các hệ mật mã dựa trên đường cong elliptic. Cụ thể ta có bài toán như sau : 

- Cho $E$ là Elliptic Curve được định nghĩa trên trường hữu hạn $\mathbb{F}_p$. Gọi $G$ là một điểm khởi đầu và $Q$ là một điểm bất kì khác $G$ thuộc $E$. Tìm $k$ là một số nguyên thỏa mãn : 
$$
Q=kG
$$
- Giá trị $k$ khi này được gọi là Logarit rời rạc của $Q$ theo cơ sở $P$.

Như đã nói, **không hề tồn tại** phép chia trên Elliptic Curve. Chính vì vậy, cách phổ biến nhất và khả thi nhất để tìm lại được $k$ thường là brute-force trong khoảng $[1; n-1]$, với $n$ là Order của điểm sinh $G$ mà thôi.

Độ phức tạp thuật toán để giải ECDLP là $\mathcal{O}(n)$. Nếu như $n$ nhỏ thì việc brute-force sẽ trở nên khả thi. Nhưng trong thực thế, $n$ của ta thường có độ dài lên đến $256$ hoặc $512$ bits, điều đó khiến cho việc brute-force trở nên không khả thi nữa. 

Thay vì sử dụng phương pháp brute force, có những thuật toán hiệu quả hơn để giải bài toán ECDLP, nhưng độ phức tạp của chúng vẫn tăng rất nhanh theo kích thước của trường hữu hạn : 
- Thuật toán Baby‐step Giant‐step : Độ phức tạp là khoảng $O(\sqrt{n})$ phép tính với $n$ là bậc của đường cong.
- Thuật toán Pollard-Rho : Cũng có độ phức tạp là khoảng $O(\sqrt{n})$ nhưng sẽ tiêu tốn ít bộ nhớ hơn. 
- Thuật toán Pohlig-Hellman : Sử dụng việc phân tích thành thừa số nguyên tố để tìm logarit rời rạc. Độ phức tạp của thuật toán này phụ thuộc vào các ước số nguyên tố của bậc của đường cong.

Dẫu cho có nhiều phương pháp là thế, tuy nhiên vẫn không hề có phương pháp nào đủ mạnh để phá được bài toán ECDLP này nếu các tham số được chọn lọc cẩn thận. Điều đó cho thấy tính khó giải của bài toán ECDLP đến từ việc không có thuật toán nào hiện tại có thể giải nó một cách hiệu quả, ngoại trừ phương pháp brute force và một số thuật toán khác có độ phức tạp tính toán rất cao. Đây chính là nền tảng cho độ an toàn của ECC (Elliptic Curve Cryptography).



## 2. ECDH
**ECDH (Elliptic Curve Diffie-Hellman)** là một giao thức trao đổi khóa bất đối xứng, cho phép hai bên (Alice và Bob) thiết lập một khóa bí mật chung (shared secret) qua kênh không an toàn, dựa trên tính toán trong nhóm điểm của một Elliptic Curve trên trường hữu hạn.

So với Diffie-Hellman truyền thống trên nhóm theo lũy thừa modulo $p$, ECDH dùng toán tử nhân scalar trên Elliptic Curve, giúp đạt độ an toàn tương tự với kích thước khóa nhỏ hơn rất nhiều.

Về cơ bản ta có thuật toán như sau : 
- **Bước 1** : Hai bên Alice và Bob sẽ chọn chung một Elliptic Curve với một điểm sinh $G$.
- **Bước 2** : Đầu tiên, Alice sẽ tạo cho mình một số nguyên $d_A$ bí mật ngẫu nhiên và tính điểm $Q_A=d_A.G$. Tương tự, Bob cũng sẽ tạo cho mình một số nguyên $d_B$ bí mật ngẫu nhiên và tính điểm $Q_B=d_B.G$.
- **Bước 3** : Cả hai sẽ trao đổi giá trị $Q_A$ và $Q_B$ của mình cho đối phương, Alice nhận $Q_B$ và Bob nhận $Q_A$.
- **Bước 4** : Sau khi nhận được giá trị công khai của đối phương, mỗi bên sẽ tính giá trị bí mật chung $s$ như sau : 
$$
\begin{cases}
s_A=d_A.Q_B=d_A.d_B.G\\
s_B=d_B.Q_A=d_B.d_A.G\\
\end{cases}
$$

$$
\Rightarrow s=s_A=s_B
$$

Như vậy là đã trao đổi khóa thành công. Giá trị $s$ khi này là một điểm có tọa độ là $(x_s,y_s)$. Thông thường, người ta chỉ lấy phần hoành độ $x_s$ và đưa vào một hàm dẫn xuất khóa (KDF – Key Derivation Function) để sinh ra các “khóa bí mật” (symmetric keys) để dùng cho mã hóa đối xứng như AES. 

Ví dụ minh họa : Cho $(E):y^2\equiv x^3-7x+10\pmod{19}$. Gọi $G(3,15)$ là điểm sinh của $(E)$. Ta sẽ mô phỏng lại quá trình trao đổi khóa như sau : 
- Alice sẽ chọn giá trị bí mật là : $d_A=7$. Từ có cô có giá trị công khai là : $Q_A=d_A.G=7.G=(2,2)$
- Bob sẽ chọn giá trị bí mật là : $d_B=11$. Từ có anh có giá trị công khai là : $Q_B=d_B.G=11.G=(10,3)$
- Hai bên trao đổi hai giá trị này cho nhau. Khi đó : 
    - Alice sẽ tính khóa bí mật chung là : $d_A.Q_B=7.(10,3)=(17,15)$ 
    - Bob sẽ tính khóa bí mật chung là : $d_B.Q_A=11.(2,2)=(17,15)$

Như vậy, cả hai đã trao đổi thành công khóa bí mật chung.  Khoá này sẽ có thể được dùng làm khóa chung cho mã hóa AES bằng cách lấy tọa độ $x_S=17$, dùng hàm băm như SHA-512 để băm số đó ra thành : 
```python=
from hashlib import sha512
xS = "17".encode()
key = sha512(xS).hexdigest()[:32]
print(key)
# dc2de67eb248dcdc50c63aabd1bca833
```

Tóm tắt lại toàn bộ quá trình như sau : 
```python=
# dùng chung Elliptic Curve E, điểm sinh G
E = EllipticCurve(GF(19), [-7,10])
G = E(3,15)

# tạo khóa bí mật của Alice và Bob
dA = 7
dB = 11

# tính giá trị công khai 
QA = dA*G # (2 : 2 : 1)
QB = dB*G # (10 : 3 : 1)

# tính giá trị bí mật 
sA = dA*QB
sB = dB*QA 
print(sA == sB) 
# True
# s = (17 : 15 : 1)
```



## 3. ECDSA
**ECDSA (Elliptic Curve Digital Signature Algorithm)** - thuật toán sinh chữ ký số dựa trên đường cong Elliptic, là một phiên bản khác của DSA (Digital Signature Algorithm), nhưng được sử dụng trên Elliptic Curve nhằm sử dụng độ khó của bài toán ECDLP để tăng độ bảo mật cho dữ liệu.

ECDSA được sử dụng để tạo chữ kí số cho dữ liệu, giúp chống lại sự giả mạo cũng như làm sai lệch dữ liệu, cung cấp một phương pháp xác thực mà không ảnh hưởng đến tính bảo mật của dữ liệu gốc.

ECDSA được ứng dụng rộng rãi trong rất nhiều lĩnh vực cần tính bảo mật và sự riêng tư dữ liệu, đặc biệt như trong Blockchain.

Chữ kí của ECDSA sẽ có dạng là một cặp số nguyên $(r,s)$. Để có thể kí được một Plaintext $M$ nào đó : 
- Đầu tiên, hai bên Alice và Bob phải thỏa thuận với nhau về việc sử dụng chung một Elliptic Curve trên trường hữu hạn $\mathbb{F}_p$ và một điểm sinh $G$ có **order** $n$ bất kì thuộc đường cong đó.
- Alice sẽ có cho mình một khóa bí mật $d_A\in[1,n-1]$ và một khóa công khai là $Q_A=d_A\times G$.
- Alice sẽ chọn cho mình một số nguyên **ngẫu nhiên tuyệt đối** $k\in[1,n-1]$ khác với khóa bí mật của mình. Số nguyên $k$ này phải được giữ bí mật tuyệt đối, chỉ có Alice được biết.
- Tiếp theo, Alice sẽ tính tọa độ điểm $P$ với công thức : 
$$
P(x_P,y_P)=k.G
$$

- Khi này, giá trị $r$ của ta chính là $x_P$. 
- Alice sẽ đi tính $z=hash(M)$, với hash là một hàm băm nào đó đủ mạnh như : SHA-256, SHA-512... Và công thức tính $s$ sẽ là : 
$$
s\equiv k^{-1}(z+r.d_A)\pmod{n}
$$

- Nếu như $s=0$ thì cần phải chọn lại $k$.
- Sau khi đã có được cặp chữ kí $(r,s)$, Alice sẽ gửi cặp này cho Bob cùng với $M$ đã được mã hóa của mình để Bob có thể xác minh. Bob sẽ giải mã $M$ bằng khóa chung của cả hai, có thể là đã dùng ECDH để trao đổi. Sau khi có được Plaintext $M$, Bob sẽ tính $z=hash(M)$. Theo đó, để xác minh $M$ có phải là chuẩn không, Bob sẽ tính : 
$$
P(x_P,y_P)\equiv s^{-1}.(z.G+r.Q_A)\pmod{n}
$$

- Khi đó, nếu tọa độ $x_P$ tính được từ công thức trên bằng với $r$ thì chứng tỏ rằng chữ ký là hợp lệ và PLaintext $M$ gửi qua là chính xác.
- Ta hoàn toàn có thể chứng minh công thức đó như sau : 
$$
P(x_P,y_P)\equiv s^{-1}.(z.G+r.Q_A)\pmod{n}
$$

$$
\Leftrightarrow P=s^{-1}.(z.G+r.d_A.G)
$$

$$
\Leftrightarrow P=s^{-1}.G.(z+r.d_A)
$$

$$
\Leftrightarrow P=s^{-1}.G.s.k
$$

$$
\Leftrightarrow P=k.G
$$

Nếu chữ ký $(r,s)$ hoặc Plaintext $m$ bị thay đổi trong quá trình truyền, giá trị $P$ tính toán trong quá trình xác minh cũng sẽ thay đổi. Điều này làm cho chữ ký không hợp lệ, giúp Bob phát hiện được sự thay đổi hoặc giả mạo thông tin. Đây chính là cơ chế bảo vệ tính toàn vẹn và xác thực của ECDSA.

Minh hoạ ở hình vẽ này : 
![image](https://hackmd.io/_uploads/rkbhcAU-ge.png)


# IV. Attack
Tuy bài toán ECDLP rất là khó phá, nhưng vẫn sẽ có một số trường hợp vẫn có thể bị tấn công nếu như chọn các tham số không đúng cách. Các bạn có thể xem nhiều kiểu tấn công hơn ở trong **[Blog này](https://github.com/elikaski/ECC_Attacks)**.

Bây giờ ta sẽ ví dụ một số cách tấn công tiêu biểu dưới đây : 
## 1. MOV Attack
Ví dụ, cho một Elliptic Curve có Modulo $p$ và điểm sinh $G$ có order là $n$. Ta sẽ định nghĩa **Embedding Degree** của Elliptic Curve là một số nguyên $k$ nhỏ nhất thỏa mãn phương trình: 
$$
p^k\equiv 1\pmod{n}
$$

Khi đó, bài toán ECDLP sẽ có thể được rút gọn thành bài toán DLP trong trường hữu hạn $\mathbb{F}_{p^k}$ thông thường với các công cụ dễ dàng giải quyết hơn. 

Thông thuờng, giá trị $k$ của ta sẽ rất lớn (gần như là bằng $p$). Nhưng khi $k$ quá bé ($k\leq 6$) thì khi này đường cong của ta sẽ là một **đường cong siêu kì dị (supersingular)**

Bài toán DLP thông thường sẽ được giải vô cùng nhanh chóng, và thuật toán tấn công loại đường cong như thế này được gọi là **MOV attack** (Menezes-Okamoto-Vanstone).

Về cơ bản thì nó cũng khá là phức tạp nên mình sẽ để một cái tool giải các bài dạng như này **[ở đây](https://github.com/jvdsn/crypto-attacks/blob/master/attacks/ecc/mov_attack.py)**, các bạn có thể tham khảo!!!!


## 2. Smart Attack
Như ta đã biết, số lượng các điểm trên Elliptic Curve trong trường hữu hạn $\mathbb{F}_p$ chưa chắc đã bằng với Modulo $p$. Tuy nhiên, vẫn tồn tại một vài đường cong mà số lượng điểm trên đường cong đó bằng với chính Modulo $p$, hay nó sẽ là : 
$$
\#E(\mathbb{F}_p)=p
$$

Khi đó đường cong của ta được gọi là **đường cong bất quy tắc (Anomalous curve)** và nó hoàn toàn có thể bị tấn công bởi **Smart attack**.

Khi giải quyết các dạng bài ECC thì ta cũng nên thử kiểm tra xem đường cong đó có $\#E(\mathbb{F}_p)=p$ hay không. Nếu như có thì hãy dùng chiếc **[tool](https://github.com/jvdsn/crypto-attacks/blob/master/attacks/ecc/smart_attack.py)** này để giải :smile:


## 3. Bias Nonce 
Trong ECDSA, ta sẽ có cặp chữ kí là : 
$$
\begin{cases}
r=x_P\text{ với }P=k.G\\
s\equiv k^{-1}(z+r.d_A)\pmod{n}
\end{cases}
$$

Thông thường, **Nonce** $k$ của ta sẽ được chọn một cách cẩn thận để tránh việc kẻ tấn công có thể có khôi phục được $k$, rồi từ đó có thể lấy được khóa bí mật $d$. 

Tuy nhiên, đôi khi người dùng thường sẽ bỏ qua vấn đề này mà chọn $k$ một cách rất cẩu thả. Ví dụ, người dùng có thể dùng hàm băm để băm một giá trị nào đó để tạo ra $k$. Giả sử trong một Elliptic Curve có order của điểm sinh $G$ là một số nguyên tố $n$ có độ dài là $256$ bits, mà người dùng lại dùng hàm băm $SHA1$ để băm một chuỗi nào đó để ra giá trị $k$. 

Như thế là không được!!!

Vấn đề cốt lõi ở đây là giá trị $k$ của ta bé hơn rất nhiều so với $n$, bởi vì hàm băm $SHA1$ sẽ chỉ trả về cho ta một chuỗi có độ dài là $160$ bits mà thôi. Khi đó, trong phép Modulo có dài là $256$ bits, $96$ bits đầu của $k$ sẽ toàn là bit $0$. Khi đó nó sẽ gọi là **Zero MSB**.


$k$ của ta khi này sẽ được gọi là **biased nonce (nonce bị lệch)**, và nếu như nhiều tin nhắn được ký bởi cùng một khóa bí mật $d$ thì ta sẽ hoàn toàn có thể khôi phục lại được luôn cả khóa bí mật $d$. Vì sao???

Giả sử $l$ bits đầu của $k_i$ toàn là bit $0$. Ta sẽ có một điều kiện là : 
$$
|k_i| < 2^{\log_2n-l}
$$

Với mỗi $k_i$ như vậy, nếu các Plaintext $M$ khác nhau đều kí bởi cùng một khóa bí mật $d$ giống nhau thì khi đó ta có : 
$$
s_i\equiv k_i^{-1}(z_i+r_i.d_A)\pmod{n}
$$

Biến đổi một chút ta sẽ có : 
$$
s_i.k_i\equiv (z_i+r_i.d_A)\pmod{n}
$$

$$
\Leftrightarrow k_i\equiv s_i^{-1}(z_i+r_i.d_A)\pmod{n}
$$

$$
\Leftrightarrow k_i\equiv (s_i^{-1}.z_i)+(s_i^{-1}.r_i.d_A)\pmod{n}
$$

$$
\Leftrightarrow k_i-(s_i^{-1}.r_i).d_A+(-s_i^{-1}.z_i)\equiv 0\pmod{n}
$$

Đây chính là bài toán **HNP** có dạng là : 
$$
\beta_i-t_i\alpha+a_i\equiv 0\pmod{n}
$$

Với $i\in [1,m]$, đặt : 
$$
\begin{cases}
B=2^{???} \text{ thỏa mãn } |k_i|<B<n\\
t_i=s_i^{-1}.r_i\\
a_i=-s_i^{-1}.z_i
\end{cases}
$$
>Thông thường ta phải điều chỉnh $B$ khá nhiều để có thể cho ra được đáp án cuối cùng.

Ta thiết lập một Lattice có dạng như sau : 
$$
\mathcal{L}=
\begin{bmatrix}
n	&	&	&	&	&	\\
&n	&	&	&	&	\\
&	&\ddots	&	&	&	\\
&	&	&n	&	&	\\
t_1	&t_2	&\cdots	&t_m	& B/n	\\a_1	&a_2	&\cdots	&a_m	&		& B 
\end{bmatrix}
$$

Áp dụng LLL cho Lattice này ta sẽ thu được một vector có dạng như sau : 
$$
u=(β_1,β_2,...,β_m,\frac{d_A.B}{n}, −B)
$$

Khi này chỉ cần tính : $u[-2].\frac{n}{B}$ là ta đã thu lại $d_A$ rồi!!!

Đó chính là cách tấn công ECDSA khi mà Nonce không được chọn đúng, tức là Bias Nonce. Các bạn có thể xem thêm **[ở đây](https://eprint.iacr.org/2023/032.pdf)** (nó nằm mục $5.3.2$).



# V. ECC : Challenge CryptoHack
Coming Soon...
