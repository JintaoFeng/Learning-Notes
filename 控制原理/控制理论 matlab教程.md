# 控制理论 matlab教程

除了冒号运算符，linespace和logspace命令也可以用来生成序列数据。

```matlab
x=linspace(n1,n2,n);
w=logspace(d1,d2,n);
```

`linspace`命令生成从n1到n2的向量，共n个点。

logspace命令生成从$10^{d1}$到$10^{d2}$的、按照对数间隔的向量，共n个点。



# 频率响应分析

## 用matlab绘制bode图

命令`bode`可以计算连续线性时不变系统的频率响应的幅值和相角。在matlab中输入命令（不带左端参数），屏幕上就会画出伯德图，图中幅值的单位为分贝(dB)。

```matlab
bode(num,den);
bode(num,den,w);
bode(A,B,C,D);
bode(A,B,C,D,w);
bode(A,B,C,D,iu,w);
bode(sys);
```

在带有左端参数，例如采用下式时，bode只将系统频率响应的数值赋予矩阵变量mag、phase和w，而不是在屏幕上绘制伯德图。带有左端参数的命令`bode`常用形式有：

```
[mag,phase,w] = bode(num,den);
[mag,phase,w] = bode(num,den,w);
[mag,phase,w] = bode(A,B,C,D);
[mag,phase,w] = bode(A,B,C,D,w);
[mag,phase,w] = bode(A,B,C,D,iu,w);
[mag,phase,w] = bode(sys);
```



矩阵mag和phase分别包含系统频率响应在指定频率点算得的幅值和相角。相角的单位为角度（°）。幅值可以通过以下语句转换成分贝。

```matlab
magdB=20*log10(mag);
```

如需指定频率范围，可以使用命令`logspace(d1,d2)`或者`logspace(d1,d2,n)`，`logspace`命令生成从$10^{d1}$到$10^{d2}$的、按照对数间隔的向量，共n个点。可以在bode命令中包含指定的频率向量。

**example**

```matlab
num=[9,1.8,9];
den=[1 1.2 9 0];
w=logspace(-2,3,100);
bode(num,den,w);
grid on;
```

![image-20210220154758763](..\picture\image-20210220154758763.png)

如果我们需要指定幅值范围，比如在上面的例子中，我们可以指定幅值图的范围为50到-50，可以通过以下方式实现：

```
dBmax=50*ones(1,100);
dBmin=-50*ones(1,100);
semilogx(w,magdB,w,dBmax,w,dBmin);
```

相位可以同理设置。

```matlab
num=[9,1.8,9];
den=[1 1.2 9 0];
w=logspace(-2,3,100);
[mag,phase,w] = bode(num,den,w);
magdB=20*log10(mag);
dBmax=50*ones(1,100);
dBmin=-50*ones(1,100);
figure;
semilogx(w,magdB,'-',w,dBmax,w,dBmin);

h = findobj(gcf, 'Type','line');
set(h, 'LineWidth', 2);
grid on;
title("Bode Diagram of G(s)=9(s^2+0.2s+1/[s(s^2+1.2s+9)])");
xlabel("Frequency(rad/sec)");
ylabel("Gain dB");

pmax=150*ones(1,100);
pmin=-150*ones(1,100);
figure;c 
semilogx(w,phase,'-',w,pmax,w,pmin);
grid on;
h = findobj(gcf, 'Type','line');
set(h, 'LineWidth', 2);
title("Bode Diagram of G(s)=9(s^2+0.2s+1/[s(s^2+1.2s+9)])");
xlabel("Frequency(rad/sec)");
ylabel("Phase deg");
```

![image-20210220170311303](..\picture\image-20210220170311303.png)

对于状态空间模型，他的伯德图可以由以下定义：
$$
\dot{x}=Ax+Bu\\
y=Cx+Du
$$
x=状态空间向量。（n-向量）

y=输出向量。（m-向量）

u=控制向量。（r-向量）

A=状态矩阵。（nxn矩阵）

B=控制矩阵。（nxr矩阵）

C=输出矩阵。（mxn矩阵）

D=转换矩阵。（mxr矩阵）

命令`bode(A,B,C,D)`可以绘制与每个系统输入相对应的一系列伯德图，其频率范围是自动设定的。

命令`bode(A,B,C,D,iu)`可以绘制从输入`iu`到该系统的所有输出$（y_1,y_2,\cdots y_m）$的伯德图，其中iu为系统的第i个输入。伯德图的频率范围是自动设定的。





## 相角裕度和增益裕度

一般而言，$G(jw)$的轨迹离$-1+j0$点越近，系统响应就越震荡。$G(jw)$的轨迹与$-1+j0$点的接近程度可以看作系统稳定裕度的度量。不过，这并不适用于条件稳定系统。通常用相角裕度和增益裕度来描述这种接近程度。

**相角裕度：**相角裕度是指在增益穿越频率点上，为使系统濒临不稳定边缘所需引入的滞后相角。增益穿越频率是开环传递函数幅值$G(jw)=1$时的频率。相角裕度$\gamma$是180°加上开环传递函数在增益穿越频率上的相角$\phi$。

下图演示了伯德图、极坐标图和对数幅相图中稳定和不稳定系统的相角裕度。在极坐标图中，可以从原点到$G(jw)$与单位圆的交点画一条直线。那么，从负实轴到这条线的角度即为相角裕度，$\gamma>0$时相角裕度为正，$\gamma<0$时相角裕度为负。对于最小相位系统，当系统稳定时相角裕度必为正。在对数幅相图中，复平面上的临界点对应于0dB线和-180°的交点。

**增益裕度：**增益裕度是指当$G(jw)$的相角为-180°时，幅值$|G(jw)|$的倒数。定义相角穿越频率$\omega_1$为开环传递函数的相角等于-180°时的频率，就可以得到增益裕度$K_g$。

当增益裕度用分贝数表示时，如果$K_g>1$，则增益裕度为正；如果$K_g<1$，则增益裕度为负，因此，正的增益裕度表明系统稳定，而负的增益裕度表明系统不稳定。

对于稳定的最小相位系统，增益裕度表明增益要增加多少才会使系统变得不稳定。对于不稳定系统，增益裕度表明增益要下降多少才会使系统变得不稳定。

一阶或二阶系统的增益裕度无穷大，这是因为这些系统的及坐标轴不穿越负实轴。因此，从理论上讲，一阶或二阶系统不可能不稳定。

注意，对于开环不稳定的非最小相位系统，只有当$G(jw)$轨迹包围$-1+j0$点时，闭环系统才能满足稳定性条件。因此，这类稳定的非最小相位系统的相角裕度和增益裕度均为负值。





