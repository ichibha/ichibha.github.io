# Q&A

**Q1.** SCF計算で以下のエラーが発生します：

```text
Error in routine set_dft_from_name (1): unrecognized dft
```

**A1.** 入力ファイルの `&system` セクションに次の行を追加してください：

```fortran
input_dft = "PBE"
```

これにより DFT 汎関数が正しく認識され、SCF 計算が実行されます。

**Q2.** nscf計算で以下のエラーが発生します：

```text
Error in routine tetra_init (13): cannot remap grid on k-point list
```

**A2.** `&system` セクションに以下を追加してください：

```fortran
nosym = .true.
noinv = .true.
```


