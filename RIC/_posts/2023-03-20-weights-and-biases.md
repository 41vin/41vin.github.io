---
layout: post
title: Weights and Biases
description: >
  실험관리(Weights and Biases)에 관한 글을 정리한 것입니다.
image: /assets/img/blog/MLOps.png
sitemap: false
---

# 실험관리 개요
## 실험관리가 필요한 이유
- 가장 성능 좋은 모델을 파악하기 위해
- 어떤 하이퍼파라미터가 제일 중요한지 파악하기 위해


# Weights and Biases
머신러닝 프로젝트의 실험관리 솔루션을 제공하고 있습니다
- 연동이 쉽습니다
- 어디서나 접근이 가능합니다
- 재연가능한 모델
- GPU 사용률
- 실시간으로 퍼포먼스 디버깅이 가능합니다
  

## Weights and Biases 실습(Quickstart)
![800x400](/assets/img/blog/wandb/0320-wandb1.png "WanDB1")

위의 코드는 간단하게 training loop를 시험삼아 돌려본 것입니다. 결과는 아래와 같이 나오게 됩니다.


![800x400](/assets/img/blog/wandb/0320-wandb2.png "WanDB2")

맨 윗줄에 있는 링크를 들어가게 되면 실험관리 대시보드를 확인할 수 있습니다.

![800x400](/assets/img/blog/wandb/0320-wandb-3.png "WanDB3")

## Weights and Biases 실습(From Scratch)

[실습 Colab Notebook](https://colab.research.google.com/drive/19aRTg2LqzWqfG-EGftC4c_jrlIYHvCLF?usp=sharing) 링크를 첨부하였습니다.

### 코드 설명

``` python
!pip install wandb -q
```

Python 패키지 관리자 pip을 사용하여 `wandb`(Weights & Library) 라이브러리를 설치합니다.

{: note}


``` python
!wandb login
```
- Weibhts & Biases에 API 키를 사용하여 로그인합니다.

``` python
import wandb
```
- `wandb` 라이브러리를 호출합니다.


``` python
 wandb.init(project = 'wandb-tutorial-geon',
           config = {
               "layer_1": 128,
               "learning_rate": 0.01,
               "dropout_rate": 0.2,
               "layer_1_activation": "relu"
           }
          )
```


`wandb.init()` 함수를 사용하여 새로운 머신러닝 실험을 시작하게  됩니다. 이 함수를 호출할 때 아래와 같은 매개변수를 설정할 수 있습니다.

- **project**: 실험 결과를 그룹화하기 위한 프로젝트의 이름입니다. Weights & Biases 대시보드에서 해당 프로젝트를 식별하는 데 사용됩니다.
- **confi**g: 실험 설정을 담은 딕셔너리입니다. 모델의 구조, 학습률, 드롭아웃 비율과 같은 하이퍼파라미터 및 기타 메타데이터가 포함됩니다. 본 코드에서 사용된 하이퍼파라미터는 다음과 같습니다.
  - **layer_1**: 첫 번째 레이어에서 뉴런 수를 128로 설정합니다.
  - **learning_rate**: 모델의 학습률을 0.01로 설정합니다.
  - **dropout_rate**: 드롭아웃은 모델이 학습하는 동안 무작위로 일부 뉴런을 비활성화하여 overfittin을 방지하는 기법입니다.
  - **layer_1_activation**: 첫 번째 레이어의 활성화 합수르 ReLU를 사용합니다.

``` python
config = wandb.config
```

- `wandb`의 설정을 `config` 변수에 저장합니다. 이 설정은 앞서 `wandb.init()` 함수를 통해 초기화한 실험 설정을 포함합니다. 여기에는 모델의 하이퍼파라미터가 포함됩니다.

``` python
from wandb.keras import WandCallback
```

- `wandb`의 Keras 콜백을 가져옵니다. 이 콜백은 모델을 학습할 때 `wandb` 대시보드에 학습과정을 자동으로 기록합니다.

```
import tensorflow as tf
```

- TensorFlow 라이브러리를 `tf`라는 별칭으로 가져옵니다. TensorFlow는 딥러닝 모델을 구축하고 학습하기 위한 오픈소스 라이브러리입니다.

``` python
mnist = tf.keras.datasets.mnist
(train_x, train_y), (test_x, test_y) = mnist.load_data()
```

- MNIST 데이터셋을 로드하고, 학습용과 테스트용으로 분리합니다.


``` python
train_x, test_x = train_x / 255.0, test_x / 255.0
```

- 이미지 데이터를 정규화하여 0과 1 사이의 값으로 스케일링합니다. 이는 모델 학습을 더 효율적으로 만듭니다.

``` python
model = tf.keras.models.Sequential([
    tf.keras.layers.Flatten(input_shape = (28, 28)),
    tf.keras.layers.Dense(config.layer_1, activation = config.layer_1_activation),
    tf.keras.layers.Dropout(config.dropout_rate),
    tf.keras.layers.Dense(10, activation = 'softmax')
])
```

- **모델 구축**: 순차 모델을 정의합니다. 모델은 아래와 같은 레이어로 구성됩니다.
  - `Flatten`: 28X28 이미지를 784(28X28) 크기의 1D 벡터로 평탄화합니다.
  - `Dense`: 설정된 유닛 수와 활성화 함수를 가진 완전 연결 레이어입니다. `config`에서 정의된 값을 사용합니다.
  - `Dropout`: Overfitting을 방지하기 위해 레이어의 일부 연결을 무작위로 끊습니다.
  - `Dense`: 10개의 출력 유닛과 `softmax` 활성화 함수를 가집니다.

``` python
model.compile(optimizer= tf.keras.optimizers.Adam(learning_rate=config.learning_rate),
              loss='sparse_categorical_crossentropy',
              metrics = ['accuracy'])
```

- **model compile()**: 모델을 학습하기 전에 학습 과정을 구상합니다.
  

``` python
model.fit(train_x, train_y, epochs=5, callbacks=[WandbCallback()])
```

- **epoch**: 데이터셋을 몇 번 반복해서 학습할지 지정합니다.
- **WandCallback()**: 학습 과정에서 발생하는 데이터를 `wandb`에 자동으로 기록하도록 합니다.

``` python
model.evaluate(test_x, test_y)
```

- **model.evaluate()**: 테스트 데이터셋에 대한 모델의 성능을 평가합니다.