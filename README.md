# ai_utilities

A set of scripts useful with `fast.ai` lectures and libraries.
The most common use case is downloading images for training vision models.

`image_download` is the primary function and is intended to be imported into a python script. (This differs from a previous version intended for use as a CLI script.)

## Installation
- This version no longer requires selenium. It does depend upon `hellock`, `icrawler` and `python-magic`
- `Anaconda`
- `conda install selenium`
- `https://github.com/mozilla/geckodriver/releases/` for current Geckodriver (Firefox) according to OS.
- `wget https://github.com/mozilla/geckodriver/releases/geckodriver-v0.24.0-linux64.tar.gz`
- `tar xfvz geckodriver-v0.24.0-linux64.tar.gz`
- `mv geckodriver ~/bin/`, where `~/bin` is in `PATH`
- `git clone https://github.com/prairie-guy/ai_utilities.git `


## Example Usage
Download 500 images of each `class`, check each image is a valid `jpeg`, save to directory `dataset`, create imagenet-type directory structure and create `data = ImageDataBunch.from_folder(...)`
```
sys.path.append(your-parent-directory-of-ai_utilities)
from ai_utilities import *
path = Path.cwd()/'dataset'
pets = ['dog', 'cat', 'gold fish', 'tortise', 'snake' ]
for p in pets:
    image_download(p, 500, timeout=.1)
make_train_valid('dataset')
data = ImageDataBunch.from_folder(path,ds_tfms=get_transforms(), size=224, bs=64).normalize(imagenet_stats)
```    

## Functions
### image_download.py
Downloads a specified number of images (typically limited to 1000) from a specified search engine. By default, images are saved to the directory `dataset`

```
image_download(searchtext:str, num_images:int, engine:str='google', gui:bool=False, timeout:float=0.3)
Select, search, download and save a specified number images using a choice of search engines, 
currently `google` or `bing`. (Downloaded images are checked to be valid `jpeg` files.)

positional arguments:
  searchtext            Search Image
  num_images            Number of Images

optional arguments:
  gui=False             Use Browser in the GUI
  engine='google'       Search engine {google|bing}
  timeout=0.3           Timeout for requests (May require optimization based upon connection)
```



### make_train_valid.py
From a directory containing sub-directories, each with a different class of images, make an imagenet-type directory structure.
It randomly copies files from `labels_dir` to sub-directories: `train`, `valid`, `test`. Creates an imagmenet-type directory usable by `ImageDataBunch.from_folder(dir,...)`

```
make_train_valid(labels_dir:Path, train:float=.8, valid:float=.2, test:float=0)
                           
positional arguments:
  labels_dir     Contains at least two directories of labels, each containing
                 files of that label

optional arguments:
  train=.8  files for training, default=.8
  valid=.2  files for validation, default=.2
  test=  0  files for training, default=.0
```

For example, given a directory:
```
catsdogs/
         ..cat/[*.jpg]
         ..dog/[*.jpg]
```         

Creates the following directory structure:
```
catsdogs/
         ..cat/[*.jpg]
         ..dog/[*.jpg]
         ..train/
                 ..cat/[*.jpg]
                 ..dog/[*.jpg]
         ..valid/
                 ..cat/[*.jpg]
                 ..dog/[*.jpg]
``` 
