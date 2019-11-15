---
title: 'Esercitazione: Eseguire il modello TensorFlow in Python - Servizio visione artificiale personalizzato'
titleSuffix: Azure Cognitive Services
description: Eseguire un modello TensorFlow in Python. Questo articolo si applica solo ai modelli esportati dai progetti di classificazione delle immagini nel servizio Visione personalizzata.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: 00d3b1dce9bacc01c8319c678ab27a3d4cc8da88
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718430"
---
# <a name="tutorial-run-tensorflow-model-in-python"></a>Esercitazione: Eseguire il modello TensorFlow in Python

Dopo aver [esportato il modello TensorFlow](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dal Servizio visione artificiale personalizzato, questa guida introduttiva mostrerà come usare questo modello in locale per classificare le immagini.

> [!NOTE]
> Questa esercitazione è valida solo per i modelli esportati da progetti di classificazione delle immagini.

## <a name="prerequisites"></a>Prerequisiti

Per usare l'esercitazione, è necessario seguire questa procedura:

- Installare Python 2.7+ o Python 3.5+.
- Installare pip.

È quindi necessario installare anche i pacchetti seguenti:

```
pip install tensorflow
pip install pillow
pip install numpy
pip install opencv-python
```

## <a name="load-your-model-and-tags"></a>Caricare il modello e i tag

Il file ZIP scaricato contiene un file model.pb e un file labels.txt. Questi file rappresentano il modello sottoposto a training e le etichette di classificazione. Il primo passaggio consiste nel caricare il modello nel progetto.

```Python
import tensorflow as tf
import os

graph_def = tf.compat.v1.GraphDef()
labels = []

# These are set to the default names from exported models, update as needed.
filename = "model.pb"
labels_filename = "labels.txt"

# Import the TF graph
with tf.io.gfile.GFile(filename, 'rb') as f:
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')

# Create a list of labels.
with open(labels_filename, 'rt') as lf:
    for l in lf:
        labels.append(l.strip())
```

## <a name="prepare-an-image-for-prediction"></a>Preparare un'immagine per la stima

È necessario eseguire alcuni passaggi per preparare l'immagine in modo che sia della forma corretta per la stima. Questi passaggi simulano la modifica dell'immagine eseguita durante il training:

### <a name="open-the-file-and-create-an-image-in-the-bgr-color-space"></a>Aprire il file e creare un'immagine nello spazio colore BGR

```Python
from PIL import Image
import numpy as np
import cv2

# Load from a file
imageFile = "<path to your image file>"
image = Image.open(imageFile)

# Update orientation based on EXIF tags, if the file has orientation info.
image = update_orientation(image)

# Convert to OpenCV format
image = convert_to_opencv(image)
```

### <a name="handle-images-with-a-dimension-1600"></a>Gestire immagini di dimensioni superiori a 1600

```Python
# If the image has either w or h greater than 1600 we resize it down respecting
# aspect ratio such that the largest dimension is 1600
image = resize_down_to_1600_max_dim(image)
```

### <a name="crop-the-largest-center-square"></a>Ritagliare il riquadro centrale più grande

```Python
# We next get the largest center square
h, w = image.shape[:2]
min_dim = min(w,h)
max_square_image = crop_center(image, min_dim, min_dim)
```

### <a name="resize-down-to-256x256"></a>Ridimensionare a 256x256

```Python
# Resize that square down to 256x256
augmented_image = resize_to_256_square(max_square_image)
```

### <a name="crop-the-center-for-the-specific-input-size-for-the-model"></a>Ritagliare il centro per le dimensioni di input specifiche per il modello

```Python
# Get the input size of the model
with tf.compat.v1.Session() as sess:
    input_tensor_shape = sess.graph.get_tensor_by_name('Placeholder:0').shape.as_list()
network_input_size = input_tensor_shape[1]

# Crop the center for the specified network_input_Size
augmented_image = crop_center(augmented_image, network_input_size, network_input_size)

```

I passaggi precedenti usano le funzioni helper seguenti:

```Python
def convert_to_opencv(image):
    # RGB -> BGR conversion is performed as well.
    image = image.convert('RGB')
    r,g,b = np.array(image).T
    opencv_image = np.array([b,g,r]).transpose()
    return opencv_image

def crop_center(img,cropx,cropy):
    h, w = img.shape[:2]
    startx = w//2-(cropx//2)
    starty = h//2-(cropy//2)
    return img[starty:starty+cropy, startx:startx+cropx]

def resize_down_to_1600_max_dim(image):
    h, w = image.shape[:2]
    if (h < 1600 and w < 1600):
        return image

    new_size = (1600 * w // h, 1600) if (h > w) else (1600, 1600 * h // w)
    return cv2.resize(image, new_size, interpolation = cv2.INTER_LINEAR)

def resize_to_256_square(image):
    h, w = image.shape[:2]
    return cv2.resize(image, (256, 256), interpolation = cv2.INTER_LINEAR)

def update_orientation(image):
    exif_orientation_tag = 0x0112
    if hasattr(image, '_getexif'):
        exif = image._getexif()
        if (exif != None and exif_orientation_tag in exif):
            orientation = exif.get(exif_orientation_tag, 1)
            # orientation is 1 based, shift to zero based and flip/transpose based on 0-based values
            orientation -= 1
            if orientation >= 4:
                image = image.transpose(Image.TRANSPOSE)
            if orientation == 2 or orientation == 3 or orientation == 6 or orientation == 7:
                image = image.transpose(Image.FLIP_TOP_BOTTOM)
            if orientation == 1 or orientation == 2 or orientation == 5 or orientation == 6:
                image = image.transpose(Image.FLIP_LEFT_RIGHT)
    return image
```

## <a name="predict-an-image"></a>Stimare un'immagine

Una volta preparata l'immagine come tensore, è possibile passarla al modello per una stima:

```Python

# These names are part of the model and cannot be changed.
output_layer = 'loss:0'
input_node = 'Placeholder:0'

with tf.compat.v1.Session() as sess:
    try:
        prob_tensor = sess.graph.get_tensor_by_name(output_layer)
        predictions, = sess.run(prob_tensor, {input_node: [augmented_image] })
    except KeyError:
        print ("Couldn't find classification output layer: " + output_layer + ".")
        print ("Verify this a model exported from an Object Detection project.")
        exit(-1)
```

## <a name="view-the-results"></a>View the results

Sarà quindi necessario mappare nuovamente alle etichette i risultati dell'esecuzione del tensore dell'immagine nel modello.

```Python
    # Print the highest probability label
    highest_probability_index = np.argmax(predictions)
    print('Classified as: ' + labels[highest_probability_index])
    print()

    # Or you can print out all of the results mapping labels to probabilities.
    label_index = 0
    for p in predictions:
        truncated_probablity = np.float64(np.round(p,8))
        print (labels[label_index], truncated_probablity)
        label_index += 1
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come inserire il modello in un'applicazione per dispositivi mobili:
* [Use exported Tensorflow model in an Android application](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) (Usare il modello Tensorflow esportato in un'applicazione Android)
* [Use exported CoreML model in a Swift iOS application](https://go.microsoft.com/fwlink/?linkid=857726) (Usare il modello CoreML esportato in un'applicazione iOS Swift)
* [Use exported CoreML model in an iOS application with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) (Usare il modello CoreML esportato in un'applicazione iOS con Xamarin)
