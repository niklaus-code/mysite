---
title: 大模型微调
date: 2024-07-17 15:08:03
---

```bash
from datasets import load_dataset, DatasetDict

import os
os.environ["CUDA_VISIBLE_DEVICES"] = "5, 7"
ds_train = load_dataset("/home/ysman/dataset/codeparrot-ds-train", split="train")
ds_valid = load_dataset("/home/ysman/dataset/codeparrot-ds-valid", split="validation")

raw_datasets = DatasetDict(
    {
        "train": ds_train,
        "valid": ds_valid,
    }
)

# print(raw_datasets)

from transformers import AutoTokenizer

#token 的长度128
context_length = 256
tokenizer = AutoTokenizer.from_pretrained("/home/ysman/model/code-search-net-tokenizer")
def tokenize(element):
    outputs = tokenizer(
        element["content"],
        truncation=True,
        max_length=context_length,
        return_overflowing_tokens=True,
        return_length=True,
    )
    input_batch = []
    for length, input_ids in zip(outputs["length"], outputs["input_ids"]):
        if length == context_length:
            input_batch.append(input_ids)
    return {"input_ids": input_batch}
tokenized_datasets = raw_datasets.map(
    tokenize, batched=True, remove_columns=raw_datasets["train"].column_names, num_proc=4
)

from transformers import AutoTokenizer, GPT2LMHeadModel,AutoConfig

config = AutoConfig.from_pretrained(
    "/home/ysman/model/gpt2",
    vocab_size=len(tokenizer),
    n_ctx=context_length,
    bos_token_id=tokenizer.bos_token_id,
    eos_token_id=tokenizer.eos_token_id,
)
model = GPT2LMHeadModel(config)
# model = GPT2LMHeadModel.from_pretrained("/home/ysman/model/gpt2")
# model_size = sum(t.numel() for t in model.parameters())
# print(f"GPT-2 size: {model_size/1000**2:.1f}M parameters")


from transformers import Trainer, TrainingArguments
from transformers import DataCollatorForLanguageModeling
from transformers import Trainer, TrainingArguments
from transformers import DataCollatorForLanguageModeling

tokenizer.pad_token = tokenizer.eos_token
data_collator = DataCollatorForLanguageModeling(tokenizer, mlm=False)
args = TrainingArguments(
    output_dir="codeparrot-ds",
    per_device_train_batch_size=32,
    per_device_eval_batch_size=32,
    evaluation_strategy="steps",
    eval_steps=5_000,
    logging_steps=5_000,
    gradient_accumulation_steps=8,
    num_train_epochs=1,
    weight_decay=0.1,
    warmup_steps=1_000,
    lr_scheduler_type="cosine",
    learning_rate=5e-4,
    save_steps=5_000,
    fp16=True,
    # push_to_hub=True,
)

trainer = Trainer(
    model=model,
    tokenizer=tokenizer,
    args=args,
    data_collator=data_collator,
    train_dataset=tokenized_datasets["train"],
    eval_dataset=tokenized_datasets["valid"],
)

trainer.train()
trainer.save_model("/home/ysman/model/test/")
                                                  
```
