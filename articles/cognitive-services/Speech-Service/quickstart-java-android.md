---
title: 'Guida introduttiva: Riconoscimento vocale in Java su Android con Speech SDK di Servizi cognitivi'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in Java su Android con Speech SDK di Servizi cognitivi
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: 9f761fed46f0730a64a984111da1bae1229cc93d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127072"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in Java su Android con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione Java per Android usando Speech SDK di Servizi cognitivi per la trascrizione del riconoscimento vocale.
L'applicazione si basa sul pacchetto NuGet Speech SDK Maven di Servizi cognitivi Microsoft versione 0.6.0 e su Android Studio 3.1.

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, visitare la pagina [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC (Windows, Linux, Mac) in grado di eseguire Android Studio.
* La versione 3.1 di [Android Studio](https://developer.android.com/studio/).
* Un dispositivo Android basato su ARM (API 23: Android 6.0 Marshmallow o versione successiva) [abilitato per lo sviluppo](https://developer.android.com/studio/debug/dev-options) con un microfono funzionante.

## <a name="create-an-android-studio-project"></a>Creare un progetto di Android Studio

Avviare Android Studio e selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio).

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

Nella procedura guidata **Create New Project** (Creazione nuovo progetto) che viene visualizzata selezionare le opzioni seguenti:

1. Nella schermata **Create Android Project** (Crea progetto Android) immettere **Quickstart** (Avvio rapido) come **nome dell'applicazione**, **samples.speech.cognitiveservices.microsoft.com** come **dominio aziendale** e scegliere un percorso di progetto. Lasciare deselezionate le caselle di controllo e fare clic su **Next** (Avanti).

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. Nella schermata **Target Android Devices** (Dispositivi Android di destinazione) selezionare solo l'opzione **Phone and Tablet** (Telefono e Tablet), scegliere **API 23: Android 6.0 (Marshmallow)** dall'elenco a discesa e fare clic su **Next** (Avanti).

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. Nella schermata **Add an Activity to Mobile** (Aggiungere un'attività ai dispositivi mobili) selezionare **Empty Activity** (Attività vuota) e fare clic su **Next** (Avanti).

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Nella schermata **Configure Activity** (Configura attività) usare **MainActivity** come nome dell'attività e **activity\_main** come nome del layout. Selezionare entrambe le caselle di controllo e fare clic su **Finish** (Fine).

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Dopo qualche minuto di esecuzione, dovrebbe apparire il progetto Android Studio appena creato.

## <a name="configure-your-project-for-the-speech-sdk"></a>Configurare il progetto per Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `0.6.0`.

Speech SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste per usarlo.
È ospitato in un repository Maven in https://csspeechstorage.blob.core.windows.net/maven/.

Di seguito viene descritto come configurare il progetto per usare Speech SDK.

Aprire la finestra della struttura del progetto in **File** \> **Project Structure** (Struttura del progetto).
Nella finestra visualizzata apportare le modifiche seguenti (fare clic su **OK** solo dopo aver completato tutti i passaggi):

1. Selezionare **Project** (Progetto) e modificare le impostazioni del **Repository predefinito delle librerie** aggiungendo una virgola e l'URL del repository Maven racchiuso tra virgolette singole `'https://csspeechstorage.blob.core.windows.net/maven/'`:

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ancora nella stessa schermata, sul lato sinistro, selezionare il modulo **App** e nella parte superiore la scheda **Dependencies** (Dipendenze). Quindi, fare clic sul segno più di colore verde nell'angolo superiore destro e selezionare **Library dependency** (Dipendenze di libreria).

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Nella finestra visualizzata immettere il nome e la versione di Speech SDK per Android, `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`, quindi fare clic su **OK**.
   A questo punto Speech SDK viene aggiunto all'elenco delle dipendenze come illustrato di seguito:

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. Nel menu in alto scegliere la scheda **Properties** (Proprietà). Selezionare **1.8** sia per **Source Compability** (Compatibilità di origine) che per **Target Compatibility** (Compatibilità di destinazione).

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Infine, fare clic su **OK** per chiudere le finestre **Project Structure** (Struttura del progetto) e applicare tutti gli aggiornamenti.

## <a name="create-a-minimal-ui"></a>Creare un'interfaccia utente minima

Modificare il layout per l'attività principale, `activity_main.xml`.
Per impostazione predefinita dovrebbe apparire una barra del titolo con il nome dell'applicazione e un oggetto TextView con la dicitura "Hello World!".

* Fare clic sull'oggetto TextView. Modificare l'attributo ID nell'angolo superiore destro in `hello`.

* Dalla tavolozza in alto a sinistra nella finestra `activity_main.xml`, trascinare un pulsante nello spazio vuoto sopra il testo.

* Negli attributi del pulsante a destra, nel valore dell'attributo `onClick` immettere `onSpeechButtonClicked`, che sarà il nome del gestore del pulsante.
  Modificare l'attributo ID nell'angolo superiore destro in `button`.

* Usare l'icona a forma di bacchetta magica nella parte superiore della finestra di progettazione per dedurre i vincoli del layout.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

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

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Modificare il file di origine `MainActivity.java` e sostituirne il codice con il seguente (sotto l'istruzione package):

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Il metodo `onCreate` include codice che richiede autorizzazioni Internet e microfono e che inizializza l'associazione di piattaforma nativa. La configurazione delle associazioni di piattaforma nativa è richiesta solo una volta: deve essere effettuata durante l'inizializzazione dell'applicazione.
   
   * Il metodo `onSpeechButtonClicked` è stato in precedenza indicato come gestore dei clic del pulsante. La pressione di un pulsante attiva il riconoscimento vocale effettivo.

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

* Per la compilazione, premere Ctrl + F9 o scegliere **Build** \> **Make Project** (Compila > Crea progetto).

* Connettere il dispositivo Android al PC di sviluppo. Assicurarsi di avere la [modalità di sviluppo e il debug USB abilitati](https://developer.android.com/studio/debug/dev-options).

* Per avviare l'app, premere MAIUSC + F10 o selezionare **Run** \> **Run 'app'** (Esegui>Esegui applicazione).

* Nella finestra di destinazione della distribuzione che viene visualizzata, selezionare il dispositivo Android.

  ![Avviare l'app nel debug](media/sdk/qs-java-android-12-deploy.png)

* L'app dovrebbe avviarsi sul dispositivo.
  Quando si fa clic sul pulsante, il riconoscimento e la visualizzazione nell'interfaccia utente avviene entro 15 secondi (dovrebbe essere possibile visualizzare la risposta nella finestra di logcat in Android Studio):

  ![Interfaccia utente dopo il riconoscimento corretto](media/sdk/qs-java-android-13-gui-on-device.png)

Questo screenshot conclude l'avvio rapido di Android. Il codice di esempio completo del progetto può essere scaricato dal repository degli esempi.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/java-android`.

## <a name="next-steps"></a>Passaggi successivi

* [Scaricare gli esempi](speech-sdk.md#get-the-samples)
