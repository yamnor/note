# エチレンの円錐交差構造を探索する

分子が比較的高いエネルギーの波長領域（近赤外〜可視・紫外領域）の光を吸収すると、電子基底（S<sub>0</sub>）状態から電子励起（S<sub>n</sub>）状態に遷移することがあります。このような光励起の後、分子は、吸収したエネルギーを光として放出しながら S<sub>0</sub> 状態に戻る**発光**が起こることもあれば、立体構造が変化して S<sub>n</sub> 状態と S<sub>0</sub> 状態のエネルギー差が非常に小さくなることで、光を放出しないで S<sub>0</sub> 状態に戻る**非断熱遷移**が起こることもあります。非断熱遷移は、光照射によって分子の形を変える**光異性化反応**などにおいて重要な役割を果たし、分子が多彩な機能を発現するための源となっています。

さて、光励起後、分子の立体構造が徐々に変化して電子状態間のエネルギー差が段々と小さくなり、ついにそのエネルギー差がゼロになったとき、非断熱遷移の確率が最も起こりやすくなります。この「電子状態間のエネルギー差がゼロとなり互いに交差」している状態は**円錐交差**と呼ばれています。ある分子の光化学反応を理論的に解析するとき、その出発点として、光励起後に到達する円錐交差の地点を探索して、その立体構造を調べたり、非断熱遷移に関わる様々な性質を調べたいと思うことがしばしばあります。

このノートでは、最もシンプルな芳香族化合物である**エチレン**（CH<sub>2</sub>=CH<sub>2</sub>）を例として、その S<sub>0</sub>/S<sub>1</sub> 状態間が交差する**円錐交差構造を探索する方法**をまとめています。量子化学計算プログラムには GAMESS を用いています。

## S<sub>0</sub> 状態の最小エネルギー構造を探索する

はじめに、エチレンの S<sub>0</sub> 状態で最も小さなポテンシャルエネルギーを持つ構造（最小エネルギー構造）を探索してみましょう。最小エネルギー構造を探索する計算のことを**構造最適化**（Geometery Optimization）と呼びます。GAMESS の入力ファイルは次のようになります。電子状態の計算には、密度汎関数（DFT）法を用いています。汎関数は BHHLYP、基底関数には 6-31G(d) です。

```
 $CONTRL
    RUNTYP=OPTIMIZE
    SCFTYP=RHF
    DFTTYP=BHHLYP
    ICHARG=0
    MULT=1
    COORD=UNIQUE
 $END
 $STATPT
    NSTEP=100
 $END
 $SYSTEM
    MWORDS=128
 $END
 $BASIS
    GBASIS=N31 NGAUSS=6 NDFUNC=1
 $END
 $DATA
C2H4
C1 1
C 6  0.00000000  0.00000000  0.00000000
C 6 -1.31000000  0.00000000  0.00050000
H 1 -1.85000000 -0.93530000 -0.00050000
H 1 -1.85000000  0.93530000  0.00180000
H 1  0.54000000  0.93530000  0.00100000
H 1  0.54000000 -0.93530000  0.00320000
 $END
```

得られた出力ファイルは[こちら](https://raw.githubusercontent.com/yamnor/yamlab-note/main/ethylene-conical/optimize.log)。Python というプログラミング言語を用いて、出力ファイルから、構造最適化に伴う全エネルギーの変化を読み取って、グラフとして表してみましょう。Google Colab というサービスを使って、出力ファイルの解析を実際に実行してみることができます。

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yamnor/yamlab-note/blob/main/ethylene-conical/optimize.ipynb)

## S<sub>0</sub> → S<sub>n</sub> 励起状態のエネルギーを解析する（TD-DFT法）

```
 $CONTRL
    RUNTYP=ENERGY
    TDDFT=EXCITE
    SCFTYP= RHF
    DFTTYP=BHHLYP
    ICHARG=0
    MULT=1
    COORD=UNIQUE
 $END
 $TDDFT
    NSTATE=5
 $END
 $SYSTEM
    MWORDS=128
 $END
 $BASIS
    GBASIS=N31 NGAUSS=6 NDFUNC=1
 $END
 $DATA
C2H4
C1 1
C 6  0.00000000  0.00000000  0.00000000
C 6 -1.32029884  0.00001048 -0.00023551
H 1 -1.88916197 -0.91657892 -0.00030266
H 1 -1.88916423  0.91658755 -0.00032712
H 1  0.56876188  0.91665251  0.00010461
H 1  0.56877229 -0.91667652  0.00003663
 $END
```

