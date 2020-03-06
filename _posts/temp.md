---
udlayout:    post   				    # 使用的布局（不需要改）
title:    博客尝试页   # 标题 
subtitle: qwq康康我的公式显示不显示的出来 枯了   #副标题
date:      2020-03-05 				# 时间
author:    Culaccino					# 作者
header-img: img/upd_img7.PNG        #这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签

---


$$
p_t=softmax(tanh(W_p[\gamma_tV_t,E_t,h_t]+b_p))
$$

$$
\theta^*=arg\,\max_{\theta} \sum_{t=1}^T logP_r(\Psi_t|\Psi_{t-1},V_t,E_t;\theta)
$$

$$
\xi_i=\sum_{j=1}^m\alpha_{i,j}r_{i,j}
$$

$$
\alpha_{i,j}=\frac{exp(q_{i,j})}{\sum_{j=1}^mexp(q_{i,j})}\\
q_{i,j}=w_rtanh(W_rr_{i,j}+U_r\alpha_{i-1,j}+b_r)
$$

$$
V_t=\sum_{i=1}^n\beta_{t,i}\xi_i
$$

$$
\beta_{t,i}=\frac{exp(l_{t,i})}{\sum_{i=1}^nexp(l_{t,i})}\\
l_{t,i}=w_ftanh(W_f\xi_i+U_fh_{t-1}+b_f)
$$

$$
\rho_{t,s}=tanh(W_Se_{t-s:t-1})\ \ \ \ \ s\in 1,2,3
$$

$$
\eta_{t,s}=\frac{exp(z_{t,s})}{\sum_{s=1}^3exp(z_{s,t})}\\
z_{t,s}=w_Ztanh(W_z\rho_{t,s}+U_zh_{t-1}+b_z)
$$

$$
i_t=\sigma(W_ix_t+U_ih_{t-1}+b_i)\\
f_t=\sigma(W_hx_t+U_hh_{t-1}+b_h)\\
o_t=\sigma(W_ox_t+U_oh_t+b_o)\\
g_t=\sigma(W_gx_t+U_gh_{t-1}+b_g)\\
c_t=f_tc_{t-1}+i_tg_t\\
h_t=o_t\Phi(c_t)
$$

$$
\gamma_t=sigmoid(W_\gamma*h_t)
$$
