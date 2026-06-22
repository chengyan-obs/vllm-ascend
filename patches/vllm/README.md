# VL LoRA profile_run multimodal metadata fix

This patch targets the upstream vLLM repository.

Root cause:
During EngineCore initialization, profile_run calls model.embed_multimodal(...)
for multimodal encoder profiling. This path executes visual encoder and
connector LoRA, but the original code does not set TOWER / CONNECTOR LoRA
metadata for the dummy multimodal input. As a result, token_lora_indices may
use the full preallocated buffer instead of the actual multimodal token count.

Fix:
Set dummy LoRA mappings for TOWER and CONNECTOR during multimodal encoder
profiling before calling model.embed_multimodal(...).

Validation:
Qwen VL LoRA regression set passed:
6 passed, 9 warnings in 874.09s.
