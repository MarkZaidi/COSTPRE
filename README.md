# Introduction
## Background

[![Click to redirect to Youtube video](https://user-images.githubusercontent.com/52012166/127568618-0e63f455-3909-4d9f-9b48-f41b6cc5b9af.png)](https://youtu.be/-qQ6RaGl01Q "Computational Staining of Tumor Hypoxia from H&E Images using Convolutional Neural Networks")
COSTPRE (short for COmputational STain Prediction) is a modified U-Net style convolutional neural network, used to predict histological stain distributions in H&E stained images. This was originally developed to predict hypoxia regional stains (i.e. EF5, Pimonidazole, Ca9), but can ideally be trained to learn associations between any two spatially aligned images.

The rationale for constructing COSTPRE lies in the results of our study detailing the [Quantitative visualization of hypoxia and proliferation gradients within histological tissue sections](https://www.frontiersin.org/articles/10.3389/fbioe.2019.00397/full?report=reader). Here, we saw that there are morphological structures associated with hypoxia that are visible on H&E-stained tissue, such as necrosis and vasculature. This led us to conclude that these morphological associations can be learned in deep learning models, and led us to generate this model. Prediction of hypoxia can be used to retroactively investigate whether hypoxia may have played a role in the success or failure of a clinical trial. As H&E-stained tissue sections will exist for nearly all trials, predicting hypoxia distributions can serve as valuable tool in cases where access to the original biological samples may be limited. Furthermore, exogenous hypoxia probes such as EF5 or Pimonidazole must be administered to the patient prior to tissue excision, which further imposes barriers as those probes have not been approved for use in humans by the FDA.
## Training Dataset
The [Wouters-Koritzinsky group](http://www.wklab.org/) at the University of Toronto, Canada has has generated a dataset consisting of over 240 000 images consisting of spatially aligned H&E and immunofluorescent hypoxia histology, as a part of various preclinical experiments. Multiple serial sections were taken from a tumor, and stained for markers of hypoxia, H&E, proliferation, perfusion, vasculature, nuclei, and also contain binary masks of various structures such as necrosis.

![image](https://user-images.githubusercontent.com/52012166/127563294-36d70a02-9c3b-4f63-967b-729988084557.png)

These stained sections were scanned as a whole slide image (WSI) and spatially aligned using tools such as [QuASI](https://github.com/MarkZaidi/QuASI). Access to the training dataset will be reviewed on a case-by-case basis, as much of this data has not been published. COSTPRE was trained using an H&E image, binary mask of necrosis (derived from H&E image), and a serial EF5-stained image of hypoxia. Since the binary mask of necrosis can be generated exclusively from H&E images, we aim to omit this as a requirement for subsequent versions of this model. Until then, you can create a mask of necrosis using open source tools such as [QuPath](https://github.com/qupath/qupath).
## Model Structure
![image](https://user-images.githubusercontent.com/52012166/127566189-c01e39ed-e474-4f41-8cf3-7827e4f3454d.png)

COSTPRE follows a U-Net architecture, originally developed by [Ronnenberger in 2015](https://link.springer.com/chapter/10.1007/978-3-319-24574-4_28). Advantages of this CNN variant includes the ability to work with fewer training images, more precise inferences, and image generation of just a few seconds per WSI. U-Nets such as [SAUNet](https://link.springer.com/chapter/10.1007/978-3-030-59719-1_77) are becoming more widely used in medical image segmentation.

## Acknowledgement

COSTPRE is jointly developed by [Mark Zaidi](https://github.com/MarkZaidi) and [Haotian Cui](https://github.com/subercui), whom are PhD students in the [Wouters-Koritzinsky](http://www.wklab.org/) and [Wang](https://wanglab.ml/) labs, respectively. To cite, please use:
```
Abstract PO-018: Computational staining of tumor hypoxia from H&E images using convolutional neural networks
Mark Zaidi, Haotian Cui, Bo Wang, Trevor D. McKee and Bradly G. Wouters
Clin Cancer Res March 1 2021 (27) (5 Supplement) PO-018; DOI: 10.1158/1557-3265.ADI21-PO-018
```

# Usage
## Dependencies

```bash
pip install -r requirements.txt
```

## Training

First prepare the training images, run

```bash
cd data
python img_prep.py --source-folder path_to_source --target-folder path_to_target
cd -
```

To train the model, simply run

```bash
# request excution permission for the first time
chmod +x train.sh

./train.sh
```

Be aware that you can specific a image to plot during training by setting the `--test-img` argument in `train.sh`.

## Inference

We provide the `infer` method in the `train.py` for online inference. First provide images of RGB H&E slide, `necrosis.png` and `perfusion.png`; Then run the following command to predict the hypoxia output. The output will be saved as `predict.png`.

```bash
python train.py --infer \
        --patch-size 128 \
        --model path_to_checkpoint_file \
        --test-img path_to_source_images
```
