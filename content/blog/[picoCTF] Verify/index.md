---
title: "[picoCTF] Verify"
author: "Wook Lee"
date: "2024-07-04"
tags: ["picoCTF"]
---

<div style="text-align: center"><img src="./giphy.gif"></div>

_Happy 4th of July_

This is my first time experiencing picoCTF. I had heard of it numerous times on the Internet, and it turned out to be way more fun and engaging than I expected. I think I’ll stick to picoCTF for a while before moving on to HackTheBox, as I feel picoCTF provides more basic and foundational CTFs than HTB. Anyway, I'm really glad I stumbled upon this resource and decided to give it a try.

---

### Description

_Author_: Jeffrey John

People keep trying to trick my players with imitation flags. I want to make sure they get the real thing! I'm going to provide the SHA-256 hash and a decrypt script to help you know that my flags are legitimate.

---

First we need to connect to SSH server by `ssh ctf-player@rhea.picoctf.net -p 61394`

After you are logged into the server, type `ls` to check files and directories

```shell
ctf-player@pico-chall$ ls
checksum.txt  decrypt.sh  files
```

<br>

Checksum
`3ad37ed6c5ab81d31e4c94ae611e0adf2e9e3e6bee55804ebc7f386283e366a4`

<br>

```shell
ctf-player@pico-chall$ ls files
0agQiFLS  3kS2W94N  92q4JPFx  CxDy3RIy  GodaoG3e  K9mG0gBB  OVCZKr6X  QNPhWoha  Tui6wJfr  WxflVQBr  Za4Oz6eg  cWvaGe5W  f3Oy21ek  jSNCdUA3  oRzmzVaf  st6t5Khc  wx3RfP7B
0pEkV2ds  3kYAjtIX  9EMX68VB  Cz1ZfreC  H3rLRpMi  KCi7EWrH  OX3IlkB9  QjJChAMg  TwspwefZ  X4s5LEFG  ZaN8BLaN  caGexAeh  f7v2BtXe  k2kklNHQ  oZ71cyGG  t0luWBy8  xzvNiQwK
0wWA41ot  3mKIltIv  9nlUSB5k  DHDt0q0q  HG1OKGnm  KV1QF2CV  OYpH5Rfs  RIjDMPzO  U14Ody2N  X8OIdwTY  ZfRDLvRe  cdLJdv1c  fKWz0UcF  k3AMdGoS  oaOPzO00  t3YK8diU  yOEWonka
0yVzp2am  3xjxuSOP  A0aXQwRy  DMo5tbhE  HPGHI002  KcHrhrZK  Opx3E3FO  RJZglp6X  U6YYQ3HK  XScpq1MB  ZgC80wUc  chaBbXpo  fWnAvkyW  k5aiuMj6  oeq8cWO4  tLG9HM3a  yajqgzPt
12GUEFi0  49gLh1zo  AKEjqj8u  DNZBkTaH  HRSTilo9  KfXHVDto  Oq8kRa6b  RXCGKuwy  UKi454YR  XW7eedrJ  aDSBn4a7  ciYQtbBp  ffM4jxzv  kLFFhUci  orc2TmKb  uJzNSw96  ypsNLNOA
12R70dbh  49qfB01x  AeCM4Vvt  Di7h281J  HchfFzCW  L4oXILcc  OzUaGPJp  RZfcsWcw  UWQ5qeIb  XeegDb5b  aNhIds5X  coeqJdbu  ffw8WXYD  l4lFl66y  p2FLQhZe  uQyUDnOZ  ytd5LOm1
1EQhRC4i  5p13qchp  AhVRy5sU  Dj2k48PL  HlVWXs9d  LCeIKlH5  P0zSZ01H  Ricc6Xgh  UiciI0Fr  XfZqFKg1  aWrHNgwY  covwBpER  fjdhIY2n  lUsUQJ4B  pyJ5KxZp  v6LGqmwI  yzqsPNuQ
1FjaHS3F  5r6mt5Iq  Aqg5GrWn  DqlYDm8h  HouNr2C0  LJckwIQl  PB3Mh3mc  RuvyhgVU  UinP1H13  XjZyXSLg  aXaKm8w8  cxKJcozU  ftNxaFsY  m2KQa1Hp  q12b9M6L  v8sVJPvD  zH4qslwZ
1cYEYb6L  6kPfytcD  BMBlGtHm  E5vU7ojc  I7V7Ju5A  LRXQ5oNw  PBh0NfB8  S9jVApXB  Ujaf2OwZ  XrAdESZl  b47rxTge  dINee6RV  gQQDt3TI  m5cOAhxS  q2yrfUO0  vCUbox39  zM5KAlbJ
1iXLQGXR  6rd0x1aK  BP14euwo  EAZMi0dM  IAiKRrWF  LqUvuW7o  PGQBY3gP  SA3xkV3Z  UzFFqfxa  XrvFYdDg  b7e3VlZ8  dS2gaUE4  gY52Y8t0  mVEKZ3oW  q7LqCtpM  vTgToTLG  zMQ1nXew
2hOQXHZC  6vYE68JA  BkMRgk48  EB06dS7k  INu5ULbd  M19EEXCb  PKjcfWRy  SXltVhZT  V2CZ7uY0  Xw38pYK0  bDlbvwh8  dVJ9IeAT  gZ9thrVF  mcjegRRr  q845iKih  vfN94Ek3  zWeRABcB
2nsMaCTj  7U4dSToL  BoBiL395  EVjQlyVv  Ic0zhcYV  MKg0Y6X7  PUt9VRoX  SbFIvoSj  VHviUe6f  Xxuckerf  bIl1SDxK  dZVnOthw  hDyxnGKZ  n05ZtlwX  qRM9Cugo  vnr7vUto  zZe9EIdH
2zpsEiQJ  7cnZoSuo  BvplEv2R  EtzUMFMR  J75ycvyv  MwfNVxMB  Pf3zlSAC  SkQZlbB2  VUPouqTb  Y4u4wEGY  bf6qqi2j  dkV6p1DF  hKDw8Cwn  n7C2bpPk  qYpcbIbt  w63X3jh4  zhBiEB8c
363nnRwS  83NyszLP  CS66jcDI  FN4tlPPC  JBVTXHTa  MzdMfZHa  PtEdNKlH  TBQytfqs  Vj67lQyx  YCf9VpOR  blIGgzh7  dtc6oz6G  iRXqo85L  nDjOINiw  rQP8fnIe  wVXmJCep
3BrlDAbo  8Dw7QTA4  CXjlF1PS  FmmWrIZ7  JJxoEHaC  NFea6BFS  PwyKZRSq  TSJCXwIT  WBStW98P  YG1pCKDt  br8OM834  e018b574  imkWZwMa  nF6EHta6  rUOHrSvR  wj3JmMau
3PmKbHhH  8HwmtNGn  CaDiX4Hy  GhWI4eJh  JXT0M9Rt  NgY5gymg  PzU12rre  TTjLO7LQ  WET6cnvi  YPNiaCgG  brRmUfmD  e7irOvB1  j1RDffgK  niz3loOL  rfNEgD0l  wr59gSPm
3ckGbZtx  8cSetvuU  Cdje4q5k  GkM1UPTw  Ji1SbY1i  NopiZGwa  Q5IWBGXV  ThekmVcy  Wh09BzOS  YdulsHwq  btZJHGWw  exGstYty  j5Tc1Z62  o0R9Vxk8  ruWv5GEU  wtPMeWzq
3eBHvesU  8sqe8FVs  CntNA8JK  GoGhbQto  K1clcP8Z  O2eRM15N  Q8eHpBL9  TqHFzH54  WrlAfM9p  YdxjMT1r  c3eE4Atp  exbu4azE  jNImYWHs  oGmnMVYP  rudfRBMm  wtq06VT7
```

At this stage, what we gotta do is create a checksum of each file with `sha256sum` command and see if we get the matching checksum. In order to create checksum of all files in a directory we can do `sha256sum <directory>/*`

```shell
ctf-player@pico-chall$ sha256sum files/* | grep $(cat checksum.txt)
3ad37ed6c5ab81d31e4c94ae611e0adf2e9e3e6bee55804ebc7f386283e366a4  files/e018b574
```

As we can see from the output above, we found a matching checksum in a file called `e018b574`

Now we can decrypt the file by running `./decrypt.sh files/e018b574` command

```shell
ctf-player@pico-chall$ ./decrypt.sh files/e018b574
picoCTF{trust_but_verify_e018b574}
```

We got the flag!

**_flag_**: `picoCTF{trust_but_verify_e018b574}`
