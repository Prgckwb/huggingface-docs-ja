# クイックツアー

PEFTは、大規模な事前学習済みモデルを微調整するための、パラメータ効率の良い手法を提供します。従来のパラダイムでは、下流のタスクごとにモデルのすべてのパラメータを微調整しますが、今日のモデルには膨大な数のパラメータがあるため、これは非常にコストがかかり、現実的ではなくなっています。代わりに、プロンプトパラメータの数を少なくしたり、低ランク適応（LoRA）などの再パラメータ化手法を使用したりして、学習可能なパラメータの数を減らすのがより効率的です。

このクイックツアーでは、PEFTの主な機能と、一般的には消費者デバイスでアクセスできない大規模モデルでのトレーニングや推論の実行方法を紹介します。

## トレーニング

各PEFT手法は、[`PeftModel`]を構築するための重要なパラメータをすべて格納する[`PeftConfig`]クラスで定義されます。例えば、LoRAでトレーニングするには、[`LoraConfig`]クラスをロードして作成し、以下のパラメータを指定します。

- `task_type`: トレーニングするタスク（この場合はsequence-to-sequenceの言語モデリング）。
- `inference_mode`: 推論用にモデルを使用するかどうか。
- `r`: 低ランク行列の次元。
- `lora_alpha`: 低ランク行列のスケーリング係数。
- `lora_dropout`: LoRA層のドロップアウト確率。

```python
from peft import LoraConfig, TaskType

peft_config = LoraConfig(task_type=TaskType.SEQ_2_SEQ_LM, inference_mode=False, r=8, lora_alpha=32, lora_dropout=0.1)
```

<Tip>
ターゲットとするモジュールやバイアスの種類など、調整可能な他のパラメータの詳細については、[LoraConfig]のリファレンスを参照してください。

</Tip>
[LoraConfig]の設定が完了したら、[get_peft_model]関数を使用して[PeftModel]を作成します。この関数は、Transformersライブラリからロードできるベースモデルと、LoRAを使用したトレーニング用にモデルを設定する方法のパラメータを含む[LoraConfig]を受け取ります。

微調整したいベースモデルをロードします。

```python
from transformers import AutoModelForSeq2SeqLM

model = AutoModelForSeq2SeqLM.from_pretrained("bigscience/mt0-large")
```

ベースモデルとpeft_configを[get_peft_model]関数でラップして、[PeftModel]を作成します。モデルの学習可能なパラメータ数を把握するには、[print_trainable_parameters]メソッドを使用します。

```python
from peft import get_peft_model

model = get_peft_model(model, peft_config)
model.print_trainable_parameters()
"output: trainable params: 2359296 || all params: 1231940608 || trainable%: 0.19151053100118282"
```

bigscience/mt0-largeの12億のパラメータのうち、学習しているのはわずか0.19%です！

これで完了です🎉！これで、Transformersの[~transformers.Trainer]、Accelerate、またはカスタムPyTorchトレーニングループを使用してモデルをトレーニングできます。

例えば、[~transformers.Trainer]クラスでトレーニングするには、いくつかのトレーニングハイパーパラメータを指定して[~transformers.TrainingArguments]クラスを設定します。

```python
training_args = TrainingArguments(
    output_dir="your-name/bigscience/mt0-large-lora",
    learning_rate=1e-3,
    per_device_train_batch_size=32,
    per_device_eval_batch_size=32,
    num_train_epochs=2,
    weight_decay=0.01,
    evaluation_strategy="epoch",
    save_strategy="epoch",
    load_best_model_at_end=True,
)
```

モデル、トレーニング引数、データセット、トークナイザー、その他必要なコンポーネントを[~transformers.Trainer]に渡し、[~transformers.Trainer.train]を呼び出してトレーニングを開始します。

```python
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_datasets["train"],
    eval_dataset=tokenized_datasets["test"],
    tokenizer=tokenizer,
    data_collator=data_collator,
    compute_metrics=compute_metrics,
)

trainer.train()
```

### モデルの保存
モデルのトレーニングが終了したら、[~transformers.PreTrainedModel.save_pretrained]関数を使用してモデルをディレクトリに保存できます。

```python
model.save_pretrained("output_dir")
```

また、[~transformers.PreTrainedModel.push_to_hub]関数を使用して、モデルをHub（最初にHugging Faceアカウントにログインしていることを確認してください）に保存することもできます。

```python
from huggingface_hub import notebook_login

notebook_login()
model.push_to_hub("your-name/bigscience/mt0-large-lora")
```

どちらの方法でも、トレーニングされた追加のPEFTウェイトのみが保存されるため、非常に効率的に保存、転送、ロードできます。例えば、LoRAでトレーニングされたこのfacebook/opt-350mモデルには、adapter_config.jsonとadapter_model.safetensorsの2つのファイルしか含まれていません。adapter_model.safetensorsファイルはわずか6.3MBです！

<div class="flex flex-col justify-center"> <img src="https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/peft/PEFT-hub-screenshot.png"/> <figcaption class="text-center">Hubに保存されているopt-350mモデルのアダプターウェイトは、モデルウェイト全体のサイズ（~700MBになる可能性がある）と比較して~6MBしかありません。</figcaption> </div>

### 推論
<Tip>
利用可能なAutoPeftModelクラスの完全なリストについては、AutoPeftModel APIリファレンスを参照してください。

</Tip>
[AutoPeftModel]クラスと[~transformers.PreTrainedModel.from_pretrained]メソッドを使用すると、PEFTでトレーニングされたモデルを簡単にロードして推論に使用できます。

```python
from peft import AutoPeftModelForCausalLM
from transformers import AutoTokenizer
import torch

model = AutoPeftModelForCausalLM.from_pretrained("ybelkada/opt-350m-lora")
tokenizer = AutoTokenizer.from_pretrained("facebook/opt-350m")

model = model.to("cuda")
model.eval()
inputs = tokenizer("Preheat the oven to 350 degrees and place the cookie dough", return_tensors="pt")

outputs = model.generate(input_ids=inputs["input_ids"].to("cuda"), max_new_tokens=50)
print(tokenizer.batch_decode(outputs.detach().cpu().numpy(), skip_special_tokens=True)[0])

"Preheat the oven to 350 degrees and place the cookie dough in the center of the oven. In a large bowl, combine the flour, baking powder, baking soda, salt, and cinnamon. In a separate bowl, combine the egg yolks, sugar, and vanilla."
```

自動音声認識など、AutoPeftModelForクラスで明示的にサポートされていない他のタスクでも、基本の[AutoPeftModel]クラスを使用してタスクのモデルをロードできます。

```python
from peft import AutoPeftModel

model = AutoPeftModel.from_pretrained("smangrul/openai-whisper-large-v2-LORA-colab")
```

### 次のステップ
PEFT手法の1つを使用してモデルをトレーニングする方法を見てきましたが、プロンプトチューニングなどの他の手法も試してみることをお勧めします。手順は、クイックツアーに示されている手順と非常によく似ています。

1. PEFT手法用の[PeftConfig]を準備する。
2. [get_peft_model]メソッドを使用して、設定とベースモデルから[PeftModel]を作成する。
その後、好きなようにトレーニングできます！推論用にPEFTモデルをロードするには、[AutoPeftModel]クラスを使用できます。

セマンティックセグメンテーション、多言語自動音声認識、DreamBooth、トークン分類などの特定のタスクで別のPEFT手法を使用してモデルをトレーニングすることに興味がある場合は、タスクガイドも見てみてください。
