# nnUNet
比赛复现说明总结
 
任务：Dataset601，DICOM二维切片转3D，使用nnUNet‑v2的 3d_fullres 做4类分割；训练fold取0/1，学习率0.005/0.01/0.02，每50个epoch保存模型，学习率、保存周期在自定义Trainer修改，fold在训练命令指定。
 
1.环境复现
 
1. 通过conda创建python3.10环境，安装torch、nnunetv2、SimpleITK等依赖库。
2. 每次新开终端需要设置3个nnUNet环境变量；提供代码可校验版本、GPU可用性。
 
2.原始数据预处理
 
1.  convert_dicom_to_nifti.py ：训练集DICOM转NIfTI，自动选切片最多序列，支持断点续跑。
2.  process_masks_and_json.py ：参考CT图像空间重采样mask，生成4类任务的 dataset.json 。
3.  convert_test_dicom.py ：测试集DICOM转NIfTI，输出至imagesTs目录。
 
3.nnUNet预处理
 
执行 nnUNetv2_preprocess 做指纹、plans、重采样归一化；手动删除2d配置，仅保留3d_fullres配置。
 
4.模型训练
 
1.  custom_trainers.py ：继承官方Trainer，改写 initial_lr 学习率、 save_every 保存间隔。
2. 将自定义Trainer放到nnUNet对应目录，训练命令用 ‑tr 指定自定义训练器， ‑f 指定fold（0或1）启动训练。
 
5.推理预测
 
运行 run_infer.sh 一键推理，输出重命名为比赛提交格式 {id}_mask.nii.gz ；附带代码脚本校验输出文件数量与命名正确性。
 
6.交付文件清单
 
- 脚本类：环境配置 environment.yml 、3个DICOM转换脚本、mask与json生成脚本、自定义Trainer、训练shell、推理shell、复现notebook。
- 目录：原始数据 nnUNet_raw 、预处理缓存 nnUNet_preprocessed 、训练权重 nnUNet_results 、推理输出 predict_output 。
 
