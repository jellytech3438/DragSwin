# DragSwin

This repository uses [StyleSwin](https://github.com/microsoft/StyleSwin) to implement [DragGAN](https://github.com/XingangPan/DragGAN).

## Abstract

DragGAN used StyleGAN2 as its backbone to modify and generate images. Our work depends on it heavily. Users can operate image dragging task from source points to target points. Although there are many variants of this paper appeared, but none of them achieved the task using Vision Transformers. Therefore, I aim to accomplish this task by implementing Transformer-based operations. Furthermore, I use mask as a significant part of manipulate with images, and modifying the model to generate better images.

## Modification

### Layer of Split
When dragging images, DragGAN divides the features into fixed and trainable parts. We change the based model from CNN to Transformer. Therefore, we experimented with using different block indices in StyleSwin as the division point, and used LPIPS and MDS as evaluation metrics (lower is better).

We observed that in StyleSwin, using later layers as the division block results in better scores.

| Block No.      | 2 | 3 | 4 | 5 | 6 |
| -------------- | ----- | ----- | ----- | ----- | ----- |
| LPIPS ↓        | 0.403 | 0.411 | 0.420 | 0.359 | 0.354 |
| MDS ↓          | 3.605 | 2.828 | 2.236 | 2.236 | 2.236 |

(When the i-th layer is selected, the (i+1)-th layer (the first fixed layer) is used as the feature for loss computation.)

### Mask Usage and Changes
In the original DragGAN, users can restrict the region that is allowed to move during the dragging process by drawing a mask. However, in the paper, this restriction is implemented only through the loss function, which still results in unwanted changes within the masked region.
To address this, we leverage the transformer architecture, which allows for more precise control through its attention mechanism.

We also replace the Position Embedding with Rotary Position Embedding (RoPE) to improve patch relationships after movement.

## Run the gui
It is recommand to run the program with gui code.
```bash
$ python gui.py
```

## Responsible AI Considerations

Our work does not directly modify the exiting images which may alter the identity or expression of the people. We discourage the use of our work in such applications as it is not designed to do so. We have quantitatively verified that the proposed method does not show evident disparity, on gender and ages as the model mostly follows the dataset distribution; however, we encourage additional care if you intend to use the system on certain demographic groups. We also encourage use of fair and representative data when training on customized data. We caution that the high-resolution images produced by our model may potentially be misused for impersonating humans and viable solutions so avoid this include adding tags or watermarks when distributing the generated photos.

## Acknowledgements

This code borrows heavily from [StyleSwin](https://github.com/rosinality/stylegan2-pytorch) and [DragGAN](https://github.com/XingangPan/DragGAN). We also adapt the codes from [Rotary Position Embedding for Vision Transformer](https://github.com/naver-ai/rope-vit).

## License
The codes are under the MIT license follow with [StyleSwin's Liecnes](https://github.com/microsoft/StyleSwin?tab=MIT-1-ov-file).
