# Priva

## Installation
### Code
```
git clone https://github.com/FHQiii/Priva.git
```
### Environment
```
pip install numpy torch blobfile tqdm pyYaml pillow    # e.g. torch 1.7.1+cu110.
```
### Pre-Trained Models
To restore human face images, download this [model](https://drive.google.com/file/d/1wSoA5fm_d6JBZk4RZ1SzWLMgev4WqH21/view?usp=share_link)(from [SDEdit](https://github.com/ermongroup/SDEdit)) and put it into `Priva/exp/logs/celeba/`. 
```
https://drive.google.com/file/d/1wSoA5fm_d6JBZk4RZ1SzWLMgev4WqH21/view?usp=share_link
```
To restore general images, download this [model](https://openaipublic.blob.core.windows.net/diffusion/jul-2021/256x256_diffusion_uncond.pt)(from [guided-diffusion](https://github.com/openai/guided-diffusion)) and put it into `Priva/exp/logs/imagenet/`.
```
wget https://openaipublic.blob.core.windows.net/diffusion/jul-2021/256x256_diffusion_uncond.pt
```
### Quick Start
Run below command to get 4x SR results immediately. The results should be in `Priva/exp/image_samples/demo`.
```
python main.py --ni --simplified --config celeba_hq.yml --path_y celeba_hq --eta 0.85 --deg "sr_averagepooling" --deg_scale 4.0 --sigma_y 0 -i demo
```

## Setting
The detailed sampling command is here:
```
python main.py --ni --simplified --config {CONFIG}.yml --path_y {PATH_Y} --eta {ETA} --deg {DEGRADATION} --deg_scale {DEGRADATION_SCALE} --sigma_y {SIGMA_Y} -i {IMAGE_FOLDER}
```
with following options:
- We implement **TWO** versions of Priva in this repository. One is SVD-based version, which is more precise in solving noisy tasks. Another one is the simplified version, which does not involve SVD and is flexible for users to define their own degradations. Use `--simplified` to activate the simplified Priva. Without `--simplified` will turn to the SVD-based Priva.
- `PATH_Y` is the folder name of the test dataset, in `Priva/exp/datasets`.
- `ETA` is the DDIM hyperparameter. (default: `0.85`)
- `DEGREDATION` is the supported tasks including `cs_walshhadamard`, `cs_blockbased`, `inpainting`, `denoising`, `deblur_uni`, `deblur_gauss`, `deblur_aniso`, `sr_averagepooling`,`sr_bicubic`, `colorization`, `mask_color_sr`, and user-defined `diy`.
- `DEGRADATION_SCALE` is the scale of degredation. e.g., `--deg sr_bicubic --deg_scale 4` lead to 4xSR.
- `SIGMA_Y` is the noise observed in y.
- `CONFIG` is the name of the config file (see `configs/` for a list), including hyperparameters such as batch size and sampling step.
- `IMAGE_FOLDER` is the folder name of the results.

For the config files, e.g., celeba_hq.yml, you may change following properties:
```
sampling:
    batch_size: 1
    
time_travel:
    T_sampling: 100     # sampling steps
    travel_length: 1    # time-travel parameters l and s, see section 3.3 of the paper.
    travel_repeat: 1    # time-travel parameter r, see section 3.3 of the paper.
```

# References
This implementation is based on / inspired by:
- https://github.com/wyhuai/RND (null-space learning)
- https://github.com/andreas128/RePaint (time-travel trick)
- https://github.com/bahjat-kawar/ddrm (code structure)
