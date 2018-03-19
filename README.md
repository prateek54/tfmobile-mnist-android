# MNIST with TensorFlow Mobile on Android

This project demonstrates how to use [TensorFlow Mobile](https://www.tensorflow.org/mobile/mobile_intro) on Android for handwritten digits classification from MNIST.


## How to build from scratch

### Requirement

- TensorFlow 1.6.0
- Python 3.6, NumPy 1.14.1
- Android Studio 3.0, Gradle 4.1


### Step 1. Training

The model is defined in [mnist.py](https://github.com/nex3z/tfmobile-mnist-android/blob/master/model/mnist.py), run the following command to train the model.

```
python train.py --model_dir ./saved_model --iterations 10000
```

After training, a collection of checkpoint files and a frozen GraphDef file `mnist.pb` will be generated in `./saved_model`.

You can test the model on test set using the command below.

```
python test.py --model_dir ./saved_model
```

A pre-trained model can be downloaded from [here](https://github.com/nex3z/tfmobile-mnist-android/releases/download/v1.0.0/mnist-10000.zip).


### Step 2. Model optimization

TensorFlow provides [optimize_for_inference.py](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/tools/optimize_for_inference.py) to optimize the model by removing parts of a graph that are only needed for training.

Navigate to the TensorFlow repository directory, run the following command to optimize the model.

```
python tensorflow/python/tools/optimize_for_inference.py \
    --input=model_path/mnist.pb \
    --output=output_path/mnist_optimized.pb \
    --input_names=x \
    --output_names=output   
```

The `input_file` argument should point to the TensorFlow GraphDef file (`mnist.pb`) trained in Step 1. The `output_file` argument specifies the location for the converted model.

Notice that the `mnist.pb` generated by [train.py](https://github.com/nex3z/tfmobile-mnist-android/blob/master/train.py) is already frozen, otherwise we will have to freeze the graph first by using [freeze_graph.py](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/tools/freeze_graph.py).

A optimized model file can be downloaded from [here](https://github.com/nex3z/tfmobile-mnist-android/releases/download/v1.0.0/mnist_optimized.pb).


### Step 3. Build Android app

Copy the `mnist_optimized.pb` generated in Step 2 to `/android/app/src/main/assets`, then build and run the app.

The [Classifer](https://github.com/nex3z/tfmobile-mnist-android/blob/master/android/app/src/main/java/com/nex3z/tfmobilemnist/Classifier.java) creates a [TensorFlowInferenceInterface](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/android/java/org/tensorflow/contrib/android/TensorFlowInferenceInterface.java) from  `mnist_optimized.pb`. The TensorFlowInferenceInterface provides an interface for inference, which is included in the following library.

```
implementation "org.tensorflow:tensorflow-android:1.5.0"
```


## Credits

- The basic model architecture comes from [tensorflow-mnist-tutorial](https://github.com/martin-gorner/tensorflow-mnist-tutorial).
- The official TensorFlow Mobile [Android demo](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/examples/android).
- The [FingerPaint](https://android.googlesource.com/platform/development/+/master/samples/ApiDemos/src/com/example/android/apis/graphics/FingerPaint.java) from Android API demo.
