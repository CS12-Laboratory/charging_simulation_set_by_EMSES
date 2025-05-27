# 帯電シミュレーション by EMSES
太陽風が吹き付ける月面の表面帯電現象シミュレーション用の実験セット

EMSES本体に関しては[MPIEMSES3D Repository](https://github.com/CS12-Laboratory/MPIEMSES3D)から別途ダウンロードする必要あり

**以下のガイドは古いため、随時[EMSES-tutorials](https://github.com/CS12-Laboratory/EMSES-tutorials)を確認すること。**


- [帯電シミュレーション by EMSES](#帯電シミュレーション-by-emses)
  - [内容](#内容)
    - [plasma.inp](#plasmainp)
    - [exp\_surface](#exp_surface)
      - [パラメータ:](#パラメータ)
    - [exp\_hole](#exp_hole)
      - [パラメータ:](#パラメータ-1)
    - [exp\_surface\_with\_pe](#exp_surface_with_pe)
      - [パラメータ:](#パラメータ-2)
  - [その他必要なもの](#その他必要なもの)
    - [emses3d\_ohhelp20](#emses3d_ohhelp20)
    - [Anaconda(python環境)のcamphorへのロード(準備 1.で実施)](#anacondapython環境のcamphorへのロード準備-1で実施)
  - [TODO](#todo)
    - [準備](#準備)
      - [1. スパコン(camphor)の基本環境設定](#1-スパコンcamphorの基本環境設定)
      - [2. EMSESのビルド](#2-emsesのビルド)
      - [3. ビルドしたEMSESをシミュレーションフォルダーにコピー](#3-ビルドしたemsesをシミュレーションフォルダーにコピー)
      - [4. 各シミュレーションフォルダ内のjobスクリプトの権限変更(必要ない可能性あり)](#4-各シミュレーションフォルダ内のjobスクリプトの権限変更必要ない可能性あり)
      - [5. 各シミュレーションフォルダ内のjobスクリプトの変更(下記ツール(camptools)を用いる場合必要なし)](#5-各シミュレーションフォルダ内のjobスクリプトの変更下記ツールcamptoolsを用いる場合必要なし)
      - [6. (便利ツール集のインストール)](#6-便利ツール集のインストール)
    - [実行](#実行)
      - [下記ツール(camptools)を用いる場合](#下記ツールcamptoolsを用いる場合)
    - [解析](#解析)
    - [発展](#発展)
  - [便利ツール集](#便利ツール集)
    - [Camphor上でのコマンドツール: camptools ( https://github.com/Nkzono99/camptools )](#camphor上でのコマンドツール-camptools--httpsgithubcomnkzono99camptools-)
    - [EMSESパラメータファイル生成ツール: emses\_inp\_generator ( https://github.com/Nkzono99/emses\_inp\_generator )](#emsesパラメータファイル生成ツール-emses_inp_generator--httpsgithubcomnkzono99emses_inp_generator-)
    - [EMSESシミュレーション結果可視化ツール: emout ( https://github.com/Nkzono99/emout )](#emsesシミュレーション結果可視化ツール-emout--httpsgithubcomnkzono99emout-)
- [その他](#その他)
  - [EMSESシミュレーションを実行後、その結果を用いて追加stepのシミュレーションを行う](#emsesシミュレーションを実行後その結果を用いて追加stepのシミュレーションを行う)
  - [Particle-in-cell(PIC)法について](#particle-in-cellpic法について)
  - [京大スーパーコンピューターシステムのマニュアル](#京大スーパーコンピューターシステムのマニュアル)

## 内容
### plasma.inp
EMSESで用いるパラメータファイルの解説

### exp_surface
太陽風が吹き付ける月面環境のシミュレーションフォルダー

#### パラメータ:
- グリッド幅: 0.5m
- シミュレーションサイズ: 32x32x256 grid
- プラズマ密度: 5/cc
- プラズマ流速度: 400km/s
- 熱速度: 10eV
- 月面高度: 60grid

```
|   ↓   ↓   ↓  | 256grid
|              |
|              |
|              |
|              |
|              |
|              |
|              |
|              |
|--------------| 60grid
|              |
|              |
|              |
---------------- 0grid
```

### exp_hole
太陽風が吹き付ける空洞(幅10x10grid, 深さ40grid)有り月面環境のシミュレーションフォルダー
設定してあるシミュレーションパラメータでは状態が収束しないため、step数を追加する必要あり

#### パラメータ:
- グリッド幅: 0.5m
- シミュレーションサイズ: 32x32x256 grid
- プラズマ密度: 5/cc
- プラズマ流速度: 400km/s
- 熱速度: 10eV
- 月面高度: 60grid

```
|   ↓   ↓   ↓  | 256grid
|              |
|              |
|              |
|              |
|              |
|              |
|              |
|    10grid    |
|-----    -----| 60grid
|    |    |    |
|    |    |    |
|    |----|    | 20grid
---------------- 0grid
```

### exp_surface_with_pe
太陽風が吹き付け、かつ、太陽光照射による光電子放出が存在する月面環境のシミュレーションフォルダー

#### パラメータ:
- グリッド幅: 0.5m
- シミュレーションサイズ: 32x32x256 grid
- プラズマ密度: 5/cc
- プラズマ流速度: 400km/s
- 熱速度: 10eV
- 月面高度: 60grid
- 光電子
    + 電流: 4.5 μA/m^2
    + 温度: 2.2eV

```
|   ↓   ↓   ↓  | 256grid
|              |
|              |
|              |
|              |
|              |
|              |
|              |
|       ↑ PE   |
|--------------| 60grid
|              |
|              |
|              |
---------------- 0grid
```

## その他必要なもの

### [MPIEMSES3D](https://github.com/CS12-Laboratory/MPIEMSES3D)
EMSES本体のソースコード

初めにmakeで実行ファイル"mpiemses3d"をビルドすること

使用の際は、ビルドした実行ファイルをシミュレーションフォルダーにコピーし以下のコマンドで実行する。

(実際の使用の際は、jobファイルを作成しスパコンの実行キューに投入する)

```
$ <MPI_EXEC> ./mpiemses3D plasma.inp
※ <MPI_EXEC>: MPI実行コマンド (camphor上では"srun -n <NUMBER_OF_PROCESS>")
```
### Anaconda(python環境)のcamphorへのロード(準備 1.で実施)

```
module load anaconda3/2019.10
```

## TODO
EMSESのビルド及び各シミュレーションを実行しその結果の可視化を行う.

### 準備
#### 1. スパコン(camphor)の基本環境設定
スパコン(camphor)にログイン時に読み込まれる~/.bashrcという設定ファイルをcharging_simulation_set_by_emses/.bashrcで置き換える。

ただしすでに~/.bashrcを修正している場合は、"# User specific aliases and functions"以下を既存の.bashrc下部に追記すること。

また.bashrcを置き換えたあとはスパコンに再ログインすること。

```
$ mv ~/.bashrc ~/.bashrc_old
$ cp <charging_simulation_set_by_emses>/.bashrc ~/.bashrc
$ chmod 640 ~/.bashrc
```

再ログイン後、EMSES出力ファイルを読み取るためのライブラリをインストールする。

```
$ pip install --user h5py
```

#### 2. EMSESのビルド

```
$ cd mpiemses3d_ohhelp20
$ make clean
$ make
$ chmod 500 mpiemses3D
```

#### 3. ビルドしたEMSESをシミュレーションフォルダーにコピー

```
$ cp mpiemses3d_ohhelp20/mpiemses3D <simulation-folder>/
```

#### 4. 各シミュレーションフォルダ内のjobスクリプトの権限変更(必要ない可能性あり)

```
$ chmod 700 <simulation-folder>/job.sh
```

#### 5. 各シミュレーションフォルダ内のjobスクリプトの変更(下記ツール(camptools)を用いる場合必要なし)
```
$ vim <simulation-folder>/job.sh
使用するコア数等を適宜変更する(使用するプロセス数はplasma.inp内のnodes(:)の総積)
```

#### 6. (便利ツール集のインストール)
任意でページ下部にあるツール集をインストールする。

### 実行
```    
$ cd <simulation-folder>
$ qsub job.sh
```

#### 下記ツール(camptools)を用いる場合
```
$ myqsub job.sh -d <simulation-folder>
myqsubコマンドは、シミュレーションフォルダー内のplasma.inpからnodes(:)を読み取りjob.shを書き換えたmyjob.shを作成し、myjob.shをジョブキューに投入する
```
    
### 解析
- paraviewを用いる場合
    + .h5と.xdmfをダウンロードし、paraviewにロードする
      (私はやり方を知らないが、ダウンロードをせずに解析することもできるらしい？)

- pythonを用いる場合
  + ローカルで可視化する
    .h5をダウンロードし、h5pyやmatplotlib等を用いて可視化
  
  + スパコン上で可視化する
    jupyter lab上でh5pyやmatplotlib等を用いて可視化
    \$ jupyter lab --port <port-number>
  
  + (ライブラリemoutを用いて可視化)
    emoutというライブラリをインストールすればpython上での簡単な可視化が可能
    
    * インストール
    ```
    $ pip install --user emout
    ```
    
    * 実行
    ```
    $ cd <simulation-folder>
    $ python
    $ python> import emout
    $ python> data = emout.Emout('./')
    $ python> data.phisp[istep, zlower:zupper, y, xlower:xupper].plot() # phisp.h5の二次元面を表示する場合
    $ python> data.phisp[istep, zlower:zupper, y, xlower:xupper].plot(savefilename='phisp.png') # phisp.h5の二次元面を保存する場合
    $ python> data.phisp[istep, zlower:zupper, y, x].plot() # phisp.h5のある一次元上を表示する場合
    ```


### 発展
    - 太陽風パラメータを変更(wp, vdri, p[ae]th, vdthz, vdthxy等)
    - 光電子パラメータを変更(wp(3), p[ae]th(3), curf(3)等)
    - 空洞形状パラメータを変更([xyz]lrechole, [xyz]urechole, [xyz]mine, [xyz]maxe等)
    - EMSESソースコードとPICアルゴリズムの対応を確認(esses.F90等)

## 便利ツール集
### Camphor上でのコマンドツール: camptools ( https://github.com/Nkzono99/camptools )
よく使う操作をまとめたコマンドツール

インストール
```
$ pip install --user camptools
```
    
### EMSESパラメータファイル生成ツール: emses_inp_generator ( https://github.com/Nkzono99/emses_inp_generator )
SI単位系からEMSES単位系に変換・plamsa.inpの生成を行うツール

インストール
  URLからローカルにダウンロード後以下のコマンドを実行
  ```
    $ pip install --user -r requirements.txt
  ```
    
実行 (以下のどれかを実行)
 
 - batファイルをダブルクリック
 - ```\$ ./inpgen.bat```
 - ```\$ python src/main.py```

### EMSESシミュレーション結果可視化ツール: emout ( https://github.com/Nkzono99/emout )

インストール
```
    $ pip install --user emout
```




# その他
## EMSESシミュレーションを実行後、その結果を用いて追加stepのシミュレーションを行う
- 継続ジョブの実行
```
$ mkdir <simulation-folder-new>
$ cd <simulation-folder-new>
$ cp ../<simulation-folder-old>/plasma.inp ./
$ cp ../<simulation-folder-old>/generate_xdmf.py ./
$ cp ../<simulation-folder-old>/mpiemses3d ./
$ cp ../<simulation-folder-old>/job.sh ./
$ ln -s ../<simulation-folder-old>/SNAPSHOT1 ./SNAPSHOT0

$ vim plasma.inp
plasma.inp内のjobnum(1:2)を以下のように修正
  jobnum(1:2) = 1, 1

$ "qsub job.sh" または "myqsub job.sh"
```
    
- ツール (in "camptools") を使った継続ジョブ実行
```
    $ extentsim <simulation-folder-old> <simulation-folder-new> -n <number-of-additional-step> --run
```

## Particle-in-cell(PIC)法について
松本紘,臼井英之,杉山徹,大村善治. n.d. “粒子シミュレーション法 概説.” http://www.astro.phys.s.chiba-u.ac.jp/netlab/summer-school/TEXTBOOK/text6.pdf.

内藤裕志, and 佐竹真介. 2013. “粒子シミュレーションのコーディング技法.” プラズマ・核融合学会誌 = Journal of Plasma and Fusion Research 89 (4): 245–60.

Lapenta, Giovanni. n.d. Particle In Cell Methods With Application to Simulations in Space Weather.

## 京大スーパーコンピューターシステムのマニュアル
https://web.kudpc.kyoto-u.ac.jp/manual/ja
