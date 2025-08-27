# 📘 LLM Model Merging Tutorial using MergeKit

This repository demonstrates how to merge multiple **Llama 3–based models** into a single unified model using [**MergeKit**](https://github.com/arcee-ai/mergekit). We’ll walk through installation, configuration, merging, and even running inference on the merged model.

## 🛠️ Installation

First, clone and install MergeKit:

```bash
git clone https://github.com/arcee-ai/mergekit.git
cd mergekit
pip install -e .
```

## 📄 Step 1: Create a Merge Configuration

Create a file called merge_config.yaml with the following contents:

```yaml
models:
  - model: flammenai/Mahou-1.3-llama3-8B
    parameters:
      weight: 1.0
  - model: Danielbrdz/Barcenas-Llama3-8b-ORPO
    parameters:
      weight: 1.0
  - model: Weyaxi/Einstein-v6.1-Llama3-8B
    parameters:
      weight: 1.0
merge_method: linear
tokenizer_source: union
dtype: float16
```

## ⚙️ Step 2: Understand the Configuration

- **models** → A list of base models to merge. Each has a Hugging Face model name and a weight.

  - Example models here:

    - flammenai/Mahou-1.3-llama3-8B
    - Danielbrdz/Barcenas-Llama3-8b-ORPO
    - Weyaxi/Einstein-v6.1-Llama3-8B

- **parameters → weight** → Controls how much each model contributes.

  - 1.0 for all → equal contribution.
  - Example: setting one model to 2.0 doubles its influence.

- **merge_method: linear** → Performs a **linear merge** (weighted average of parameters).
- **tokenizer_source: union** → Combines all tokenizers → ensures vocabulary coverage.
- **dtype: float16** → Saves memory by using FP16 precision.

## 🚀 Step 3: Merge the Models

### Option A: Using the CLI

```bash
   mergekit-yaml merge_config.yaml --output merged-model
```

This will create a new model inside the merged-model/ directory.

### Option B: Using Python API

```python
import yaml

from mergekit.config import MergeConfiguration
from mergekit.merge import MergeOptions, run_merge

# Load configuration
with open("merge_config.yaml", "r", encoding="utf-8") as f:
  merge_config = MergeConfiguration.model_validate(yaml.safe_load(f))
# Run merge
run_merge(
  merge_config,
  out_path="merged-model",
  options=MergeOptions(copy_tokenizer=True, lora_merge_cache="tmp"),
)
print("Merge complete! Model saved at merged-model/")
print("Merge complete! Model saved at merged-model/")
```

## 🧪 Step 4: Test the Merged Model

After merging, load the model with Hugging Face:

```python
from transformers
import AutoModelForCausalLM, AutoTokenizer

model_name = "merged-model"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype="auto")

prompt = "Explain quantum mechanics in simple terms."
inputs = tokenizer(prompt, return_tensors="pt")

outputs = model.generate(**inputs, max_new_tokens=200)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

## 📌 Notes

- Adjust **weights** to prioritize one model’s style/knowledge.
- Use float16 for inference; consider bfloat16 or float32 if finetuning.
- **Union tokenizer** ensures no missing tokens but may increase vocab size.
- CLI is quicker for one-off merges, but Python API is better for automation.
