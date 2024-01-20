# StyleTTS 2 Api

## Run this StyleTTS 2 generator API (LibriTTS only)
1. Start docker image
```
docker run -d -p 5000:5000 --gpus=all nixolas1/styletts2-api:latest
```
2. Connect using HTTP API. Uses COG http interface. Example in python:
```python
def styletts2(text, diffusion_steps=8, voice_url=None, embedding_scale=1.2, alpha=0.25, beta=0.6, seed=69):
    url = "http://localhost:5000/predictions"
    data = {
        "input": {
            "text": text,
            "reference": voice_url, # http reachable url to wav file. eg. http://localhost:5001/voice.wav
            "alpha": alpha,
            "beta": beta,
            "diffusion_steps": diffusion_steps,
            "embedding_scale": embedding_scale,
            "seed": seed
        }
    }

    headers = {'Content-Type': 'application/json'}
    response = requests.post(url, data=json.dumps(data), headers=headers)
    output = response.json().get('output', '') # output = mp3 base64 data uri
    output_data = output.split(',', 1)[-1]
    with open('temp.mp3', 'wb') as file:
    file.write(base64.b64decode(output_data))

```


## Build image yourself
1. Install [Cog](https://github.com/replicate/cog/tree/main?tab=readme-ov-file#cog-containers-for-machine-learning)
2. Download https://huggingface.co/yl4579/StyleTTS2-LibriTTS/tree/main/Models/LibriTTS into ./Models/LibriTTS
3. ???
4. `cog build -t styletts2-api`


## Inference
Please refer to [Inference_LJSpeech.ipynb](https://github.com/yl4579/StyleTTS2/blob/main/Demo/Inference_LJSpeech.ipynb) (single-speaker) and [Inference_LibriTTS.ipynb](https://github.com/yl4579/StyleTTS2/blob/main/Demo/Inference_LibriTTS.ipynb) (multi-speaker) for details. For LibriTTS, you will also need to download [reference_audio.zip](https://huggingface.co/yl4579/StyleTTS2-LibriTTS/resolve/main/reference_audio.zip) and unzip it under the `demo` before running the demo. 

- The pretrained StyleTTS 2 on LJSpeech corpus in 24 kHz can be downloaded at [https://huggingface.co/yl4579/StyleTTS2-LJSpeech/tree/main](https://huggingface.co/yl4579/StyleTTS2-LJSpeech/tree/main).

  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yl4579/StyleTTS2/blob/main/Colab/StyleTTS2_Demo_LJSpeech.ipynb)

- The pretrained StyleTTS 2 model on LibriTTS can be downloaded at [https://huggingface.co/yl4579/StyleTTS2-LibriTTS/tree/main](https://huggingface.co/yl4579/StyleTTS2-LibriTTS/tree/main). 

  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yl4579/StyleTTS2/blob/main/Colab/StyleTTS2_Demo_LibriTTS.ipynb)


You can import StyleTTS 2 and run it in your own code. However, the inference depends on a GPL-licensed package, so it is not included directly in this repository. A [GPL-licensed fork](https://github.com/NeuralVox/StyleTTS2) has an importable script, as well as an experimental streaming API, etc. A [fully MIT-licensed package](https://pypi.org/project/styletts2/) that uses gruut (albeit lower quality due to mismatch between phonemizer and gruut) is also available.  

***Before using these pre-trained models, you agree to inform the listeners that the speech samples are synthesized by the pre-trained models, unless you have the permission to use the voice you synthesize. That is, you agree to only use voices whose speakers grant the permission to have their voice cloned, either directly or by license before making synthesized voices public, or you have to publicly announce that these voices are synthesized if you do not have the permission to use these voices.*** 

### Common Issues
- **High-pitched background noise**: This is caused by numerical float differences in older GPUs. For more details, please refer to issue [#13](https://github.com/yl4579/StyleTTS2/issues/13). Basically, you will need to use more modern GPUs or do inference on CPUs.
- **Pre-trained model license**: You only need to abide by the above rules if you use **the pre-trained models** and the voices are **NOT** in the training set, i.e., your reference speakers are not from any open access dataset. For more details of rules to use the pre-trained models, please see [#37](https://github.com/yl4579/StyleTTS2/issues/37).

## References
- [archinetai/audio-diffusion-pytorch](https://github.com/archinetai/audio-diffusion-pytorch)
- [jik876/hifi-gan](https://github.com/jik876/hifi-gan)
- [rishikksh20/iSTFTNet-pytorch](https://github.com/rishikksh20/iSTFTNet-pytorch)
- [nii-yamagishilab/project-NN-Pytorch-scripts/project/01-nsf](https://github.com/nii-yamagishilab/project-NN-Pytorch-scripts/tree/master/project/01-nsf)

## License

I know, I don't like GPL either. But this package depends on `phonemizer`, which is GPL licensed. [Check out the original repository for a MIT-licensed version!](https://github.com/yl4579/StyleTTS2)

Copyright (C) 2023 Aaron (Yinghao) Li (under the MIT license). 
Copyright (C) 2023 mrfakename.

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

**This software was previously licensed under the MIT license:**

MIT License

Copyright (c) 2023 Aaron (Yinghao) Li

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
