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

