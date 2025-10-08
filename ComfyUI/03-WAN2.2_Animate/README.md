# WAN 2,2 Animate

We will use [WAN 2.2 Animate](https://huggingface.co/Wan-AI/Wan2.2-Animate-14B), a custom node (Kijai's [`ComfyUI-WanAnimatePreprocess`](https://github.com/kijai/ComfyUI-WanAnimatePreprocess)), and its embedded workflow.

Its "Preprocessing" group detects people in frames, estimates body/hand/face keypoints, extracts aligned face crops, and formats these results into the inputs WanAnimate expects. Wan 2.2 Animate will then turn a single character image plus a reference video into a high‑fidelity animated or replacement video.

This worklow will require a GPU with at least 32GB of VRAM (even when using `fp8` weights).

Because this requires a few ComfyUI "custom nodes" to be installed. 
Kijai's custom node (at the time of this writeup) was not in the auto-validated list.
Because of this, we need to change the `SECURITY_LEVEL` to `weak` in the `compose.yaml` file. This will allow the custom node to be installed.

From the "Manager", use "Custom Node Manager" and search "wananimate" then "Install" the `ComfyUI-WanAnimatePreprocess` node (by Kijai). We need to `Restart` ComfyUI to enable the node, and this will add a new workflow to the WebUI.

Our slightly modified version of the new workflow is `Wan2.2Animate.json`; we have bypassed a node and changed the models path for convenience.
To use it, once dropped into our ComfyUI, use the "Manager" to download the missing nodes before a `Restart`.

Needed weights (create folder if needed):

- For the "Preprocessing" group:
  - [yolov10m.onnx](https://huggingface.co/Wan-AI/Wan2.2-Animate-14B/resolve/main/process_checkpoint/det/yolov10m.onnx) to be placed in `basedir/models/detection` (59MB)
  - [vitpose-l-wholebody.onnx](https://huggingface.co/JunkyByte/easy_ViTPose/resolve/main/onnx/wholebody/vitpose-l-wholebody.onnx) to be placed in `basedir/models/detection` (1.2GB)
  - [vitpose_h_wholebody_model.onnx](https://huggingface.co/Kijai/vitpose_comfy/resolve/main/onnx/vitpose_h_wholebody_model.onnx) to be placed in `basedir/models/detection` (0.4MB)
  - [vitpose_h_wholebody_data.bin](https://huggingface.co/Kijai/vitpose_comfy/resolve/main/onnx/vitpose_h_wholebody_data.bin) to be placed in `basedir/models/detection` (2.5GB)
- For the "Models" group:
  - [Wan2_2-Animate-14B_fp8_scaled_e4m3fn_KJ_v2.safetensors](https://huggingface.co/Kijai/WanVideo_comfy_fp8_scaled/resolve/main/Wan22Animate/Wan2_2-Animate-14B_fp8_scaled_e4m3fn_KJ_v2.safetensors) to be placed in `basedir/models/diffusion_models` (16GB)
  - [lightx2v_I2V_14B_480p_cfg_step_distill_rank64_bf16.safetensors](https://huggingface.co/Kijai/WanVideo_comfy/resolve/main/Lightx2v/lightx2v_I2V_14B_480p_cfg_step_distill_rank64_bf16.safetensors) to be placed in `basedir/models/loras` (0.7GB)
  - [wan2.2_animate_14B_relight_lora_bf16.safetensors](https://huggingface.co/Comfy-Org/Wan_2.2_ComfyUI_Repackaged/resolve/main/split_files/loras/wan2.2_animate_14B_relight_lora_bf16.safetensors) to be placed in `basedir/models/loras` (1.3GB)
  - [umt5_xxl_fp8_e4m3fn_scaled.safetensors](https://huggingface.co/Comfy-Org/Wan_2.2_ComfyUI_Repackaged/resolve/main/split_files/text_encoders/umt5_xxl_fp8_e4m3fn_scaled.safetensors) to be placed in `basedir/models/text_encoders` (6.3GB)
  - [wan_2.1_vae.safetensors](https://huggingface.co/Comfy-Org/Wan_2.2_ComfyUI_Repackaged/resolve/main/split_files/vae/wan_2.1_vae.safetensors) to be placed in `basedir/models/vae` (0.2GB)

## Usage

- Drop a short landscape video in the "Preprocessing" group's "Load Video (upload)" node.
- Drop a landscape image as the source in the "Reference Image" group's "Load Image" node.
- Adapt the "CLIP Text Encode (Prompt)" in the node to the right of the "Models" group.
