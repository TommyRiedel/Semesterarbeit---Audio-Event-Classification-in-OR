# Semesterarbeit: "Development of a Deep Learning based Audio Event Classification for the Operating Room"

# Motivation
The estimated time utilization of the operating room can be updated during the operation by recognizing it's current phase.
Hardly any work currently uses sounds in the operating room to estimate the current status of the operation, although audible information is easily accessible and can be meaningful.
This semester thesis therefore attempts to recognize specific sounds in an operating room in order to use the operating room more efficiently and thus being able to help more patients.

# Classes:

In an initial study with a smaller data set (200 data per class), all of the following classes were used.
Due to the smaller amount of available data, the classes marked with an * had to be neglected for further investigations with larger amounts of data per class.

  - Monopolar coagulation mode
  - Bipolar coagulation mode
  - Cutting mode
  - Hämolock fast coagulation mode
  - Table up
  - Table down
  - Table tilt
  - Table forth/back
  - Phone call (*)
  - DaVinci sounds
  - Idle

# Generation and preprocessing of the data:
The audio of 23 open / laparoscopic or robot-assisted (DaVinci) interventions (= 74h 27min) were recorded at the Klinikum Rechts der Isar (operating room 9).
Due to the limited amount of data, some sounds cannot be considered.
An example are commands such as "Schnitt", which is usually given by the surgeon at the beginning of an operation or the sound of a stappler, which occurs relatively at the end of this type of operations.
The amount of data considerably varies between the above classes.
Data augmentation and transfer learning are used to reduce the necessary amount of training data.
The recordings are divided into 0.7 second snippets using Audacity and a window function (compromise between accuracy and real-time capability).
Since transfer learning with models pretrained on ImageNet are used for classification, the audio files are converted to spectrograms (image-like).
To increase the influence of the low frequency range (= human hearing) the mel-scale is used (64 filter bands - tradeoff accuracy and computational resources).
The size of the mel-power spectrogram images are adjusted to match the input size of the corresponding model.

Normalized waveform        |  Mel-power spectrogram
:-------------------------:|:-------------------------:
<img alt="Bildschirmfoto 2023-11-09 um 14 11 58" src="https://github.com/TommyRiedel/Operating-Room-Audio-Event-Classification/assets/33426324/5e8b547b-d2b5-4549-b5d7-c79a84051cce"> | <img alt="Bildschirmfoto 2023-11-09 um 14 21 27" src="https://github.com/TommyRiedel/Operating-Room-Audio-Event-Classification/assets/33426324/e39bc789-b0a1-47e2-94f4-896a98d3a35d">

# Feature extraction and Classification:
Both feature extraction and classification are performed using the following deep learning models pretrained on ImageNet.
These consist of several convolutional layers, which extract meaningful features regardless of their position in the image/spectrogram.
The first layers extract low-level features such as curves and edges.
These are abstracted to higher order features in the deeper layers.
This is known as transfer learning, in which the knowledge already learned on one task is transferred to another task. 
The weights of the first shifts are frozen and only the last layers (classification) are retrained.
It is important that the data from the two tasks are similar so that the extracted features are meaningful for the classification of the new task.
From the **keras** library different models pretrained on the ImageNet dataset are freely available.
This type of approach is particularly relevant when the amount of data is small and therefore not sufficient to train a deep model from scratch.

# Models:
  - EfficientNetB4
  - InceptionV3
  - ResNet50
  - VGG19
  - **MobileNet**
  - DenseNet169

# Data Augmentation:
Artificially increases the amount of training data and in this case especially for the classes with less data available.
In this work, two different augmentation approaches are compared.
On the one hand, the spectrograms are augmented by masking out some time and/or frequency channels of the spectrogram.
On the other hand, theoriginal wave-file is augmented via amplification or by adding a white noise.

Augmented spectrogram       |  Augmented wave-file
:-------------------------:|:-------------------------:
<img width="662" alt="Bildschirmfoto 2023-11-13 um 13 04 11" src="https://github.com/TommyRiedel/Operating-Room-Audio-Event-Classification/assets/33426324/a1fe10f8-4459-4d17-9cc6-31004c258358"> |
<img width="659" alt="Bildschirmfoto 2023-11-13 um 13 05 28" src="https://github.com/TommyRiedel/Operating-Room-Audio-Event-Classification/assets/33426324/f085fe41-6d48-46b9-9f28-74e515a7dce6">

