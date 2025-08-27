# Model Merge Configuration

This repository contains a configuration file for merging multiple Llama 3–based models into a single unified model using a **linear merge method**.

## 📄 Configuration

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`  models:    - model: flammenai/Mahou-1.3-llama3-8B      parameters:        weight: 1.0    - model: Danielbrdz/Barcenas-Llama3-8b-ORPO      parameters:        weight: 1.0    - model: Weyaxi/Einstein-v6.1-Llama3-8B      parameters:        weight: 1.0  merge_method: linear  tokenizer_source: union  dtype: float16  `

## ⚙️ Explanation

- **models**A list of the base models to be merged. Each model is defined with its source name and merge weight.

  - flammenai/Mahou-1.3-llama3-8B
  - Danielbrdz/Barcenas-Llama3-8b-ORPO
  - Weyaxi/Einstein-v6.1-Llama3-8B

- **parameters → weight**The relative importance of each model in the merge. Here, all are set to 1.0, meaning equal contribution.
- **merge_method: linear**Uses **linear merging**, averaging model weights based on their assigned importance.
- **tokenizer_source: union**Combines the tokenizers of all included models, ensuring full vocabulary coverage.
- **dtype: float16**The resulting merged model will use half-precision floating point (FP16), reducing memory usage and improving inference speed.

## 🚀 Usage

1. Save the configuration to a file (e.g., `merge_config.yaml`).

2. Run the merge command:
   ```bash
   mergekit-yaml merge_config.yaml --output merged-model


   ```

## 📌 Notes

- Adjust **weights** if you want one model’s style or knowledge to dominate the merged output.
- Using float16 is recommended for inference efficiency, but you can switch to bfloat16 or float32 for training/finetuning.
- The **union tokenizer** prevents token mismatches between models, but can increase vocabulary size.
