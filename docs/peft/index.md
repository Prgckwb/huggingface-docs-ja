<!--Copyright 2023 The HuggingFace Team. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with
the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on
an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.

⚠️ このファイルはMarkdownですが、Markdownビューアで適切にレンダリングされない可能性のある、ドキュメントビルダー（MDXに類似）用の特定の構文が含まれています。

-->

# PEFT

🤗 PEFT（Parameter-Efficient Fine-Tuning）は、大規模な事前学習済みモデルを、すべてのパラメータをファインチューニングすることなく、さまざまなダウンストリームアプリケーションに効率的に適応させるためのライブラリです。すべてのパラメータをファインチューニングすることは非常にコストがかかるためです。PEFTメソッドは、少数の（追加の）モデルパラメータのみをファインチューニングすることで、計算コストと保存コストを大幅に削減しながら、完全にファインチューニングされたモデルに匹敵するパフォーマンスを実現します。これにより、大規模な言語モデル（LLM）を一般的なハードウェアで学習および保存することがより容易になります。

PEFTは、Transformers、Diffusers、Accelerateライブラリと統合されており、大規模なモデルを推論のためにより高速かつ容易に読み込み、学習、使用することができます。

<div class="mt-10">
 <div class="w-full flex flex-col space-y-4 md:space-y-0 md:grid md:grid-cols-2 md:gap-y-4 md:gap-x-5">
   <a class="!no-underline border dark:border-gray-700 p-5 rounded-lg shadow hover:shadow-lg" href="quicktour"
     ><div class="w-full text-center bg-gradient-to-br from-blue-400 to-blue-500 rounded-lg py-1.5 font-semibold mb-5 text-white text-lg leading-relaxed">始めましょう</div>
     <p class="text-gray-700">🤗 PEFTを初めて使用する方は、ここから始めてライブラリの主な機能の概要と、PEFTメソッドを使用してモデルを学習する方法を確認してください。</p>
   </a>
   <a class="!no-underline border dark:border-gray-700 p-5 rounded-lg shadow hover:shadow-lg" href="./task_guides/image_classification_lora"
     ><div class="w-full text-center bg-gradient-to-br from-indigo-400 to-indigo-500 rounded-lg py-1.5 font-semibold mb-5 text-white text-lg leading-relaxed">ハウツーガイド</div>
     <p class="text-gray-700">画像分類、因果言語モデリング、自動音声認識など、さまざまなタイプのタスクにわたるさまざまなPEFTメソッドの適用方法を示す実用的なガイドです。DeepSpeedおよびFully Sharded Data Parallelスクリプトで🤗 PEFTを使用する方法を学びます。</p>
   </a>
   <a class="!no-underline border dark:border-gray-700 p-5 rounded-lg shadow hover:shadow-lg" href="./conceptual_guides/lora"
     ><div class="w-full text-center bg-gradient-to-br from-pink-400 to-pink-500 rounded-lg py-1.5 font-semibold mb-5 text-white text-lg leading-relaxed">概念ガイド</div>
     <p class="text-gray-700">LoRAおよびさまざまなソフトプロンプティング手法が、学習をより効率的にするために、学習可能なパラメータの数をどのように削減するのかについて、理論的な理解を深めます。</p>
  </a>
   <a class="!no-underline border dark:border-gray-700 p-5 rounded-lg shadow hover:shadow-lg" href="./package_reference/config"
     ><div class="w-full text-center bg-gradient-to-br from-purple-400 to-purple-500 rounded-lg py-1.5 font-semibold mb-5 text-white text-lg leading-relaxed">リファレンス</div>
     <p class="text-gray-700">🤗 PEFTのクラスとメソッドの動作に関する技術的な説明。</p>
   </a>
 </div>
</div>

<iframe
src="https://stevhliu-peft-methods.hf.space"
frameborder="0"
width="850"
height="620"
></iframe>