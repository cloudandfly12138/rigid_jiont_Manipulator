# rigid_jiont_Manipulator 二自由度机械臂轨道追踪问题
## 动力学模型 
### 假设 
1. 关节点(joint)刚性连接，不存在弹性振动，非线性振动等问题. 
2. 考虑到该机械臂工作于太空中，忽略重力的影响. 
3. 两连杆(link)密度均匀，且线密度相等.
### 公式 
- $ M(q) \ddot{q}+C(q,\dot{q}) \dot{q}= \tau $ 
- 其中，$ M(q)= $ 
$$ 
\bigl[\begin{matrix}
m_{1}l_{c1}^{2}+m_{2}(l_{1}^{2}+l_{c2}^{2}+2l_{1}l_{c2} \cos q_{2})+I_{1}+I_{2} & m_{2}(l_{c2}^{2}+l_{1}l_{c2} \cos q_{2})+I_{2} 
\\ &
m_{2}(l_{c2}^{2}+l_{1}l_{c2} \cos q_{2})+I_{2} & m_{2}l_{c2}^{2}+I_{2} 
\end{matrix}\bigr] 
$$ 
- $ C(q,\dot{q})=  $ 
$$
-m_{2}l_{1}l_{c2}\sin q_{2}
\bigl[\begin{matrix}
\dot{q_{2}} & \dot{q_{1}}+\dot{q_{2}} \\ & -\dot{q_{1}} & 0
\end{matrix}\bigr] 
$$
- $ I_{1}=m_{1}l_{1}^{2}/12 , I_{2}=m_{2}l_{2}^{2}/12 $
- $ l_{c1}=l_{1}/2 , l_{c2}=l_{2}/2 $
### 数值求解（二选一）
1. 四阶Runge-Kutta法，即ode4
2. Euler法，即泰勒一阶近似求解
## PID控制率 
- 误差$ e(k)=Q-q(k) $
- 扭矩$ \tau=K_{p}e(k)+K_{i}\Sigma e(k)+K_{d1}(e(k)-e(k-1)) $
- Q为目标点，q为当前点
- 通过调节$ K_{p},K_{i},K_{d} $ 改变扭矩$ \tau $，实现当前点到目标点的收敛.
## 程序思路
- 将目标轨道离散化为N个点，通过xy反解出Q1,Q2,问题即转化为N个控制收敛问题
- 具体实现如下
1. 设置双重循环，大循环每次改变目标点的q坐标(即Q1,Q2)，小循环实现控制收敛
2. 小循环中，根据当前q值，按照动力学方程，利用RK4法(或Euler法)计算下一个小循环步长的q值
3. 小循环中，计算当前PID控制器的输出$ \tau $,重复3,至小循环结束
4. 画图
- 调整Kp,Ki,Kd,使每个小循环的误差收敛到0
## 附录
### RK4法原理
对常微方程
\begin{equation}
\left\lbrace 
\begin{array}{l}
u'(t)=f(t,u), a\leq t\leq b \\
& u(a)=u_{0}
\end{array}
\right. 
\end{equation}
计算公式如下
\begin{equation}
\left\lbrace 
\begin{array}{l}
k_{1}=f(t_{i},u_{i}) \\
& k_{2}=f(t_{i}+h/2,u_{i}+hk_{1}/2) \\
\right. 
\end{equation}
