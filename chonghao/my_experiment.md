# First, extract the frames of each video.
python scripts/preprocess_dynerf.py --datadir data/dynerf/cook_spinach


python scripts/preprocess_mytest.py --datadir data/dytime/cook_spinach
# Second, generate point clouds from input data.
bash colmap.sh data/dynerf/cook_spinach llff

bash colmap.sh data/dytime/cook_spinach llff


bash colmap_test.sh data/dytime/cook_spinach times

# Third, downsample the point clouds generated in the second step.
python scripts/downsample_point.py data/dynerf/cook_spinach/colmap/dense/workspace/fused.ply data/dynerf/cook_spinach/points3D_downsample2.ply


python scripts/downsample_point.py data/dynerf/cook_spinach/colmap/dense/workspace/fused.ply data/dynerf/cook_spinach/points3D_downsample2.ply


python scripts/downsample_point.py data/dytime/cook_spinach/colmap/dense/workspace/fused.ply data/dytime/cook_spinach/points3D_downsample2.ply

# Finally, train.
python -m debugpy --listen 5678 --wait-for-client train.py -s data/dynerf/cook_spinach --port 6018 --expname "dynerf/cook_spinach" --configs arguments/dynerf/cook_spinach.py 



python  train.py -s data/dynerf/cook_spinach --port 6018 --expname "dynerf/cook_spinach" --configs arguments/dynerf/cook_spinach.py 


python -m debugpy --listen 5678 --wait-for-client train_timebase.py -s data/dytime/cook_spinach --port 6017 --expname "dytime/cook_spinach" --configs arguments/dytime/cook_spinach.py 


python train_timebase.py -s data/dytime/cook_spinach --port 6017 --expname "dytime/cook_spinach" --configs arguments/dytime/cook_spinach.py 

# After training
python render.py --model_path "output/dynerf/cook_spinach/"  --skip_train --configs arguments/dynerf/cook_spinach.py 


python -m debugpy --listen 5678 --wait-for-client render_timebase.py --model_path "output/dytime/cook_spinach/"  --skip_train --configs arguments/dytime/cook_spinach.py 