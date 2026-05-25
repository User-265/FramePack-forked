<p align="center">
    <img src="https://github.com/user-attachments/assets/2cc030b4-87e1-40a0-b5bf-1b7d6b62820b" width="300">
</p>

# FramePack

["下一帧预测视频扩散模型中的帧上下文打包与漂移防止"](https://lllyasviel.github.io/frame_pack_gitpage/) 的官方实现及桌面软件。

链接: [**论文**](https://arxiv.org/abs/2504.12626), [**项目页面**](https://lllyasviel.github.io/frame_pack_gitpage/)

FramePack 是一种下一帧（下一帧段）预测的神经网络结构，能够渐进式地生成视频。

FramePack 将输入上下文压缩到固定长度，因此生成的计算负载不会随视频长度增加而增加。

使用 13B 模型时，即使是在笔记本 GPU 上，FramePack 也能处理极其大量的帧。

FramePack 可以以极大批量进行训练，其批量规模可媲美图像扩散训练。

**视频扩散，却有着图像扩散般的轻量体验。**

# 新闻

**2025年7月14日:** FramePack-P1 的部分纯文本转视频防漂移压力测试结果已上传至[此链接，](https://lllyasviel.github.io/frame_pack_gitpage/p1/#text-to-video-stress-tests) 使用了常见提示词且无任何参考图像。

**2025年6月26日:** FramePack-P1 的部分结果已上传至[此链接。](https://lllyasviel.github.io/frame_pack_gitpage/p1) FramePack-P1 将是 FramePack 的下一个版本，包含两项设计：计划性防漂移（Planned Anti-Drifting）与历史离散化（History Discretization）。

**2025年5月3日:** FramePack-F1 已发布。[在此尝试。](https://github.com/lllyasviel/FramePack/discussions/459)

请注意，此 GitHub 仓库是 FramePack 的唯一官方网站。我们不提供任何网络服务。所有其他网站均为垃圾信息与仿冒网站，包括但不限于 `framepack.co`、`frame_pack.co`、`framepack.net`、`frame_pack.net`、`framepack.ai`、`frame_pack.ai`、`framepack.pro`、`frame_pack.pro`、`framepack.cc`、`frame_pack.cc`、`framepackai.co`、`frame_pack_ai.co`、`framepackai.net`、`frame_pack_ai.net`、`framepackai.pro`、`frame_pack_ai.pro`、`framepackai.cc`、`frame_pack_ai.cc` 等等。再次强调，它们全都是垃圾与仿冒网站。**请勿向任何此类网站支付费用或下载文件。**

# 环境需求

请注意，本仓库是一个功能完整的桌面软件，包含极简的独立高质量采样系统和内存管理功能。

**在尝试其他任何东西之前，请先从此仓库开始！**

需求：

* 支持 fp16 和 bf16 的英伟达 GPU，RTX 30XX、40XX、50XX 系列。GTX 10XX/20XX 未经测试。
* Linux 或 Windows 操作系统。
* 至少 6GB 显存。

使用 13B 模型生成 1 分钟（60 秒）30fps 的视频（共 1800 帧）所需的最低显存为 6GB。（是的，6GB，没有写错。笔记本 GPU 也可以。）

关于速度，在我的 RTX 4090 台式机上，生成速度约为 2.5 秒/帧（未优化）或 1.5 秒/帧（使用 teacache）。在我的笔记本（如 3070ti 笔记本或 3060 笔记本）上，速度大约慢 4 到 8 倍。[如果你的速度比这慢得多，请参照此链接进行排查。](https://github.com/lllyasviel/FramePack/issues/151#issuecomment-2817054649)

无论如何，由于它是下一帧（帧段）预测，你将能直接看到生成过程中的帧。因此，在整个视频生成完毕之前，你就会获得大量的视觉反馈。

# 安装方式

**Windows**:

[>>> 点击此处下载一键包 (CUDA 12.6 + Pytorch 2.6) <<<](https://github.com/lllyasviel/FramePack/releases/download/windows/framepack_cu126_torch26.7z)

下载后解压，运行 `update.bat` 进行更新，然后运行 `run.bat` 启动。

请注意，运行 `update.bat` 极为重要，否则你可能正在使用存在未修复 bug 的旧版本。

![image](https://github.com/lllyasviel/stable-diffusion-webui-forge/assets/19834515/c49bd60d-82bd-4086-9859-88d472582b94)

请注意，模型将自动下载。你会从 HuggingFace 下载超过 30GB 的文件。

**Linux**:

建议使用独立的 Python 3.10 环境。

    pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126
    pip install -r requirements.txt

启动 GUI，运行：

    python demo_gradio.py

请注意，该命令支持 `--share`、`--port`、`--server` 等参数。

本软件支持 PyTorch attention、xformers、flash-attn、sage-attention。默认仅使用 PyTorch attention。如果你知道如何安装，可以安装这些 attention 内核。

例如，安装 sage-attention（Linux）:

    pip install sageattention==1.0.6

不过，强烈建议你先在未安装 sage-attention 的环境下尝试，因为它会影响结果，尽管影响极其微小。

# GUI

![ui](https://github.com/user-attachments/assets/8c5cdbb1-b80c-4b7e-ac27-83834ac24cc4)

在左侧上传图像并编写提示词。

右侧是生成的视频和潜在空间预览。

因为这是一个下一帧段预测模型，生成的视频会越来越长。

你将看到每个片段的进度条，以及下一片段的潜在空间预览。

请注意，初始进度可能会比后续的扩散过程慢，因为设备可能需要一些预热时间。

# 健全性检查

在尝试自己的输入之前，强烈建议你先完成这里的健全性检查，以确定是否存在任何硬件或软件问题。

下一帧段预测模型对噪声和硬件上的细微差异非常敏感。通常，不同设备上的结果会略有不同，但整体效果看起来应该相似。在某些情况下，如果条件允许，你甚至可以得到完全一致的结果。

## 图片转5秒视频

下载此图像：

<img src="https://github.com/user-attachments/assets/f3bc35cf-656a-4c9c-a83a-bbab24858b09" width="150">

复制这段提示词：

`The man dances energetically, leaping mid-air with fluid arm swings and quick footwork.`

如下设置：

（全部为默认参数，关闭 teacache）
![image](https://github.com/user-attachments/assets/0071fbb6-600c-4e0f-adc9-31980d540e9d)

结果将是：

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/bc74f039-2b14-4260-a30b-ceacf611a185" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

**重要提示：**

再次强调，这是一个下一帧段预测模型。这意味着你会逐帧或逐片段地生成视频。

**如果你在 UI 中看到一个短得多的视频（比如只有 1 秒），这是完全正常的。** 你只需要等待。更多片段会被生成出来以补全整个视频。

## 了解 TeaCache 和量化的影响

下载此图像：

<img src="https://github.com/user-attachments/assets/42293e30-bdd4-456d-895c-8fedff71be04" width="150">

复制这段提示词：

`The girl dances gracefully, with clear movements, full of charm.`

如下设置：

![image](https://github.com/user-attachments/assets/4274207d-5180-4824-a552-d0d801933435)

关闭 teacache：

![image](https://github.com/user-attachments/assets/53b309fb-667b-4aa8-96a1-f129c7a09ca6)

你将得到：

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/04ab527b-6da1-4726-9210-a8853dda5577" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

现在打开 teacache：

![image](https://github.com/user-attachments/assets/16ad047b-fbcc-4091-83dc-d46bea40708c)

大约 30% 的用户会得到这个结果（其他 70% 的用户会根据硬件状况得到其他随机结果）：

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/149fb486-9ccc-4a48-b1f0-326253051e9b" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>一个典型的较差结果。</em>
    </td>
  </tr>
</table>

可见 teacache 并非真正无损，有时可能会对结果产生较大影响。

我们建议使用 teacache 来快速尝试想法，然后再使用完整的扩散过程以获得高质量的结果。

此建议同样适用于 sage-attention、bnb 量化、gguf 等技术。

## 图片转1分钟视频

<img src="https://github.com/user-attachments/assets/820af6ca-3c2e-4bbc-afe8-9a9be1994ff5" width="150">

`The girl dances gracefully, with clear movements, full of charm.`

![image](https://github.com/user-attachments/assets/8c34fcb2-288a-44b3-a33d-9d2324e30cbd)

将视频长度设置为 60 秒：

![image](https://github.com/user-attachments/assets/5595a7ea-f74e-445e-ad5f-3fb5b4b21bee)

如果一切正常，你最终会得到类似如下的结果。

60秒版：

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/c3be4bde-2e33-4fd4-b76d-289a036d3a47" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

6秒版：

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/37fe2c33-cb03-41e8-acca-920ab3e34861" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

# 更多示例

**项目页面** 中包含了更多示例：[**项目页面**](https://lllyasviel.github.io/frame_pack_gitpage/).

以下是一些你可能感兴趣并想尝试复现的更多示例。

---

<img src="https://github.com/user-attachments/assets/99f4d281-28ad-44f5-8700-aa7a4e5638fa" width="150">

`The girl dances gracefully, with clear movements, full of charm.`

![image](https://github.com/user-attachments/assets/0e98bfca-1d91-4b1d-b30f-4236b517c35e)

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/cebe178a-09ce-4b7a-8f3c-060332f4dab1" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

<img src="https://github.com/user-attachments/assets/853f4f40-2956-472f-aa7a-fa50da03ed92" width="150">

`The girl suddenly took out a sign that said “cute” using right hand`

![image](https://github.com/user-attachments/assets/d51180e4-5537-4e25-a6c6-faecae28648a)

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/116069d2-7499-4f38-ada7-8f85517d1fbb" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

<img src="https://github.com/user-attachments/assets/6d87c53f-81b2-4108-a704-697164ae2e81" width="150">

`The girl skateboarding, repeating the endless spinning and dancing and jumping on a skateboard, with clear movements, full of charm.`

![image](https://github.com/user-attachments/assets/c2cfa835-b8e6-4c28-97f8-88f42da1ffdf)

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/d9e3534a-eb17-4af2-a8ed-8e692e9993d2" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

<img src="https://github.com/user-attachments/assets/6e95d1a5-9674-4c9a-97a9-ddf704159b79" width="150">

`The girl dances gracefully, with clear movements, full of charm.`

![image](https://github.com/user-attachments/assets/7412802a-ce44-4188-b1a4-cfe19f9c9118)

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/e1b3279e-e30d-4d32-b55f-2fb1d37c81d2" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

<img src="https://github.com/user-attachments/assets/90fc6d7e-8f6b-4f8c-a5df-ee5b1c8b63c9" width="150">

`The man dances flamboyantly, swinging his hips and striking bold poses with dramatic flair.`

![image](https://github.com/user-attachments/assets/1dcf10a3-9747-4e77-a269-03a9379dd9af)

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/aaa4481b-7bf8-4c64-bc32-909659767115" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

<img src="https://github.com/user-attachments/assets/62ecf987-ec0c-401d-b3c9-be9ffe84ee5b" width="150">

`The woman dances elegantly among the blossoms, spinning slowly with flowing sleeves and graceful hand movements.`

![image](https://github.com/user-attachments/assets/396f06bc-e399-4ac3-9766-8a42d4f8d383)


<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/f23f2f37-c9b8-45d5-a1be-7c87bd4b41cf" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

<img src="https://github.com/user-attachments/assets/4f740c1a-2d2f-40a6-9613-d6fe64c428aa" width="150">

`The young man writes intensely, flipping papers and adjusting his glasses with swift, focused movements.`

![image](https://github.com/user-attachments/assets/c4513c4b-997a-429b-b092-bb275a37b719)

<table>
  <tr>
    <td align="center" width="300">
      <video 
        src="https://github.com/user-attachments/assets/62e9910e-aea6-4b2b-9333-2e727bccfc64" 
        controls 
        style="max-width:100%;">
      </video>
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>视频可能被 GitHub 压缩</em>
    </td>
  </tr>
</table>

---

# 提示词编写指南

很多人会问如何写出更好的提示词。

以下是我个人经常使用的 ChatGPT 模板，以获取提示词：

    You are an assistant that writes short, motion-focused prompts for animating images.

    When the user sends an image, respond with a single, concise prompt describing visual motion (such as human activity, moving objects, or camera movements). Focus only on how the scene could come alive and become dynamic using brief phrases.

    Larger and more dynamic motions (like dancing, jumping, running, etc.) are preferred over smaller or more subtle ones (like standing still, sitting, etc.).

    Describe subject, then motion, then other things. For example: "The girl dances gracefully, with clear movements, full of charm."

    If there is something that can dance (like a man, girl, robot, etc.), then prefer to describe it as dancing.

    Stay in a loop: one image in, one motion prompt out. Do not explain, ask questions, or generate multiple options.

你将此指令粘贴给 ChatGPT，然后喂给它一张图像，即可获得如下提示词：

![image](https://github.com/user-attachments/assets/586c53b9-0b8c-4c94-b1d3-d7e7c1a705c3)

*The man dances powerfully, striking sharp poses and gliding smoothly across the reflective floor.*

通常这会给你一个效果很好的提示词。

你也可以自己编写提示词。简洁的提示词通常更受欢迎，例如：

*The girl dances gracefully, with clear movements, full of charm.*

*The man dances powerfully, with clear movements, full of energy.*

等等。

# 引用

    @inproceedings{zhang2025framepack,
        title={Frame Context Packing and Drift Prevention in Next-Frame-Prediction Video Diffusion Models},
        author={Lvmin Zhang and Shengqu Cai and Muyang Li and Gordon Wetzstein and Maneesh Agrawala},
        booktitle={The Thirty-ninth Annual Conference on Neural Information Processing Systems},
        year={2025},
    }

    @article{zhang2025framepackv1,
        title={Packing Input Frame Contexts in Next-Frame Prediction Models for Video Generation},
        author={Lvmin Zhang and Maneesh Agrawala},
        journal={Arxiv},
        year={2025}
    }