## S<sub>1</sub> 状態の最小エネルギー構造を探索する

```
 $CONTRL
    RUNTYP=OPTIMIZE
    TDDFT=EXCITE
    SCFTYP= RHF
    DFTTYP=BHHLYP
    ICHARG=0
    MULT=1
    COORD=UNIQUE
 $END
 $STATPT
    NSTEP=100
 $END
 $TDDFT
    NSTATE=3
    IROOT=1
 $END
 $SYSTEM
    MWORDS=128
 $END
 $BASIS
    GBASIS=N31 NGAUSS=6 NDFUNC=1
 $END
 $DATA
C2H4
C1 1
C 6  0.00000000  0.00000000  0.00000000
C 6 -1.32029900  0.00001000 -0.00023600
H 1 -1.88916200 -0.91657900 -0.00030300
H 1 -1.88916400  0.91658700 -0.00032700
H 1  0.56876200  0.91665200  0.00010500
H 1  0.56877200 -0.91667600  0.00003700
 $END
```

## S<sub>0</sub> → S<sub>n</sub> 励起状態のエネルギーを解析する（スピン反転 TD-DFT 法）

```
 $CONTRL
    RUNTYP=ENERGY
    TDDFT=SPNFLP
    SCFTYP=ROHF
    DFTTYP=BHHLYP
    ICHARG=0
    MULT=3
    COORD=UNIQUE
 $END
 $SYSTEM
    MWORDS=128
 $END
 $BASIS
    GBASIS=N31 NGAUSS=6 NDFUNC=1
 $END
 $TDDFT
    NSTATE=3
    TAMMD=.T.
 $END
 $DATA
C2H4
C1 1
C 6  0.00000000  0.00000000  0.00000000
C 6 -1.39387838 -0.02023373  0.05708308
H 1 -2.02405816 -0.69695202  0.61316012
H 1 -1.99290168  0.59587111 -0.65717732
H 1  0.70303672  0.57666607  0.58418458
H 1  0.52478100 -0.71663905 -0.63734377
 $END
```

## S<sub>0</sub>/S<sub>1</sub> 状態間の円錐交差構造を探索する

```
 $CONTRL
    RUNTYP=CONICAL
    TDDFT=SPNFLP
    SCFTYP=ROHF
    DFTTYP=BHHLYP
    ICHARG=0
    MULT=3
    COORD=UNIQUE
 $END
 $SYSTEM
    MWORDS=128
 $END
 $BASIS
    GBASIS=N31 NGAUSS=6 NDFUNC=1
 $END
 $CONICL
    IXROOT(1)=1,3
 $END
 $TDDFT
    NSTATE=3
    TAMMD=.T.
 $END
 $DATA
C2H4
C1 1
C 6  0.00000000  0.00000000  0.00000000
C 6 -1.39387800 -0.02023400  0.05708300
H 1 -2.02405800 -0.69695200  0.61316000
H 1 -1.99290200  0.59587100 -0.65717700
H 1  0.70303700  0.57666600  0.58418500
H 1  0.52478100 -0.71663900 -0.63734400
 $END
```

```
 $CONTRL
    RUNTYP=CONICAL
    TDDFT=SPNFLP
    SCFTYP=ROHF
    DFTTYP=BHHLYP
    ICHARG=0
    MULT=3
    COORD=UNIQUE
 $END
 $SYSTEM
    MWORDS=128
 $END
 $BASIS
    GBASIS=N31 NGAUSS=6 NDFUNC=1
 $END
 $CONICL
    IXROOT(1)=2,3
 $END
 $TDDFT
    NSTATE=3
    TAMMD=.T.
 $END
 $DATA
C2H4
C1 1
C 6  0.00000000  0.00000000  0.00000000
C 6 -1.33723876 -0.30093935  0.13764264
H 1 -1.78391968 -1.11038634  0.75036359
H 1 -2.09508746  0.14953750 -0.49933204
H 1  0.61067046 -0.03539838  0.90583015
H 1  0.14815501 -1.10406642 -0.28386137
 $END
```
