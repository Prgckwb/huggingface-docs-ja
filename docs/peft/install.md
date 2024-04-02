<!--Copyright 2023 The HuggingFace Team. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with
the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on
an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.

⚠️ このファイルはMarkdownですが、Markdownビューアで適切にレンダリングされない可能性のある、ドキュメントビルダー（MDXに類似）用の特定の構文が含まれています。

-->

# インストール

開始する前に、環境を設定し、適切なパッケージをインストールし、🤗 PEFTを構成する必要があります。🤗 PEFTは**Python 3.8以降**でテストされています。

🤗 PEFTは、PyPIおよびGitHubで利用可能です。

## PyPI

PyPIから🤗 PEFTをインストールするには：

```bash
pip install peft
```

## ソース
リリースされていない新機能は毎日追加されており、いくつかのバグが含まれている可能性もあります。それらを試すには、GitHubリポジトリからインストールします：
    
```bash
pip install git+https://github.com/huggingface/peft
```

ライブラリへの貢献に取り組んでいる場合、またはソースコードを使用してコードを実行しながらライブ結果を確認したい場合は、ローカルにクローンしたバージョンのリポジトリから編集可能なバージョンをインストールできます：
```bash
git clone https://github.com/huggingface/peft
cd peft
pip install -e .
```
