# fast_attack

题目:

```python
from sage.all import *
import signal
from random import randint

flag = 'vyctf{adwa_is_the_best_crypto_player}'

class TimeoutException(Exception):
    pass

def timeout_handler(signum, frame):
    raise TimeoutException("Function timed out")

def my_function():
    # do some time-consuming task here
    pass

def main():
    point = C.random_point()
    num = randint(1, point.order() - 1)
    print(point)
    print(point * num)
    if input('>') != str(num):
        exit()
        pass

def task():
    signal.alarm(1)
    try:
        main()
        signal.alarm(0)
        return a
    except TimeoutException:
        print('\nIt has exceeded the time limit.')
        exit()

if __name__ == "__main__":
    a = 0
    b = -3045286915816228928193649683228046896481316
    p = 235322474717419
    P = GF(p)
    C = EllipticCurve(P, [a, b])
    signal.signal(signal.SIGALRM, timeout_handler)
    for i in range(10):
        task()

    print(flag)
```

比较详细地聊一下题目组成, ECC曲线落在p-adic域, 其实在sage源码上是有介绍的, 这里引用一篇介绍p-adic比较详细的博文:

[无穷位编码的镜像数和p-adic整数](https://blog.sciencenet.cn/blog-506146-669710.html)

本次攻击可以直接调用前段时间开源的[EXP](https://gitee.com/cryingn/exp)工具包: 

```python
def pwn():
    ret = rl().decode()
    x,y,z = ret.replace('(','').replace(')','').split(':')
    point = C(int(x),int(y),int(z))
    ret = rl().decode()
    x,y,z = ret.replace('(','').replace(')','').split(':')
    new_point = C(int(x),int(y),int(z))
    data = point.padic_elliptic_logarithm(new_point, p)
    sa(b'>', str(data).encode()) 
```

通过sage的`padic_elliptic_logarithm()`函数可以快速获取曲线的离散对数, 我也单独将函数剥离出来, 其中函数的调用类属于`EllipticCurve()`.

```python
def padic_elliptic_logarithm(self, p, absprec=20):
        r"""
        Computes the `p`-adic elliptic logarithm of this point.

        INPUT:

        - ``p`` -- integer: a prime ``absprec`` -- integer (default: 20):
          the initial `p`-adic absolute precision of the computation

        OUTPUT:

        The `p`-adic elliptic logarithm of self, with precision ``absprec``.

        AUTHORS:

        - Tobias Nagel
        - Michael Mardaus
        - John Cremona

        ALGORITHM:

        For points in the formal group (i.e. not integral at `p`) we
        take the ``log()`` function from the formal groups module and
        evaluate it at `-x/y`.  Otherwise we first multiply the point
        to get into the formal group, and divide the result
        afterwards.

        .. TODO::

            See comments at :issue:`4805`.  Currently the absolute
            precision of the result may be less than the given value
            of absprec, and error-handling is imperfect.

        EXAMPLES::

            sage: E = EllipticCurve([0,1,1,-2,0])
            sage: E(0).padic_elliptic_logarithm(3)
            # needs sage.rings.padics
            0
            sage: P = E(0, 0)
            # needs sage.rings.padics
            sage: P.padic_elliptic_logarithm(3)
            # needs sage.rings.padics
            2 + 2*3 + 3^3 + 2*3^7 + 3^8 + 3^9 + 3^11 + 3^15 + 2*3^17 + 3^18 + O(3^19)
            sage: P.padic_elliptic_logarithm(3).lift()
            # needs sage.rings.padics
            660257522
            sage: P = E(-11/9, 28/27)
            # needs sage.rings.padics
            sage: [(2*P).padic_elliptic_logarithm(p)/P.padic_elliptic_logarithm(p) for p in prime_range(20)]  # long time, needs sage.rings.padics
            [2 + O(2^19), 2 + O(3^20), 2 + O(5^19), 2 + O(7^19), 2 + O(11^19), 2 + O(13^19), 2 + O(17^19), 2 + O(19^19)]
            sage: [(3*P).padic_elliptic_logarithm(p)/P.padic_elliptic_logarithm(p) for p in prime_range(12)]  # long time, needs sage.rings.padics
            [1 + 2 + O(2^19), 3 + 3^20 + O(3^21), 3 + O(5^19), 3 + O(7^19), 3 + O(11^19)]
            sage: [(5*P).padic_elliptic_logarithm(p)/P.padic_elliptic_logarithm(p) for p in prime_range(12)]  # long time, needs sage.rings.padics
            [1 + 2^2 + O(2^19), 2 + 3 + O(3^20), 5 + O(5^19), 5 + O(7^19), 5 + O(11^19)]

        An example which arose during reviewing :issue:`4741`::

            sage: E = EllipticCurve('794a1')
            sage: P = E(-1,2)
            sage: P.padic_elliptic_logarithm(2)  # default precision=20                 # needs sage.rings.padics
            2^4 + 2^5 + 2^6 + 2^8 + 2^9 + 2^13 + 2^14 + 2^15 + O(2^16)
            sage: P.padic_elliptic_logarithm(2, absprec=30)
            # needs sage.rings.padics
            2^4 + 2^5 + 2^6 + 2^8 + 2^9 + 2^13 + 2^14 + 2^15 + 2^22 + 2^23 + 2^24 + O(2^26)
            sage: P.padic_elliptic_logarithm(2, absprec=40)
            # needs sage.rings.padics
            2^4 + 2^5 + 2^6 + 2^8 + 2^9 + 2^13 + 2^14 + 2^15 + 2^22 + 2^23 + 2^24
            + 2^28 + 2^29 + 2^31 + 2^34 + O(2^35)
        """
        if not p.is_prime():
            raise ValueError('p must be prime')
        debug = False  # True
        if debug:
            print("P=", self, "; p=", p, " with precision ", absprec)
        E = self.curve()
        Q_p = Qp(p, absprec)
        if self.has_finite_order():
            return Q_p(0)
        while True:
            try:
                Ep = E.change_ring(Q_p)
                P = Ep(self)
                x, y = P.xy()
                break
            except (PrecisionError, ArithmeticError, ZeroDivisionError):
                absprec *= 2
                Q_p = Qp(p, absprec)
        if debug:
            print("x,y=", (x, y))
        f = 1   # f will be such that f*P is in the formal group E^1(Q_p)
        if x.valuation() >= 0:   # P is not in E^1
            if not self.has_good_reduction(p):   # P is not in E^0
                n = E.tamagawa_exponent(p)   # n*P has good reduction at p
                if debug:
                    print("Tamagawa exponent = =", n)
                f = n
                P = n*P   # lies in E^0
                if debug:
                    print("P=", P)
                try:
                    x, y = P.xy()
                except ZeroDivisionError:
                    raise ValueError("Insufficient precision in "
                                     "p-adic_elliptic_logarithm()")
                if debug:
                    print("x,y=", (x, y))
            if x.valuation() >= 0:   # P is still not in E^1
                t = E.local_data(p).bad_reduction_type()
                if t is None:
                    m = E.reduction(p).abelian_group().exponent()
                else:
                    m = p - t
                if debug:
                    print("mod p exponent = =", m)
                    # now m*(n*P) reduces to the identity mod p, so is
                    # in E^1(Q_p)
                f *= m
                P = m*P   # lies in E^1
                try:
                    x, y = P.xy()
                except ZeroDivisionError:
                    raise ValueError("Insufficient precision in "
                                     "p-adic_elliptic_logarithm()")
                if debug:
                    print("f=", f)
                    print("x,y=", (x, y))
        vx = x.valuation()
        vy = y.valuation()
        v = vx-vy
        if not (v > 0 and vx == -2*v and vy == -3*v):
            raise ValueError("Insufficient precision in "
                             "p-adic_elliptic_logarithm()")
        try:
            t = -x/y
        except (ZeroDivisionError, PrecisionError):
            raise ValueError("Insufficient precision in "
                             "p-adic_elliptic_logarithm()")
        if debug:
            print("t=", t, ", with valuation ", v)
        phi = Ep.formal().log(prec=1+absprec//v)
        return phi(t)/f
```

> 引用注释: 如果self为'p'-adic的椭圆对数, 其精确到'absprec'.

在计算过程中我们能比较直观看到模和阶是相等的:

```python
from EXP import *

a = 0
b = -3045286915816228928193649683228046896481316
p = 235322474717419
P = GF(p)
C = EllipticCurve(P, [a, b])

point = C.random_point()
print(p == point.order())
# True
```

ECC落在p-adic域上的情况可以稍微看一下几篇相关论文:

[Elliptic Curves over p-adic Fields (amherst.edu)](https://rlbenedetto.people.amherst.edu/talks/uconn14.pdf)
[anomalous.pdf (monnerat.info)](https://www.monnerat.info/publications/anomalous.pdf)
[logs.pdf (nottingham.ac.uk)](https://www.maths.nottingham.ac.uk/plp/pmzcw/download/logs.pdf)

看了一下大部分解题人直接使用了板子, 我随意抄一段:

```python
def SmartAttack(P,Q,p):
    E = P.curve()
    Eqp = EllipticCurve(Qp(p, 2), [ ZZ(t) + randint(0,p)*p for t in E.a_invariants() ])
    P_Qps = Eqp.lift_x(ZZ(P.xy()[0]), all=True)
    for P_Qp in P_Qps:
        if GF(p)(P_Qp.xy()[1]) == P.xy()[1]:
            break
    Q_Qps = Eqp.lift_x(ZZ(Q.xy()[0]), all=True)
    for Q_Qp in Q_Qps:
        if GF(p)(Q_Qp.xy()[1]) == Q.xy()[1]:
            break
    p_times_P = p*P_Qp
    p_times_Q = p*Q_Qp
    x_P,y_P = p_times_P.xy()
    x_Q,y_Q = p_times_Q.xy()
    phi_P = -(x_P/y_P)
    phi_Q = -(x_Q/y_Q)
    k = phi_Q/phi_P
    return ZZ(k)
```

我们可以简单考虑一下smartattack攻击到底进行了什么, 取到`E`后重新构造了新的曲线`Eqp`, 找到对应`p`, `q`点提升到`P_Qps`和`Q_Qps`, 然后离散对数求解.
padic_elliptic_logarithm()函数则是将曲线改动到环上进行离散对数计算, 两者原理相似, 不过直接调用函数有更高效率, 本来我想卡个极限求解, 不过考虑到可能会出现连接延迟的问题, 稍微放宽了一点时间, 结果大家全部找板子秒了, 头疼.
以下是EXP:

```python
from EXP import *

io('./exp.py', '139.155.139.109:10002')

log(True)

a = 0
b = -3045286915816228928193649683228046896481316
p = 235322474717419
P = GF(p)
C = EllipticCurve(P, [a, b])

def pwn():
    ret = rl().decode()
    x,y,z = ret.replace('(','').replace(')','').split(':')
    point = C(int(x),int(y),int(z))
    ret = rl().decode()
    x,y,z = ret.replace('(','').replace(')','').split(':')
    new_point = C(int(x),int(y),int(z))
    data = point.padic_elliptic_logarithm(new_point, p)
    sa(b'>', str(data).encode())

for _ in range(10):
    pwn()
print(rl().decode())
```



