---
title: Trasmettere il codec audio compresso con l'SDK di riconoscimento vocale in Android-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire lo streaming di audio compresso in servizi vocali di Azure con l'SDK di riconoscimento vocale in Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: df5eb123a2fd47a3eceea8153786442bf56a2718
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803833"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Uso dell'input audio compresso di codec con Speech SDK in Android

L'API del **flusso di input audio compresso** dell'SDK vocale fornisce un modo per trasmettere audio compresso al servizio di riconoscimento vocale usando PullStream o PushStream.

> [!IMPORTANT]
> Il flusso audio di input compresso è attualmente supportato per [ C++, C#e Java in Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md). È supportata anche per Java in Android e [Objective-C nella piattaforma iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> È necessario l'SDK di riconoscimento vocale 1.7.0 o versione successiva.

Per WAV/PCM, vedere la documentazione sulla voce principale.  Al di fuori di WAV/PCM, sono supportati i formati di input compressi di codec seguenti:

- MP3
- OPUS/OGG
- FLAC
- ALEGGE nel contenitore WAV
- MULAW nel contenitore WAV

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Prerequisiti per l'uso dell'input audio compresso con codec in Android

Il codec audio compresso viene implementato con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza, i file binari gstreamer non vengono compilati con l'SDK. È necessario usare i file binari predefiniti per Android. Per scaricare le librerie predefinite, vedere [installazione per lo sviluppo per Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

`libgstreamer_android.so` è obbligatorio. Assicurarsi che i plug-in GStreamer siano collegati in `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Di seguito viene fornito un esempio `Android.mk` e un file `Application.mk`. Per creare l'oggetto condiviso GStreamer, seguire questa procedura: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

È possibile compilare `libgstreamer_android.so` usando il comando seguente in Ubuntu 16,04 o 18,04. Le righe di comando seguenti sono state testate solo per la [versione 1.14.4 di GStreamer Android](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) con [Android NDK B16B.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Una volta creato l'oggetto condiviso (libgstreamer_android. so), lo sviluppatore dell'applicazione deve inserire l'oggetto condiviso nell'app Android, in modo che possa essere caricato da Speech SDK.

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio con input audio compresso codec

Per eseguire lo streaming in un formato audio compresso per i servizi di riconoscimento vocale, creare `PullAudioInputStream` o `PushAudioInputStream`. Quindi, creare un `AudioConfig` da un'istanza della classe Stream, specificando il formato di compressione del flusso.

Si supponga di disporre di una classe di flusso di input denominata `myPullStream` e di usare OPUS/OGG. Il codice potrebbe essere simile al seguente:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
