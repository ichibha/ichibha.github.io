---
layout: none
title: Quantum ESPRESSO インストール・エラー対応まとめ
---

# Quantum ESPRESSO インストール・エラー対応まとめ （2025年8月19日更新分）

## 1. Windows（WSL2 / Ubuntu）でのインストール

### 1.1 Ubuntuバージョンによる違い

* **Ubuntu 24.04** を使用するとエラーが発生（参考: [GitLab issue #684](https://gitlab.com/QEF/q-e/-/issues/684)）。
* **Ubuntu 22.04.5** を使用すれば `apt install quantum-espresso` で正常に動作。ビルド不要のため最も簡単な方法。

### 1.2 ソースコードからのビルド（Windows / WSL2）

* 参考にしたバージョン: **QE 7.3.1**
* インストール手順例:

```bash
brew install gcc fftw openblas open-mpi

wget https://www.quantum-espresso.org/rdm-download/488/v7-3-1/4374c51f0d38b1be967fcf2a3bb298ef/qe-7.3.1-ReleasePack.tar.gz
tar xvf qe-7.3.1-ReleasePack.tar.gz
cd qe-7.3.1
```

#### 修正作業

`./external/mbd/src/mbd_c_api.F90` 内の `f_c_string` を `f2c_string` に置換。

* `call f_c_string(...)` → `call f2c_string(...)`
* `subroutine f_c_string(...)` → `subroutine f2c_string(...)`

（理由: `USE` で持ち込んだモジュールに同名の `f_c_string` が存在するため、衝突を回避）

#### コンパイル

```bash
./configure MPIF90=mpif90 FC=gfortran-15 MPICC=mpicc CC=gcc-15 CPP=cpp-15 FFLAGS="-I/usr/local/include/ -fallow-argument-mismatch"

make pwall
```

---

## 2. Mac でのインストール

### 2.1 ソースビルド

* gfortran と Mac のチップとの互換性で問題が発生しやすい。
* `configure` 時の対処例:

```bash
./configure FC=mpif90 CC=mpicc CPP=cpp FFLAGS="-I/usr/local/include/ -fallow-argument-mismatch"
```

* その後 `make.inc` を編集し、

  ```text
  CPP=cpp
  ```

  を

  ```text
  CPP=gcc -E
  ```

  に変更するとエラーを回避可能。

### 2.2 代替手段

* **conda-forge** からプリコンパイル済みバイナリをインストール可能:

```bash
conda install -c conda-forge mamba
mamba install -c conda-forge qe=7.4
```

### 2.3 初回実行時の注意（Mac）

* `pw.x` などを初めて実行すると macOS によってブロックされる。

  * 「完了」→「システム設定 > プライバシーとセキュリティ > このまま許可」→再実行で「このまま実行」→以降は常時実行可能。

---

## 3. 実行時エラーと対処法

### 3.1 Ubuntu 24.04での実行エラー（例）

```
Program received signal SIGABRT: Process abort signal.
Backtrace for this error:
...
```

* **解決策**: Ubuntu 22.04 にダウングレード。

### 3.2 `pw.x < nscf_dos.in` 実行時のエラー

```
Error in routine tetra_init (13): cannot remap grid on k-point list
```

* **解決策**: `nscf_dos.in` の `&SYSTEM` セクションに以下を追記:

```fortran
nosym = .true.
noinv = .true.
```

---

## 4. Quantum ESPRESSO バージョン取得方法（過去分）

* **7.3.1**

  ```bash
  wget https://www.quantum-espresso.org/rdm-download/488/v7-3-1/4374c51f0d38b1be967fcf2a3bb298ef/qe-7.3.1-ReleasePack.tar.gz
  ```
* **7.2**

  ```bash
  wget https://www.quantum-espresso.org/rdm-download/488/v7-2/4374c51f0d38b1be967fcf2a3bb298ef/qe-7.2-ReleasePack.tar.gz
  ```
```

  ```bash
  wget https://www.quantum-espresso.org/rdm-download/488/v6-8/4374c51f0d38b1be967fcf2a3bb298ef/qe-6.8-ReleasePack.tgz
  ```
* **6.7**

  ```bash
  wget https://www.quantum-espresso.org/rdm-download/488/v6-7/4374c51f0d38b1be967fcf2a3bb298ef/qe-6.7-ReleasePack.tgz
  ```
