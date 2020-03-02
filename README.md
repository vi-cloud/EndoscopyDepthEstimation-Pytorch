# Self-supervised Depth Estimation in Monocular Endoscopy


![](clip_1.gif) ![](clip_2.gif)

*Depth prediction from ours (2nd column), SfmLearner by Zhou et al. (3rd column), and GeoNet by Yin et al. (4th column). The first row displays results from a testing patient and camera which are not seen during training. The second row is from a training patient and camera. Depth maps are re-scaled with sparse depth maps generated from SfM results for visualization.*


This codebase implements the method described in the paper:

***Dense Depth Estimation in Monocular Endoscopy with Self-supervised Learning Methods***

Xingtong Liu, Ayushi Sinha, Masaru Ishii, Gregory D. Hager, Austin Reiter, Russell H. Taylor, Mathias Unberath

In ***IEEE Transactions on Medical Imaging (TMI)***


This work also won **Best paper award & Best presentation award** in International Workshop on Computer-Assisted and Robotic Endoscopy 2018

Please contact [**Xingtong Liu**](http://www.cs.jhu.edu/~xingtongl/) (xliu89@jh.edu) or [**Mathias Unberath**](https://www.cs.jhu.edu/faculty/mathias-unberath/) (unberath@jhu.edu) if you have any questions.

We kindly ask you to cite [TMI Early Access](https://ieeexplore.ieee.org/abstract/document/8889760) or [CARE Workshop](https://link.springer.com/chapter/10.1007/978-3-030-01201-4_15) if the code is used in your own work.
```
@ARTICLE{liu2019dense,
author={X. {Li} and A. {Sinha} and M. {Ishii} and G. D. {Hager} and A. {Reiter} and R. H. {Taylor} and M. {Unberath}},
journal={IEEE Transactions on Medical Imaging},
title={Dense Depth Estimation in Monocular Endoscopy with Self-supervised Learning Methods},
year={2019},
volume={},
number={},
pages={1-1},
keywords={Estimation;Endoscopes;Cameras;Videos;Training;Image reconstruction;Three-dimensional displays;Endoscopy;unsupervised learning;selfsupervised learning;depth estimation},
doi={10.1109/TMI.2019.2950936},
ISSN={},
month={},}
```
```
@incollection{liu2018self,
  title={Self-supervised Learning for Dense Depth Estimation in Monocular Endoscopy},
  author={Liu, Xingtong and Sinha, Ayushi and Unberath, Mathias and Ishii, Masaru and Hager, Gregory D and Taylor, Russell H and Reiter, Austin},
  booktitle={OR 2.0 Context-Aware Operating Theaters, Computer Assisted Robotic Endoscopy, Clinical Image-Based Procedures, and Skin Image Analysis},
  pages={128--138},
  year={2018},
  publisher={Springer}
}
```

## Instructions

1. Install all necessary python packages: ```torch, torchvision, opencv-python, numpy, tqdm, pathlib, torchsummary, tensorboardX, albumentations, argparse, pickle, plyfile, pyyaml, datetime, shutil, matplotlib, tensorflow-gpu```.

2. Generate training data from training videos using Structure from Motion (SfM) or Simultaneous Localization and Mapping (SLAM). In terms of the format, please refer to one training data example in this repository. We use SfM to generate training data in this work. Color images with the format of "{:08d}.jpg" are extracted from the video sequence where SfM is applied. ```camer_intrinsics_per_view``` stores the estimated camera intrinsic matrices for all registered views. In this example, since all images are from the same video sequence, we assume the intrinsic matrices are the same for all images. The first three rows in this file are focal length, x and y of the principal point of the camera of the first image. ```motion.yaml``` stores the estimated poses of the world coordinate system w.r.t. the corresponding camera coordinate system. ```selected_indexes``` stores all frame indexes of the video sequence. ```structure.ply``` stores the estimated sparse 3D reconstruction from SfM. ```undistorted_mask.bmp``` is a binary mask used to mask out blank regions of the video frames. ```view_indexes_per_point``` stores the indexes of the frames that each point in the sparse reconstruction gets triangulated with. The views per point are separated by -1 and the order of the points is the same as that in ```structure.ply```. We smooth out the point visibility information in the script to make the global scale recovery more stable and obtain more sparse points per frame for training. ```visible_view_indexes``` stores the original frame indexes of the registered views where valid camera poses are successfully estimated by SfM.

3. Run ```student_training.py``` with proper arguments for self-supervised learning. One example is:
```
/path/to/python /path/to/student_training.py --id_range 1 2 --input_downsampling 4.0 --network_downsampling 64 --adjacent_range 5 30 --input_size 256 320 --batch_size 8 --num_workers 8 --num_pre_workers 8 --validation_interval 1 --display_interval 50 --dcl_weight 5.0 --sfl_weight 20.0 --max_lr 1.0e-3 --min_lr 1.0e-4 --inlier_percentage 0.99 --training_patient_id 1 --testing_patient_id 1 --validation_patient_id 1 --number_epoch 100 --num_iter 1000 --architecture_summary --training_result_root "/path/to/training/directory" --training_data_root "/path/to/training/data"
```

4. Run ```evaluate.py``` to generate evaluation results. Apply any rigid registration algorithm to register the predicted point clouds to the corresponding CT model to calculate residual errors (this step may require manual point cloud initialization).


## Disclaimer

This codebase is only experimental and not ready for clinical applications.

Authors are not responsible for any accidents related to this repository.

This codebase is only allowed for non-commercial usage.

