# 第二届VYCTF

sakana战队面向新生的进阶挑战赛, 出题难度偏复杂.

## 比赛时间:

- 开始时间: 2024.10.31 8:00  

- 截止时间: 2024.11.03 8:00

## 奖品

- 通用赛道第一名授予纸质证书 + 周边贴纸

- 通用赛道前五名授予电子证书

- 新生赛道前三名授予实体证书 + 周边贴纸

- 新生赛道前八名授予电子证书

## 成员

本次ctf由sakana战队负责:

- 主办方: VY开源中国(VYCMa Open Source China)

- 参办方: 
  
  - vlang社区
  
  - sakana网络安全联队

- 比赛出题人:
  
  - sudopacman
  
  - nydn
  
  - adwa
  
  - H4nn4h
  
  - Nop

- 美术设计:
  
  - windust
  
  - sudopacman

- 服务器运维:
  
  - sudopacman

## 赛题

| 题目名称                                                                      | 题目描述                                                                                                               | 题目类型    | 出题人        | 题目难度   | 问题指向                                                          | flag                                                                    |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ | ------- | ---------- | ------ | ------------------------------------------------------------- | ----------------------------------------------------------------------- |
| [fast_attack](./WP/fast_attack/README.md)                                 | basectf的精神延续                                                                                                       | Crypto  | sudopacman | normal | p-adic上的ECC问题, 快速破解10组曲线                                      | vyctf{adwa_is_the_best_crypto_player}                                   |
| [Pell Company's A-level products](./WP/Pell_Company's_products/README.md) | 我想拿它来做hash                                                                                                         | Crypto  | adwa       | normal | 自定义曲线的ph算法及曲线背包问题                                             | vyctf{0010011100000101010011100001101111110101011001000101011111101001} |
| [Pell Company's B-level products](./WP/Pell_Company's_products/README.md) | Signin的精神延续                                                                                                        | Crypto  | adwa       | normal | 自定义曲线实现的类RSA加密及二元copper                                       | vyctf{ffefc2b4c1482c8beef04fbdd0e38e7a931d4f41c2e63f7aa13b2e1d1ef7f970} |
| [Pell Company's C-level products](./WP/Pell_Company's_products/README.md) | 应该是这次比赛最简单的题目了()                                                                                                   | Crypto  | adwa       | normal | 自定义曲线的阶的问题                                                    | vyctf{19636b9eb26a0f0701691f84a643b081}                                 |
| [loop](./WP/loop/README.md)                                               | 我们在研究报告中严肃提到了midi曲目的不安全性                                                                                           | MISC    | sudopacman | hard   | 音频上基于osc的信息安全问题研究报告                                           | vyctf{Lycoris_Recoil}                                                   |
| [safe_c_compiler_docker](./WP/safe_c_compiler/README.md)                  | 我从来没有觉得dev开心过(注:flag文本以随机名存放至:/home/pwn2/flag文件夹)                                                                  | Pwn     | nydn       | hard   | 函数指针劫持控制流,编译器处理函数调用传参时生成的寄存器操作代码构造shellcode，相对偏移跳转连接shellcode | vyctf{wow_you_have_become_a_c_expert}                                   |
| [简易计算器](./wp/简易计算器/README.md)                                             | 我们发现vlang的轮子还是有点太少了<br/>出题人:sudopacman<br/>附件:https://wwtk.lanzoum.com/ifsqx2dugtwd<br/>远程环境:139.155.139.109:10005 | web     | sudopacman | normal | 考察一点反编译基础, 模糊测试能力与基本的shell方法应用                                | vyctf{66895b7d-3169-478d-b7ef-fd0530a46fba}                             |
| [签到](./WP/签到/README.md)                                                   | flag存在于网页某个位置, 正常访问即可获取, 请勿进行爆破<br/>出题人:sudopacman<br/>地址:http://vycma.xyz                                         | web     | sudopacman | signin | 送分题, 考察对前端的认识                                                 | vyctf{welcome_come_to_vyctf!!!}                                         |
| [ez_rust](./WP/ez_rust/README.md)                                         | So EZ Rust,难道不是吗？桀桀桀桀<br/>注:输入每个字符之间需加空格，例如0 1 2 3 4<br/>出题人:H4nn4h<br/>附件:https://wwtk.lanzoum.com/iHB2S2dpsdtg   | Reverse | H4nn4h     | normal | rust程序分析                                                      | vyctf{d13bc9c2db60006994cc63be66dd0283}                                 |
| [嘘拟鸡](./WP/嘘拟鸡/README.md)                                                 | 黏氢仁，老夫看你钴铬旌旗，这个嘘拟鸡便两块五卖与你吧<br/>出题人:H4nn4h<br/>附件:https://wwtk.lanzoum.com/iNIk52duojxc                             | Reverse | H4nn4h     | hard   | VM                                                            | vyctf{Vm_ji_ni_tai_mei~~~Oh_baby!}                                      |
