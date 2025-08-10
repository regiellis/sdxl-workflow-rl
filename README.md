# ComfyUI SDXL ControlNet Workflow

This workflow uses SDXL with multiple ControlNet inputs (pose, depth, canny) and advanced prompt / LoRA management to produce highly controlled renders.
Core ComfyUI nodes and Reallusion-specific nodes have been excluded from the model list but installation guidance is provided.

---

![alt text](Result0.png)

## Required Models

### 1. Base Model

* Checkpoint
  * SDXL 1.0 compatible checkpoint (.safetensors or .ckpt)
  * Example: sdxl_base_1.0.safetensors

---

### 2. LoRA Models

* Any SDXL-compatible LoRAs you plan to use (loaded via Power Lora Loader (rgthree))

---

### 3. ControlNet Models

The workflow requires three ControlNet model files (with union support):

| Control Type                  | Example Model Path                            | Purpose                               |
| ----------------------------- | --------------------------------------------- | ------------------------------------- |
| Union ControlNet (Multi)      | [controlnet-union-sdxl-1.0](https://huggingface.co/xinsir/controlnet-union-sdxl-1.0) (save as `controlnet-union-sdxl.safetensors`) | Merges multiple control types         |
| OpenPose                      | SDXL/OpenPoseXL2.safetensors (download via ComfyUI Manager) | Captures and applies human pose data  |
| Depth                         | (Packed in Union model)                       | Provides scene depth guidance         |
| Canny / Edge                  | (Packed in Union model)                       | Guides shape and edge structure       |

---

## Required Custom Nodes

Installation Tip:
All the nodes and models except the Reallusion (RL) nodes can be installed via the ComfyUI Manager using the Install Missing Nodes function (recommended).
The RL nodes are available here: [ComfyUI-Reallusion](https://github.com/reallusion/ComfyUI-Reallusion).

Official RL Resources:

* [Consistent Characters & Precise Control for AI Films and Commercial Production](https://discussions.reallusion.com/t/official-consistent-characters-and-precise-control-for-ai-films-and-commercial-production/14440)
* [Maximize AI Render Performance — Custom Workflow Setup Tips](https://discussions.reallusion.com/t/official-maximize-ai-render-performance-custom-workflow-setup-tips/14691/2)

| Node                       | Repository                                                                          | Purpose                                           |
| -------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------- |
| rgthree-comfy              | [rgthree-comfy](https://github.com/rgthree/rgthree-comfy)                          | Power Lora Loader & Power Prompt nodes            |
| SDXL Prompt Styler         | [ComfyUI Instant Styler](https://github.com/cubiq/ComfyUI_InstantStyler) (or maintained variant) | Applies consistent styling presets to prompts     |
| ControlNetApplySD3         | Included with ControlNet extension                                                  | Applies VAE and prompt-controlled conditioning    |
| SetUnionControlNetType     | Part of ControlNet Union support                                                    | Assigns control types for union model processing  |

---

## Recommended Image Preprocessors

You will need external tools or ComfyUI preprocessors to generate these images before feeding them into the workflow:

* Pose Image -> OpenPose output (RenderImageOpenPose.png)
* Depth Map -> Depth estimation output (RenderImageDepth.png)
* Canny Image -> Edge detection output (RenderImageCanny.png)

---

## Installation Steps

1. Install ComfyUI
  Follow the official installation instructions: <https://github.com/comfyanonymous/ComfyUI>

1. Install Missing Nodes via Manager

* Open ComfyUI Manager
* Select Install Missing Nodes
* This will automatically install all required custom nodes (except RL nodes)

1. Install RL Nodes Manually
  Clone the RL nodes repository (PowerShell or Bash):

  ```bash
  git clone https://github.com/reallusion/ComfyUI-Reallusion.git custom_nodes/ComfyUI-Reallusion
  ```

1. Download Required Models

* Place base SDXL checkpoint in:
  `ComfyUI/models/checkpoints/`
* Place ControlNet models in:
  `ComfyUI/models/controlnet/`
* Place LoRA models in:
  `ComfyUI/models/loras/`
  * (If using Union ControlNet) Download from: [xinsir/controlnet-union-sdxl-1.0](https://huggingface.co/xinsir/controlnet-union-sdxl-1.0) and place the .safetensors file in `ComfyUI/models/controlnet/`
* OpenPoseXL2 is typically installed automatically when using the ComfyUI Manager (Install Missing Nodes); if present, no manual download is required.

1. Load Workflow
  Open the JSON workflow file in ComfyUI.

---


## Usage

1. Provide input image(s) for pose / depth / canny preprocessing.
2. Select LoRAs and prompt styling as needed.
3. Adjust ControlNet strengths and ranges for each control type.
4. Render and save outputs.

---

## Suggested Folder Structure (Example)

ComfyUI/
  models/
    checkpoints/
      sdxl_base_1.0.safetensors
    controlnet/
      controlnet-union-sdxl.safetensors
      OpenPoseXL2.safetensors
    loras/
      your_lora.safetensors
  custom_nodes/
    rgthree-comfy/
    ComfyUI-Reallusion/

---

## Troubleshooting

* Missing node errors: Run Install Missing Nodes again or verify the custom_nodes folder names.
* Model not found: Confirm the filename and that it resides in the correct models subfolder.
* Control not applying: Lower or raise the strength parameter; verify the correct control type mapping for union.
* Performance issues: See Reallusion performance guidance (link above) and ensure GPU VRAM isn’t saturated by excessive LoRAs.

---

## License

This repository includes an MIT License (see LICENSE file). Models and external node repos may have their own licenses; review them before commercial use.

---

If you need this README adjusted (e.g., add Windows-specific batch scripts, pin exact repo commit hashes, or include a generated diagram), let me know.
