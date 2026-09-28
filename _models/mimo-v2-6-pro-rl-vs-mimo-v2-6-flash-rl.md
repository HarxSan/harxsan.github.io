# MiMo-V2.6-Pro-RL vs MiMo-V2.6-Flash-RL

## Changed fields

field | MiMo-V2.6-Pro-RL | MiMo-V2.6-Flash-RL
---|---|---
attn_heads | 128 | 64
bytes | 566027990784 | 172923364096
hidden_size | 6144 | 4096
kv_heads | 8 | 4
layers | 70 | 48
moe_experts_routed | 384 | 256
rope_theta | 10000000 | 10000000.0

## Key unchanged fields

field | value
---|---
head_dim | 192
max_position_embeddings | 1048576
moe_active_experts | 8
moe_intermediate_size | 2048
tied_embeddings | False
vocab_size | 152576

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
- computed: sum of prod(shape) over 160,040 tensors in 130 safetensors shard(s) = 555,378,132,864 stored elements. Not a parameter count: config.json declares a quantization_config; 79488 weight tensor(s) are U8/I8 with a matching *_scale companion (packed storage).

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
- computed: sum of prod(shape) over 73,081 tensors in 65 safetensors shard(s) = 168,821,311,488 stored elements. Not a parameter count: config.json declares a quantization_config; 36096 weight tensor(s) are U8/I8 with a matching *_scale companion (packed storage).

## Sources

- [MiMo-V2.6-Pro-RL config.json](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Pro-RL/resolve/main/config.json)
- [MiMo-V2.6-Flash-RL config.json](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Flash-RL/resolve/main/config.json)
