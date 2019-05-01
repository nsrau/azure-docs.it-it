---
title: Introduzione a Speech Devices SDK - Servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Prerequisiti e istruzioni per iniziare con Speech Devices SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: dec40f7ec21a4a059c6e4a7c75f6378f9757a479
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916837"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Introduzione a Speech Devices SDK

Questo articolo descrive come configurare il PC di sviluppo e il kit di sviluppo per dispositivi Voce per sviluppare dispositivi abilitati per il riconoscimento vocale con Speech Devices SDK. Viene anche spiegato come compilare e distribuire un'applicazione di esempio nel dispositivo.

Il codice sorgente dell'applicazione di esempio è incluso in Speech Devices SDK ed è anche [disponibile in GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare Speech Devices SDK per sviluppare, raccogliere le informazioni e ottenere il software necessario:

* Ottenere un [kit di sviluppo di ROOBO](https://ddk.roobo.com/). Sono disponibili kit con configurazioni array di microfoni lineari o circolari. Scegliere la configurazione adatta alle proprie esigenze.

    |Configurazione kit di sviluppo|Posizione del parlante|
    |-----------------------------|------------|
    |Circolare|In qualsiasi posizione rispetto al dispositivo|
    |Lineari|Davanti al dispositivo|

* Ottenere la versione più recente di Speech Devices SDK, che include un'app Android di esempio, dal [sito di download di Speech Devices SDK](https://shares.datatransfer.microsoft.com/). Decomprimere il file ZIP in una cartella locale, ad esempio C:\SDSDK.

* Installare [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) nel PC.

* Ottenere un [chiave di sottoscrizione di servizi di riconoscimento vocale](get-started.md). Dal dashboard di Azure è possibile ottenere una versione di valutazione gratuita di 30 giorni oppure una chiave.

* Se si desidera utilizzare riconoscimento delle intenzioni tramite sintesi vocale servizi, sottoscrivere il [servizio Language Understanding Intelligent Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) e [ottenere una chiave di sottoscrizione](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    È possibile [creare un modello LUIS semplice](https://docs.microsoft.com/azure/cognitive-services/luis/) oppure usare il modello LUIS di esempio, ovvero LUIS-example.json. Il modello LUIS di esempio è disponibile nel [sito di download di Speech Devices SDK](https://shares.datatransfer.microsoft.com/). Per caricare il file JSON del modello nel [portale LUIS](https://www.luis.ai/home), selezionare **Import new app** (Importa nuova app) e quindi selezionare il file JSON.

## <a name="set-up-the-development-kit"></a>Configurare il kit di sviluppo

1. Il kit di sviluppo ha due connettori Micro-USB. Il connettore di sinistra alimenta il kit di sviluppo ed è contrassegnato con Power nell'immagine seguente. Il connettore di destra ha la funzione di controllare il kit ed è contrassegnato con Debug nell'immagine.

    ![Connessione del kit di sviluppo](media/speech-devices-sdk/qsg-1.png)

1. Alimentare il kit di sviluppo usando un cavo Micro-USB per collegare la porta di alimentazione a un PC o a una presa di alimentazione. Sotto il ripiano superiore si illuminerà la spia verde di accensione.

1. Per controllare il kit di sviluppo, collegare la porta di debug a un computer tramite un secondo cavo Micro-USB. È fondamentale usare un cavo di alta qualità per garantire comunicazioni affidabili.

1. Orientare il kit di sviluppo per la configurazione circolare o lineare.

    |Configurazione kit di sviluppo|Orientamento|
    |-----------------------------|------------|
    |Circolare|Diritto, con i microfoni rivolti verso l'alto|
    |Lineari|Sul lato, con i microfoni rivolti verso l'utente (vedere l'immagine seguente)|

    ![Orientamento del kit di sviluppo lineare](media/speech-devices-sdk/qsg-2.png)

1. Installare i certificati e impostare le autorizzazioni del dispositivo audio. Digitare i comandi seguenti in una finestra del prompt dei comandi:

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Questi comandi usano Android Debug Bridge, `adb.exe`, che fa parte dell'installazione di Android Studio. Questo strumento si trova in C:\Utenti\[nome utente]\AppData\Local\Android\Sdk\platform-tools. Per richiamare più facilmente `adb`, è possibile aggiungere questa directory al percorso. In caso contrario, è necessario specificare il percorso completo dell'installazione di adb.exe in ogni comando che richiama `adb`.
    >
    > Se viene visualizzato un errore `no devices/emulators found`, verificare che il cavo USB sia collegato e che sia un cavo di alta qualità. È possibile usare `adb devices` per verificare che il computer sia in grado di comunicare con il kit di sviluppo poiché restituirà un elenco di dispositivi.

    > [!TIP]
    > Disattivare l'audio del microfono e dell'altoparlante del PC per essere sicuri di usare i microfoni del kit di sviluppo. In questo modo si evita che il dispositivo venga attivato accidentalmente da audio proveniente dal PC.

1.  Avviare Vysor nel computer.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Il dispositivo dovrebbe essere incluso nell'elenco **Choose a device** (Scegli un dispositivo). Selezionare clic sul pulsante **View** (Visualizza) accanto al dispositivo.

1.  Connettersi alla rete wireless facendo clic sull'icona della cartella e quindi su **Impostazioni** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Se la società prevede criteri per la connessione dei dispositivi al sistema Wi-Fi, è necessario ottenere l'indirizzo MAC e contattare il reparto IT per sapere come connettersi al sistema Wi-Fi aziendale.
    >
    > Per trovare l'indirizzo MAC del kit di sviluppo, selezionare l'icona della cartella file sul desktop del kit di sviluppo.
    >
    >  ![Cartella di file Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Selezionare **Impostazioni**. Cercare "indirizzo mac" e quindi selezionare **Mac address** > **Advanced WLAN** (Indirizzo MAC > WLAN avanzata). Prendere nota dell'indirizzo MAC che viene visualizzato nella parte inferiore della finestra di dialogo.
    >
    > ![Indirizzo MAC Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Alcune società possono inoltre prevedere un limite al tempo in cui un dispositivo può rimanere connesso al sistema Wi-Fi aziendale. Può quindi essere necessario prolungare la registrazione del kit di sviluppo al sistema Wi-Fi dopo un certo numero di giorni.
    >
    > Per collegare un altoparlante al kit di sviluppo, è possibile connetterlo all'uscita audio. È consigliabile scegliere un altoparlante da 3,5 mm di buona qualità.
    >
    > ![Audio Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>Eseguire un'applicazione di esempio

Per eseguire i test di ROOBO e convalidare la configurazione del kit di sviluppo, compilare e installare l'applicazione di esempio:

1. Avviare Android Studio.

1. Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente).

   ![Android Studio: apertura di un progetto esistente](media/speech-devices-sdk/qsg-5.png)

1. Passare a C:\SDSDK\Android-Sample-Release\example. Fare clic su **OK** per aprire il progetto di esempio.

1. Aggiungere la chiave di sottoscrizione del Servizio di riconoscimento vocale al codice sorgente. Se si vuole provare la funzionalità Riconoscimento finalità, aggiungere anche la chiave di sottoscrizione e l'ID applicazione del [servizio Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

   Le chiavi e le informazioni relative all'applicazione devono essere inserite nelle righe seguenti del file di origine MainActivity.java:

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. La parola di attivazione (parola chiave) predefinita è "Computer". È anche possibile provare una delle altre parole di attivazione disponibili, come "Machine" o "Assistant". I file di risorse per queste parole di attivazione alternative sono disponibili nella cartella keyword di Speech Devices SDK. La cartella C:\SDSDK\Android-Sample-Release\keyword\Computer contiene ad esempio i file usati per la parola di attivazione "Computer".

    È anche possibile [creare una parola di attivazione personalizzata](speech-devices-sdk-create-kws.md).

    Per usare un nuovo termine di riattivazione, aggiornare le seguenti due righe di mainactivity. Java e copiare il pacchetto di word riattivazione all'app. Per utilizzare la funzionalità di riattivazione, ad esempio word 'Machine' dal kws pacchetti di riattivazione word-machine.zip:

   * Copiare il pacchetto di word di riattivazione nella cartella "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Aggiornare il mainactivity. Java con la parola chiave e il nome del pacchetto: 
    
     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Aggiornare le righe seguenti, che contengono le impostazioni per la geometria dell'array di microfoni:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```
   La tabella seguente descrive i valori disponibili:

   |Variabile|Significato|Valori disponibili|
   |--------|-------|----------------|
   |`DeviceGeometry`|Configurazione microfoni fisica|Per un kit di sviluppo circolare: `Circular6+1` |
   |||Per un kit di sviluppo lineare: `Linear4`|
   |`SelectedGeometry`|Configurazione microfoni software|Per un kit di sviluppo circolare che usa tutti i microfoni: `Circular6+1`|
   |||Per un kit di sviluppo circolare che usa quattro microfoni: `Circular3+1`|
   |||Per un kit di sviluppo lineare che usa tutti i microfoni: `Linear4`|
   |||Per un kit di sviluppo lineare che usa due microfoni: `Linear2`|


1. Per compilare l'applicazione, nel menu **Run** (Esegui) selezionare **Run 'app'** (Esegui 'app'). Viene visualizzata la finestra di dialogo **Select Deployment Target** (Seleziona destinazione di distribuzione).

1. Selezionare il dispositivo e quindi fare clic su **OK** per distribuire l'applicazione nel dispositivo.

    ![Finestra di dialogo Select Deployment Target (Seleziona destinazione di distribuzione)](media/speech-devices-sdk/qsg-7.png)

1. L'applicazione di esempio di Speech Devices SDK viene avviata e visualizza le opzioni seguenti:

   ![Applicazione di esempio di Speech Devices SDK e opzioni](media/speech-devices-sdk/qsg-8.png)

1. A questo punto, non resta che sperimentare.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="certificate-failures"></a>Errori di certificato

Se si verificano errori del certificato quando si usano i servizi di riconoscimento vocale, assicurarsi che il dispositivo abbia corretti di data e ora:

1. Passare a **Impostazioni**. In **System** (Sistema) selezionare **Date & time** (Data e ora).

    ![In Settings (Impostazioni) selezionare Date & time (Data e ora)](media/speech-devices-sdk/qsg-12.png)

1. Mantenere selezionata l'opzione **Automatic date & time** (Data e ora automatiche). In **Select time zone** (Seleziona il fuso orario) selezionare il fuso orario corrente.

    ![Opzioni per la selezione della data e del fuso orario](media/speech-devices-sdk/qsg-13.png)

    Dopo aver verificato che l'ora del kit di sviluppo corrisponde a quella del PC, il kit di sviluppo è connesso a Internet.

    Per altre informazioni sullo sviluppo, vedere la [guida allo sviluppo di ROOBO](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Audio

ROOBO include uno strumento che acquisisce tutto l'audio e lo inserisce nella memoria flash. Tale strumento può essere utile per la risoluzione dei problemi relativi all' audio. Una versione dello strumento è disponibile con ogni configurazione del kit di sviluppo. Nel [sito di ROOBO](https://ddk.roobo.com/) selezionare il dispositivo e quindi fare clic sul collegamento **ROOBO Tools** (Strumenti ROOBO) nella parte inferiore della pagina.
