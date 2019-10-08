---
title: 'Guida introduttiva: Sintesi vocale, Java (Android) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale in Java in Android con Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803762"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Guida introduttiva: Eseguire la sintesi vocale in Java su Android con Speech SDK

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-java-android.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-java-android.md).

Questo articolo spiega come sviluppare un'applicazione Java per Android usando Speech SDK di Servizi cognitivi per eseguire la sintesi vocale.
L'applicazione è basata sulla versione 1.7.0 del pacchetto Maven di Speech SDK e su Android Studio 3.3.
Speech SDK è attualmente compatibile con i dispositivi Android con processori ARM a 32/64 bit e Intel x86/x64 compatibili.

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessaria una chiave di sottoscrizione di servizi Voce. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente servizi Voce](get-started.md).

## <a name="create-and-configure-a-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Creare l'interfaccia utente

Verrà creata un'interfaccia utente di base per l'applicazione. Modificare il layout per l'attività principale, `activity_main.xml`. Inizialmente, il layout include una barra del titolo con il nome dell'applicazione e un TextView contenente il testo "Hello World!".

1. Fare clic sull'elemento TextView. Modificare l'attributo ID nell'angolo superiore destro in `outputMessage` e trascinarlo nella schermata inferiore. Eliminare il testo.

1. Dalla tavolozza in alto a sinistra nella finestra `activity_main.xml`, trascinare un pulsante nello spazio vuoto sopra il testo.

1. Negli attributi del pulsante a destra, nel valore dell'attributo `onClick` immettere `onSpeechButtonClicked`. Verrà scritto un metodo con questo nome per gestire l'evento del pulsante.  Modificare l'attributo ID nell'angolo superiore destro in `button`.

1. Trascinare un testo normale nello spazio sopra il pulsante; modificare il relativo attributo ID in `speakText` e modificare l'attributo testo in `Hi there!`.

1. Usare l'icona a forma di bacchetta magica nella parte superiore della finestra di progettazione per dedurre i vincoli di layout.


    ![Screenshot dell'icona Bacchetta magica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Il testo e la versione grafica dell'interfaccia utente dovrebbero essere simili all'immagine seguente:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine `MainActivity.java`. Sostituire tutto il codice in questo file con quanto segue.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Il metodo `onSpeechButtonClicked` è, come indicato in precedenza, il gestore di clic del pulsante. La pressione di un pulsante attiva la sintesi vocale.

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Inoltre, sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Connettere il dispositivo Android al PC di sviluppo. Assicurarsi di avere la [modalità di sviluppo e il debug USB](https://developer.android.com/studio/debug/dev-options) abilitati sul dispositivo. In alternativa, creare un [emulatore Android](https://developer.android.com/studio/run/emulator).

1. Per compilare l'applicazione, premere Ctrl + F9 o scegliere **Compila** > **Crea progetto** dalla barra del menu.

1. Per avviare l'applicazione, premere MAIUSC + F10 oppure scegliere **Esegui** > **Esegui 'app'** .

1. Nella finestra di destinazione della distribuzione che viene visualizzata, scegliere il dispositivo o l'emulatore Android.

   ![Screenshot della finestra di schermata Seleziona destinazione di distribuzione](media/sdk/qs-java-android-12-deploy.png)

Immettere un testo e premere il pulsante nell'applicazione per iniziare una sezione dedicata alla sintesi vocale. Sarà possibile ascoltare l'audio sintetizzato dall'altoparlante predefinito e sullo schermo verranno visualizzate le informazioni `speech synthesis succeeded`.

![Screenshot dell'applicazione Android](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
