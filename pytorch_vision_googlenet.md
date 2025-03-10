---
layout: hub_detail
background-class: hub-background
body-class: hub
title: GoogLeNet
summary: GoogLeNet was based on a deep convolutional neural network architecture codenamed "Inception" which won ImageNet 2014.
category: researchers
image: googlenet1.png
author: Pytorch Team
tags: [vision]
github-link: https://github.com/pytorch/vision/blob/main/torchvision/models/googlenet.py
github-id: pytorch/vision
featured_image_1: googlenet1.png
featured_image_2: googlenet2.png
accelerator: cuda-optional
demo-model-links: 
    - https://huggingface.co/spaces/pytorch/GoogleNet
    - https://console.tiyaro.ai/explore/googlenet/demo
order: 10
---

```python
import torch
model = torch.hub.load('pytorch/vision:v0.10.0', 'googlenet', pretrained=True)
model.eval()
```

All pre-trained models expect input images normalized in the same way,
i.e. mini-batches of 3-channel RGB images of shape `(3 x H x W)`, where `H` and `W` are expected to be at least `224`.
The images have to be loaded in to a range of `[0, 1]` and then normalized using `mean = [0.485, 0.456, 0.406]`
and `std = [0.229, 0.224, 0.225]`.

Here's a sample execution.

```python
# Download an example image from the pytorch website
import urllib
url, filename = ("https://github.com/pytorch/hub/raw/master/images/dog.jpg", "dog.jpg")
try: urllib.URLopener().retrieve(url, filename)
except: urllib.request.urlretrieve(url, filename)
```

```python
# sample execution (requires torchvision)
from PIL import Image
from torchvision import transforms
input_image = Image.open(filename)
preprocess = transforms.Compose([
    transforms.Resize(256),
    transforms.CenterCrop(224),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
])
input_tensor = preprocess(input_image)
input_batch = input_tensor.unsqueeze(0) # create a mini-batch as expected by the model

# move the input and model to GPU for speed if available
if torch.cuda.is_available():
    input_batch = input_batch.to('cuda')
    model.to('cuda')

with torch.no_grad():
    output = model(input_batch)
# Tensor of shape 1000, with confidence scores over Imagenet's 1000 classes
print(output[0])
# The output has unnormalized scores. To get probabilities, you can run a softmax on it.
probabilities = torch.nn.functional.softmax(output[0], dim=0)
print(probabilities)
```

```
# Download ImageNet labels
!wget https://raw.githubusercontent.com/pytorch/hub/master/imagenet_classes.txt
```

```
# Read the categories
with open("imagenet_classes.txt", "r") as f:
    categories = [s.strip() for s in f.readlines()]
# Show top categories per image
top5_prob, top5_catid = torch.topk(probabilities, 5)
for i in range(top5_prob.size(0)):
    print(categories[top5_catid[i]], top5_prob[i].item())
```


### Model Description

GoogLeNet was based on a deep convolutional neural network architecture codenamed "Inception", which was responsible for setting the new state of the art for classification and detection in the ImageNet Large-Scale Visual Recognition Challenge 2014 (ILSVRC 2014). The 1-crop error rates on the ImageNet dataset with a pretrained model are list below.

| Model structure | Top-1 error | Top-5 error |
| --------------- | ----------- | ----------- |
|  googlenet       | 30.22       | 10.47       |



### References

 - [Going Deeper with Convolutions](https://arxiv.org/abs/1409.4842)
