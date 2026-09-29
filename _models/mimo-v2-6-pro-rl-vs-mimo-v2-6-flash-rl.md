---
layout: page
title: "MiMo-V2.6-Pro-RL vs MiMo-V2.6-Flash-RL"
description: "Config and weight-storage diff of MiMo-V2.6-Pro-RL vs MiMo-V2.6-Flash-RL, read from each model's config.json and safetensors headers, pinned by commit."
---

absent = the key is not in that model's config.json (the model then uses its library default).

## Changed fields

field | MiMo-V2.6-Pro-RL | MiMo-V2.6-Flash-RL
---|---|---
bytes | 566027990784 | 172923364096
hidden_size | 6144 | 4096
hybrid_layer_pattern | 10 × 0, 60 × 1 | 9 × 0, 39 × 1
moe_layer_freq | 1 × 0, 69 × 1 | 1 × 0, 47 × 1
n_routed_experts | 384 | 256
num_attention_heads | 128 | 64
num_hidden_layers | 70 | 48
num_key_value_heads | 8 | 4
num_nextn_predict_layers | absent | 3
swa_num_attention_heads | 128 | 64

## Key unchanged fields

field | value
---|---
head_dim | 192
max_position_embeddings | 1048576
moe_intermediate_size | 2048
n_shared_experts | null
num_experts_per_tok | 8
rope_theta | 10000000
sliding_window | 128
swa_head_dim | 192
swa_num_key_value_heads | 8
swa_rope_theta | 10000
swa_v_head_dim | 128
tie_word_embeddings | false
v_head_dim | 128
vocab_size | 152576

## Other differing config keys

field | MiMo-V2.6-Pro-RL | MiMo-V2.6-Flash-RL
---|---|---
attention_value_scale | 0.612 | 0.707
audio_config.out_hidden_size | 6144 | 4096
layernorm_epsilon | 1e-05 | 1e-06
vision_config.out_hidden_size | 6144 | 4096

Note: vision_config and audio_config present in config.json -- the stored-element and byte counts below include those encoders, not the text model alone.

## Dtype / quantization notes

**MiMo-V2.6-Pro-RL:**

- "activation_scheme": "dynamic"
- "fmt": "e4m3"
- "mxfp4_block_size": 32
- "quant_method": "fp8"
- "store_dtype": "mxfp4"
- computed: dtype BF16: 10,647,286,656 stored elements, 21,294,573,312 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: dtype F32: 857,088 stored elements, 3,428,352 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: dtype F8_E4M3: 13,378,781,184 stored elements, 13,378,781,184 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: dtype U8: 531,351,207,936 stored elements, 531,351,207,936 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: sum of prod(shape) over 160,040 tensors in 130 safetensors shard(s) = 555,378,132,864 stored elements. Not a parameter count: config.json declares a quantization_config; 79488 weight tensor(s) are U8/I8 with a matching *_scale companion (packed storage); config.json declares a vision_config; config.json declares an audio_config.

**MiMo-V2.6-Flash-RL:**

- "activation_scheme": "dynamic"
- "fmt": "e4m3"
- "mxfp4_block_size": 32
- "quant_method": "fp8"
- "store_dtype": "mxfp4"
- computed: dtype BF16: 4,101,308,032 stored elements, 8,202,616,064 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: dtype F32: 248,192 stored elements, 992,768 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: dtype F8_E4M3: 3,859,808,256 stored elements, 3,859,808,256 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: dtype U8: 160,859,947,008 stored elements, 160,859,947,008 bytes (from each tensor's data_offsets span in the safetensors header)
- computed: sum of prod(shape) over 73,081 tensors in 65 safetensors shard(s) = 168,821,311,488 stored elements. Not a parameter count: config.json declares a quantization_config; 36096 weight tensor(s) are U8/I8 with a matching *_scale companion (packed storage); config.json declares MTP draft layers; config.json declares a vision_config; config.json declares an audio_config.

## Sources

- [MiMo-V2.6-Pro-RL config.json](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Pro-RL/resolve/73875d00b30a89ef8cc353a0b60b0e9f9561952d/config.json) -- as of commit 73875d0, fetched 2026-09-29
- [MiMo-V2.6-Flash-RL config.json](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Flash-RL/resolve/5711b268169967567844e1e560e8a3966da959b1/config.json) -- as of commit 5711b26, fetched 2026-09-29
