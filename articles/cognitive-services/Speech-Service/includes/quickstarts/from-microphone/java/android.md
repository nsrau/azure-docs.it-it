---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, Java (Android) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Java in Android con Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: f0d9a4d2a86b13d5e2d37a900f27d2977f63de14
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505780"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=android)

## <a name="create-a-user-interface"></a>Creare un'interfaccia utente

Ora verrà creata un'interfaccia utente di base per l'applicazione. Modificare il layout per l'attività principale, `activity_main.xml`. Inizialmente, il layout include una barra del titolo con il nome dell'applicazione e un elemento TextView contenente il testo "Hello World!".

* Selezionare l'elemento TextView. Modificare l'attributo ID nell'angolo superiore destro in `hello`.

* Dalla tavolozza in alto a sinistra nella finestra `activity_main.xml`, trascinare un pulsante nello spazio vuoto sopra il testo.

* Negli attributi del pulsante a destra, nel valore dell'attributo `onClick` immettere `onSpeechButtonClicked`. Verrà scritto un metodo con questo nome per gestire l'evento del pulsante. Modificare l'attributo ID nell'angolo superiore destro in `button`.

* Usare l'icona a forma di bacchetta magica nella parte superiore della finestra di progettazione per dedurre i vincoli di layout.

  ![Screenshot dell'icona Bacchetta magica](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Il testo e la versione grafica dell'interfaccia utente dovrebbero essere simili all'immagine seguente:

![Interfaccia utente](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine `MainActivity.java`. Sostituire tutto il codice in questo file con quanto segue:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Il metodo `onCreate` include codice che richiede autorizzazioni Internet e microfono e che inizializza l'associazione di piattaforma nativa. Configurare le associazioni di piattaforma nativa è obbligatorio una sola volta. È consigliabile eseguirla all'inizio dell'inizializzazione dell'applicazione.

   * Il metodo `onSpeechButtonClicked` è, come indicato in precedenza, il gestore di clic del pulsante. La pressione di un pulsante attiva la trascrizione del riconoscimento vocale.

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire inoltre la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Connettere il dispositivo Android al PC di sviluppo. Assicurarsi di avere la [modalità di sviluppo e il debug USB](https://developer.android.com/studio/debug/dev-options) abilitati nel dispositivo.

1. Per compilare l'applicazione, premere CTRL + F9 o scegliere **Build** > **Make Project** (Compila > Crea progetto) dalla barra del menu.

1. Per avviare l'applicazione, premere MAIUSC + F10 oppure scegliere **Run** > **Run 'app'** (Esegui > Esegui 'app').

1. Nella finestra di destinazione della distribuzione visualizzata, selezionare il dispositivo Android.

   ![Screenshot della finestra di schermata Seleziona destinazione di distribuzione](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Fare clic sul pulsante nell'applicazione per iniziare una sezione dedicata al riconoscimento vocale. I 15 secondi successivi di riconoscimento vocale in lingua inglese verranno inviati ai servizi Voce e trascritti. Il risultato viene visualizzato nell'applicazione Android e nella finestra di logcat in Android Studio.

![Screenshot dell'applicazione Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]

