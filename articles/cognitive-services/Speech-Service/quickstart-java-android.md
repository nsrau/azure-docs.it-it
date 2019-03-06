---
title: 'Avvio rapido: Riconoscere i contenuti vocali, Java (Android) - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Java in Android con Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 2ee5028e0766ec52c407bf0ef0e18885c11734d8
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961052"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Avvio rapido: Riconoscere i contenuti vocali in Java su Android con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come sviluppare un'applicazione Java per Android usando Speech SDK di Servizi cognitivi per la trascrizione del riconoscimento vocale.
L'applicazione è basata sulla versione 1.3.1 del pacchetto Maven di Speech SDK di Servizi cognitivi Microsoft e su Android Studio 3.1.
Speech SDK è attualmente compatibile con i dispositivi Android con processori ARM a 32/64 bit e Intel x86/x64 compatibili.

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

È necessaria una chiave di sottoscrizione del Servizio di riconoscimento vocale per completare la guida introduttiva. È possibile ottenerne una gratuitamente. Vedere [Prova il servizio di riconoscimento vocale gratuitamente](get-started.md) per i dettagli.

## <a name="create-and-configure-a-project"></a>Creare e configurare un progetto

1. Avviare Android Studio e selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio) nella schermata iniziale.

    ![Screenshot della finestra iniziale di Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Viene visualizzata la procedura guidata **Crea nuovo progetto**. Nella schermata **Create Android Project** (Crea progetto Android) immettere **Quickstart** (Avvio rapido) come **nome dell'applicazione**, **samples.speech.cognitiveservices.microsoft.com** come **dominio aziendale** e scegliere una directory di progetto. Lasciare le caselle di controllo C++ e Kotlin deselezionate, quindi selezionare **Avanti**.

   ![Screenshot della procedura guidata Crea nuovo progetto](media/sdk/qs-java-android-02-create-android-project.png)

1. Nella schermata **Target Android Devices** (Dispositivi Android di destinazione), selezionare solo **Telefoni e Tablet**. Nell'elenco a discesa sottostante scegliere **API 23: Android 6.0 (Marshmallow)** e selezionare **Avanti**.

   ![Screenshot della procedura guidata Crea nuovo progetto](media/sdk/qs-java-android-03-target-android-devices.png)

1. Nella schermata **Add an Activity to Mobile** (Aggiungere un'attività ai dispositivi mobili) selezionare **Empty Activity** (Attività vuota) e fare clic su **Next** (Avanti).

   ![Screenshot della procedura guidata Crea nuovo progetto](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Nella schermata **Configure Activity** (Configura attività) usare **MainActivity** come nome dell'attività e **activity\_main** come nome del layout. Selezionare entrambe le caselle di controllo e selezionare **Fine**.

   ![Screenshot della procedura guidata Crea nuovo progetto](media/sdk/qs-java-android-05-configure-activity.png)

Android Studio richiede qualche momento per preparare il nuovo progetto Android. Successivamente, configurare il progetto per conoscere Speech SDK e usare Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.3.1`.

Speech SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste.
È ospitato in un repository Maven in https://csspeechstorage.blob.core.windows.net/maven/.

Di seguito viene descritto come configurare il progetto per usare Speech SDK. Aprire la finestra Struttura del progetto, scegliendo **File** > **Struttura del progetto** dalla barra del menu Android Studio. Nella finestra Struttura del progetto, apportare le modifiche seguenti:

1. Nell'elenco sul lato sinistro della finestra, selezionare **Progetto**. Modificare le impostazioni del **Repository predefinito della libreria** aggiungendo una virgola e l'URL del repository Maven racchiuso tra virgolette singole. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![Screenshot della finestra della struttura del progetto](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Nella stessa schermata, sul lato sinistro, selezionare **app**. Quindi selezionare la scheda **Dipendenze** nella parte superiore della finestra. Selezionare il segno verde più (+) e scegliere **Dipendenza della libreria** dal menu a discesa.

   ![Screenshot della finestra della struttura del progetto](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Nella finestra visualizzata immettere il nome e la versione di Speech SDK per Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1`. Selezionare **OK**.
   A questo punto Speech SDK viene aggiunto all'elenco delle dipendenze come illustrato di seguito:

   ![Screenshot della finestra della struttura del progetto](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Fare clic sulla scheda **Proprietà**. Selezionare **1.8** sia per **Source Compability** (Compatibilità di origine) che per **Target Compatibility** (Compatibilità di destinazione).

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Selezionare **OK** per chiudere la finestra Struttura del progetto e applicare le modifiche apportate al progetto.

## <a name="create-user-interface"></a>Creare l'interfaccia utente

Verrà creata un'interfaccia utente di base per l'applicazione. Modificare il layout per l'attività principale, `activity_main.xml`. Inizialmente, il layout include una barra del titolo con il nome dell'applicazione e un TextView contenente il testo "Hello World!".

* Fare clic sull'elemento TextView. Modificare l'attributo ID nell'angolo superiore destro in `hello`.

* Dalla tavolozza in alto a sinistra nella finestra `activity_main.xml`, trascinare un pulsante nello spazio vuoto sopra il testo.

* Negli attributi del pulsante a destra, nel valore dell'attributo `onClick` immettere `onSpeechButtonClicked`. Verrà scritto un metodo con questo nome per gestire l'evento del pulsante.  Modificare l'attributo ID nell'angolo superiore destro in `button`.

* Usare l'icona a forma di bacchetta magica nella parte superiore della finestra di progettazione per dedurre i vincoli di layout.

  ![Screenshot dell'icona Bacchetta magica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Il testo e la versione grafica dell'interfaccia utente dovrebbero essere simili all'immagine seguente:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

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

1. Per avviare l'applicazione, premere MAIUSC + F10 oppure scegliere **Esegui** > **Esegui 'app'**.

1. Nella finestra di destinazione della distribuzione che viene visualizzata, selezionare il dispositivo Android.

   ![Screenshot della finestra di schermata Seleziona destinazione di distribuzione](media/sdk/qs-java-android-12-deploy.png)

Fare clic sul pulsante dell'applicazione per iniziare una sezione dedicata al riconoscimento vocale. I 15 secondi successivi di riconoscimento vocale in lingua inglese verranno inviati al servizio di riconoscimento vocale e trascritti. Il risultato viene visualizzato nell'applicazione Android e nella finestra di logcat in Android Studio.

![Screenshot dell'applicazione Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
