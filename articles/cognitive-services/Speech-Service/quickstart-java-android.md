---
title: 'Guida introduttiva: Riconoscimento vocale, Java (Android) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Java in Android con Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2f728231c01056ecb8709f84f13e834ef3618dc8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803304"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscere i contenuti vocali in Java su Android con Speech SDK

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-java-android.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come sviluppare un'applicazione Java per Android usando Speech SDK di Servizi cognitivi per la trascrizione del riconoscimento vocale.
L'applicazione è basata sul pacchetto Maven di Speech SDK e su Android Studio 3.3.
Speech SDK è attualmente compatibile con i dispositivi Android con processori ARM a 32/64 bit e Intel x86/x64 compatibili.

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessaria una chiave di sottoscrizione di servizi Voce. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente servizi Voce](get-started.md).

## <a name="create-and-configure-a-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Creare l'interfaccia utente

Verrà creata un'interfaccia utente di base per l'applicazione. Modificare il layout per l'attività principale, `activity_main.xml`. Inizialmente, il layout include una barra del titolo con il nome dell'applicazione e un TextView contenente il testo "Hello World!".

* Fare clic sull'elemento TextView. Modificare l'attributo ID nell'angolo superiore destro in `hello`.

* Dalla tavolozza in alto a sinistra nella finestra `activity_main.xml`, trascinare un pulsante nello spazio vuoto sopra il testo.

* Negli attributi del pulsante a destra, nel valore dell'attributo `onClick` immettere `onSpeechButtonClicked`. Verrà scritto un metodo con questo nome per gestire l'evento del pulsante.  Modificare l'attributo ID nell'angolo superiore destro in `button`.

* Usare l'icona a forma di bacchetta magica nella parte superiore della finestra di progettazione per dedurre i vincoli di layout.

  ![Screenshot dell'icona Bacchetta magica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Il testo e la versione grafica dell'interfaccia utente dovrebbero essere simili all'immagine seguente:

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine `MainActivity.java`. Sostituire tutto il codice in questo file con quanto segue.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Il metodo `onCreate` include codice che richiede autorizzazioni Internet e microfono e che inizializza l'associazione di piattaforma nativa. Configurare le associazioni di piattaforma nativa è obbligatorio una sola volta. È consigliabile eseguirla all'inizio dell'inizializzazione dell'applicazione.

   * Il metodo `onSpeechButtonClicked` è, come indicato in precedenza, il gestore di clic del pulsante. La pressione di un pulsante attiva la trascrizione del riconoscimento vocale.

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Inoltre, sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Connettere il dispositivo Android al PC di sviluppo. Assicurarsi di avere la [modalità di sviluppo e il debug USB](https://developer.android.com/studio/debug/dev-options) abilitati sul dispositivo.

1. Per compilare l'applicazione, premere Ctrl + F9 o scegliere **Compila** > **Crea progetto** dalla barra del menu.

1. Per avviare l'applicazione, premere MAIUSC + F10 oppure scegliere **Esegui** > **Esegui 'app'** .

1. Nella finestra di destinazione della distribuzione che viene visualizzata, selezionare il dispositivo Android.

   ![Screenshot della finestra di schermata Seleziona destinazione di distribuzione](media/sdk/qs-java-android-12-deploy.png)

Fare clic sul pulsante dell'applicazione per iniziare una sezione dedicata al riconoscimento vocale. I 15 secondi successivi di riconoscimento vocale in lingua inglese verranno inviati ai servizi Voce e trascritti. Il risultato viene visualizzato nell'applicazione Android e nella finestra di logcat in Android Studio.

![Screenshot dell'applicazione Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
