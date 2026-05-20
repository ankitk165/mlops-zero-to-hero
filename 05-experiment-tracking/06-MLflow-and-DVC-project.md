Please refer to the below repository for this lecture.

https://github.com/iam-veeramalla/Wine-Prediction-Model
1. Checkout and requirement installation
```
git clone https://github.com/iam-veeramalla/Wine-Prediction-Model.git
cd Wine-Prediction-Model/
python3 -m venv .venv
ls -a
source .venv/bin/activate
pip install -r requirements.txt
```

2. dvc setup

```
python3 -m pip install dvc dvc_s3
dvc init
dvc init -f
dvc add data/wine_sample.csv
ls data/
```

s3 bucket remote setup
```
aws s3 ls
aws s3 mb s3://wine-pred-model-dvc
aws s3 ls
dvc remote add -d wineremote s3://wine-pred-model-dvc/dvcfile
dvc push
```
git addition
```
git add .dvc/config data/wine_sample.csv.dvc
```

dvc pull
```
ls data/
rm data/wine_sample.csv
ls data
dvc pull
```

3. Train and experiment run

```
Code has parameters setup in a function, for mlflow and training algo. Can be overriden at runtime.
mlflow url is at localhost:7006. this needs to be running before, could be simple setup using python or on k8s and then port forward
setup an experiment and run, log parameters in run
```

```
python train.py
```
