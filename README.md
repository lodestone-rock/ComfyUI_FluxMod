# DEPRECATED! Chroma Manual [WIP]
Chroma is a 8.9B parameter model based on FLUX.1-schnell.
use comfyui default workflow here instead [https://comfyanonymous.github.io/ComfyUI_examples/chroma/](https://comfyanonymous.github.io/ComfyUI_examples/chroma/)

## Requirements

- ComfyUI installation
- [Chroma checkpoint](https://huggingface.co/lodestones/Chroma)
- [T5 XXL](https://huggingface.co/comfyanonymous/flux_text_encoders/resolve/main/t5xxl_fp16.safetensors) or [T5 XXL fp8](https://huggingface.co/comfyanonymous/flux_text_encoders/resolve/main/t5xxl_fp8_e4m3fn.safetensors) (either of them will work)
- [FLUX VAE](https://huggingface.co/black-forest-labs/FLUX.1-schnell/resolve/main/ae.safetensors)
- [Chroma_Workflow](https://huggingface.co/lodestones/Chroma/resolve/main/simple_workflow.json)


### Manual Installation (Chroma)

1. Navigate to your ComfyUI's `ComfyUI/custom_nodes` folder
2. Clone the repository:

```bash
git clone https://github.com/lodestone-rock/ComfyUI_FluxMod.git
```

3. Restart ComfyUI
4. Refresh your browser if ComfyUI is already running

### How to run the model
1. put `T5_xxl` into `ComfyUI/models/clip` folder
2. put `FLUX VAE` into `ComfyUI/models/vae` folder
3. put `Chroma checkpoint` into `ComfyUI/models/diffusion_models` folder
4. load chroma workflow to your ComfyUI
5. Run the workflow

[WIP]

# Do not follow instruction bellow if you want to use Chroma! 
---

# ComfyUI FluxMod 🚀

A modulation layer addon for Flux that reduces model size to 8.8B parameters without significant quality loss.

[![Hugging Face](https://img.shields.io/badge/🤗%20Hugging%20Face-Modulation%20Layer-yellow)](https://huggingface.co/lodestone-horizon/flux-essence)

## Overview

ComfyUI_FluxMod acts as a plugin for Flux, enabling you to run Flux Dev and Flux Schnell on more consumer-friendly hardware. This is achieved by utilizing a modulation layer that significantly reduces the parameter count while maintaining quality.

> **Note**: You still need a Flux Dev or Flux Schnell model to use this addon.

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Node Information](#node-information)
- [Quantization Guide](#quantization-guide)
- [Examples](#examples)
- [FAQ](#faq)
- [Support](#support)
- [Contributing](#contributing)
- [Issues](#issues)
- [Sample Workflow](#sample-workflow)

## Requirements

- ComfyUI installation
- Flux model (Dev or Schnell). Tested to work with the original models, may not work with third party versions such as fine tunes.
- [universal_modulator.safetensors](https://huggingface.co/lodestone-horizon/flux-essence)
- [ComfyUI-GGUF](https://github.com/city96/ComfyUI-GGUF) installed if you want to use GGUF Flux models.

## Installation

It's recommended to use either ComfyUI Manager or Comfy Registry.

### Via ComfyUI Manager (GUI)

1. Install [ComfyUI Manager](https://github.com/ltdrdata/ComfyUI-Manager) if you haven't already
2. Open ComfyUI and click on the Manager Button (puzzle piece icon)
3. Go to "Custom Nodes Manager" tab
4. Search for "ComfyUI_FluxMod"
5. Click Install
6. Restart ComfyUI

### Via Comfy Registry (CLI)

```bash
comfy node registry-install comfyui_fluxmod
```

### Manual Installation

1. Navigate to your ComfyUI's custom_nodes folder
2. Clone the repository:

```bash
git clone https://github.com/lodestone-rock/ComfyUI_FluxMod.git
```

3. Restart ComfyUI
4. Refresh your browser if ComfyUI is already running

## Node Information

| Node                    | Description                              | Options                                                                                                                             |
| ----------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| FluxModCheckpointLoader | Primary checkpoint loading node          | • **ckpt_name**: Flux model path<br>• **guidance_name**: Modulation addon path<br>• **quant_mode**: Quantization selection          |
| KSamplerMod             | Modified KSampler for 8-bit quantization | • **activation_casting**: Switch between bf16 and fp16                                                                              |
| FluxModSamplerWrapper   | Sampler wrapper for 8-bit quantization   | • **activation_casting**: Switch between bf16 and fp16                                                                              |
| SkipLayerForward        | Skip specific Flux layers                | • **skip_mmdit_layers**: Which MMDiT layer to skip<br>• **skip_dit_layers**: Which DIT layers to skip (-1 to disable)               |

## Usage

> ⚠️ **Important**: When using `float8_e4m3fn` or `float8_e5m2` quantization modes, you must use either the `KSamplerMod` node instead of the regular KSampler or `FluxModSamplerWrapper` for `SamplerCustom` or other sampler nodes that take a `SAMPLER` input. This requirement does not apply when using `bf16` mode in `FluxModCheckpointLoader` or when starting ComfyUI with `--fast`.

1. Double click workspace → search "FluxModCheckpointLoader"
2. Select your Flux model in `ckpt_name`
3. Select modulation addon in `guidance_name`
4. Choose quantization mode
5. Configure remaining nodes as per standard Flux workflow

> 💡 **Tip**: Check the `examples` folder for sample workflows. Drag and drop the workflow image into ComfyUI to get started quickly.

## Quantization Guide

| Mode          | Recommended GPU | VRAM Usage | Recommended |
| ------------- | --------------- | ---------- | ----------- |
| bf16          | 24GB+           | ~19GB      | ✅          |
| float8_e4m3fn | 12-16GB         | ~10GB      | ✅          |
| float8_e5m2   | 12-16GB         | ~10GB      | ❌          |

## Examples

Here are some comparison examples showing the output quality between the original Flux model and FluxMod:

<details>
<summary><b>Example 1: Art Studio Scene</b></summary>

![Comparison 1](https://github.com/lodestone-rock/flux-mod/blob/main/examples/comparison_1.png)
**Prompt:** A photo of an art studio with a cabin design, there are paint splatters over much of the wooden furnishing, there are old style windows overlooking a lake outside with a slanted ceiling with large skylights letting in natural light. There is an easel with a half-finished painting. There is a paint palette that is placed on a wooden table. There are dust speckles floating in the air illuminated by the golden hour light. There is a woman standing in a colourful dress and ruby red shoes who is painting in front of the easel.

</details>

<details>
<summary><b>Example 2: Glass Rainbow Room</b></summary>

![Comparison 2](https://github.com/lodestone-rock/flux-mod/blob/main/examples/comparison_2.png)
**Prompt:** A photo of a room with the walls made of millions of pieces of shattered glass in rainbow colours, there is light shining through the glass causing a huge dispersion of colours across the scene. The light is refracting off all the glass in the room illuminating the room with bright hues from the colored glass.

</details>

<details>
<summary><b>Example 3: Balloon Text</b></summary>

![Comparison 3](https://github.com/lodestone-rock/flux-mod/blob/main/examples/comparison_3.png)
**Prompt:** Text made out of foil balloons saying "Hi there fellow traveller, make sure to star this GitHub! Thank you!"

</details>

<details>
<summary><b>Example 4: Space Pirate Ship</b></summary>

![Comparison 4](https://github.com/lodestone-rock/flux-mod/blob/main/examples/comparison_4.png)
**Prompt:** Concept art of a ornately decorated pirate ship in outer space that is floating through a nebula with spectacular blue and purple hues. There is dust that is around the ship as it sails through the cosmos, dispersing at the bow of the ship.

</details>

<details>
<summary><b>Example 5: Detailed Room Scene</b></summary>

![Comparison 5](https://github.com/lodestone-rock/flux-mod/blob/main/examples/comparison_5.png)
**Prompt:** A scene with a blue block and a red ball that is placed on top of the blue block. In the background there is a green door with pink walls. To the left of the door, there is a painting on the wall which is showing a scene of an ocean wave that is orange. To the right of the door, there is a portrait of a cat that has purple eyes. There is a window to the right side of the scene that is letting in light that has a shade of blue, illuminating the carpet which has a cyan hue. There is a bed on the left side of the room that has a white pillow with orange sheets. There is a bedside table with an orange lamp and a phone that is placed on top of it. To the right side of the beside table, there is a green bin with a red recycling logo on it.

</details>

## FAQ

<details>
<summary><b>Will my outputs be different?</b></summary>
Yes, outputs will likely differ as we're reducing parameters. However, the difference is often minimal for most use cases.
</details>

<details>
<summary><b>How much does this degrade image quality?</b></summary>
Testing shows minimal quality degradation in most cases. The most notable exception is long text generation, which shows a moderate degradation.
</details>

<details>
<summary><b>Why use this over regular quantization?</b></summary>
You don't have to use this over regular quantisation! You can combine them, or if you don't want to use quantisation at all and you have enough VRAM, you can also just stick with bf16. If you combine quantisation, you can make the model even smaller and allow it to run on consumer hardware.
</details>

<details>
<summary><b>I tried exporting/saving this model and I got an error?</b></summary>
This model has a completely different architecture compared to the original Flux and none of the current methods for exporting/saving models would support it. This is why we needed to have this custom node created in the first place, since otherwise it wouldn't load properly.
</details>

## Support

Need help? Join our [Discord community](https://discord.gg/UxBAMcpqDU) for support and discussions.

## Contributing

Pull requests are welcome! Feel free to contribute to the project by:

- Fixing bugs
- Adding new features
- Improving documentation
- Suggesting enhancements

## Issues

Found a bug? Have a suggestion? Please create a [GitHub issue](https://github.com/lodestone-rock/ComfyUI_FluxMod/issues) with:

- A clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- Your environment details (OS, GPU, etc.)

## Sample Workflow

![Workflow Example](https://github.com/lodestone-rock/flux-mod/blob/main/examples/workflow.png)
