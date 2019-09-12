---
title: "Esercitazione: Rilevare e incorniciare i visi in un'immagine con Android SDK"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione verrà creata una semplice app Android che usa l'API Viso per rilevare e incorniciare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 740b3fae81521fec2cba31e3b8fd161f767c4380
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858979"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Esercitazione: Creare un'app Android per rilevare e incorniciare i visi in un'immagine

In questa esercitazione si creerà una semplice applicazione Android che usa l'API Viso di Azure, tramite Java SDK, per rilevare i visi umani in un'immagine. L'applicazione mostra un'immagine selezionata e traccia una cornice attorno a ogni viso rilevato.

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Creare un'applicazione Android
> - Installare la libreria client dell'API Viso
> - Usare la libreria client per rilevare i visi in un'immagine
> - Tracciare una cornice intorno a ogni viso rilevato

![Screenshot di Android di una foto con visi incorniciati da un rettangolo rosso](../Images/android_getstarted2.1.PNG)

Il codice di esempio completo è disponibile nell'archivio [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) su GitHub.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave. Quindi, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per la stringa dell'endpoint di servizio, denominate rispettivamente `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) con livello API 22 o successivo (richiesto dalla libreria client del servizio Viso).

## <a name="create-the-android-studio-project"></a>Creare il progetto Android Studio

Seguire questa procedura per creare un nuovo progetto di applicazione Android.

1. In Android Studio selezionare **Start a new Android Studio project** (Avvia un nuovo progetto Android Studio).
1. Nella schermata **Create Android Project** (Crea progetto Android) modificare i campi predefiniti, se necessario, quindi fare clic su **Next** (Avanti).
1. Nella schermata **Target Android Devices** (Dispositivi Android di destinazione) usare il selettore a discesa per scegliere **API 22** o successivo, quindi fare clic su **Next** (Avanti).
1. Selezionare **Empty Activity** (Attività vuota) e quindi fare clic su **Next** (Avanti).
1. Deselezionare l'opzione **Backwards Compatibility** (Compatibilità con le versioni precedenti) e quindi fare clic su **Finish** (Fine).

## <a name="add-the-initial-code"></a>Aggiungere il codice iniziale

### <a name="create-the-ui"></a>Creare l'interfaccia utente

Aprire *activity_main.xml*. Nell'editor di layout, selezionare la scheda **Text** (Testo) e quindi sostituire i contenuti con il codice seguente.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Creare la classe principale

Aprire *MainActivity.java* e sostituire le istruzioni `import` esistenti con il codice seguente.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Quindi, sostituire il contenuto della classe **MainActivity** con il codice riportato di seguito. Il codice crea un gestore dell'evento sul **pulsante** che avvia una nuova attività per consentire all'utente di selezionare un'immagine. Mostra l'immagine in **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Provare l'app

Commentare la chiamata a **detectAndFrame** nel metodo **onActivityResult**. Quindi, premere **Avvia** dal menu per testare l'app. Quando si apre l'app, in un emulatore o un dispositivo connesso, fare clic sui **Sfoglia** nella parte inferiore. Verrà visualizzata la finestra di dialogo di selezione dei file del dispositivo. Scegliere un'immagine e verificare che venga visualizzata nella finestra. Quindi, chiudere l'app e andare al passaggio successivo.

![Schermata di Android di una foto con visi](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Aggiungere l'SDK Viso

### <a name="add-the-gradle-dependency"></a>Aggiungere la dipendenza Gradle

Nel riquadro **Project** (Progetto) usare il selettore a discesa per selezionare **Android**. Espandere **Gradle Scripts** (Script Gradle) e quindi aprire *build.gradle (Module: app)* (build.gradle - Modulo: app). Aggiungere una dipendenza per la libreria client del servizio Viso, `com.microsoft.projectoxford:face:1.4.3`, come mostrato nello screenshot più avanti, quindi fare clic su **Sync Now** (Sincronizza adesso).

![Screenshot di Android Studio per il file build.gradle dell'app](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Aggiungere il codice di progetto relativo al viso

Tornare a **MainActivity.java** e aggiungere le istruzioni `import` seguenti:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Quindi, inserire il codice seguente nella classe **MainActivity**, sopra il metodo **onCreate**:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

Nel riquadro **Project** (Progetto) espandere **app**, quindi **manifests** (manifesti) e infine aprire *AndroidManifest.xml*. Inserire l'elemento seguente come figlio diretto dell'elemento `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Caricare l'immagine e rilevare i visi

L'app rileverà i visi chiamando il metodo **faceClient.Face.DetectWithStreamAsync**, che esegue il wrapping dell'API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e restituisce un elenco di istanze di **Viso**.

Ogni elemento **Viso** restituito è racchiuso in un rettangolo che ne indica la posizione, combinata con una serie di attributi del viso facoltativi. In questo esempio sono necessari solo i rettangoli posizionati intorno ai visi.

Inserire i due metodi seguenti nella classe **MainActivity**. Si noti che al termine del rilevamento volto, l'app chiama il metodo **drawFaceRectanglesOnBitmap** per modificare **ImageView**. Questo metodo verrà definito successivamente.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Disegnare rettangoli intorno ai visi

Inserire il metodo dell'helper seguente nella classe **MainActivity**. Questo metodo traccia un rettangolo intorno a ogni viso rilevato usando le coordinate del rettangolo di ogni istanza **Viso**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Infine, rimuovere il commento dalla chiamata al metodo **detectAndFrame** in **onActivityResult**.

## <a name="run-the-app"></a>Esecuzione dell'app

Eseguire l'applicazione e quindi cercare un'immagine con un viso. Attendere qualche secondo per consentire al servizio Viso di rispondere. Verrà visualizzato un rettangolo rosso intorno a ciascun viso nell'immagine.

![Screenshot Android di volti con rettangoli rossi disegnati attorno a essi](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso il processo di base per l'uso di Java SDK dell'API Viso ed è stata creata un'applicazione per rilevare e incorniciare i visi in un'immagine. In seguito, si apprenderanno altre informazioni sul rilevamento volto.

> [!div class="nextstepaction"]
> [Come rilevare visi nelle immagini](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
