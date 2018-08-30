---
title: Introduzione a Speech Devices SDK
description: Prerequisiti e istruzioni per iniziare con Speech Devices SDK.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 463a015b7c01dafc5b30de56b95fa0510ffb98e4
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42424370"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Introduzione a Speech Devices SDK

Questo articolo descrive come configurare il PC in uso e Speech Devices SDK per abilitare per la voce i dispositivi con questo kit di sviluppo. L'articolo illustra anche come compilare e distribuire a un dispositivo un'applicazione di esempio. 

Il codice sorgente dell'applicazione di esempio, in dotazione con Speech Devices SDK, è anche [disponibile in GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare lo sviluppo con Speech Devices SDK,procurarsi le informazioni e il software necessari.

* Ottenere un kit di sviluppo [da Roobo](http://ddk.roobo.com/). Sono disponibili kit dotati di matrici di microfoni con configurazione lineare o circolare. Scegliere la configurazione adatta alle proprie esigenze.

    |Configurazione kit di sviluppo|Posizione del parlante|
    |-----------------------------|------------|
    |Circolare|In qualsiasi posizione rispetto al dispositivo|
    |Lineari|Davanti al dispositivo|

* Ottenere la versione più recente di Speech Devices SDK, comprendente un'app Android di esempio, dal [sito di download](https://shares.datatransfer.microsoft.com/) di Speech Devices SDK. Decomprimere il file ZIP in una cartella locale, ad esempio `C:\SDSDK`.

* Installare [Android Studio](https://developer.android.com/studio/) e [Vysor](http://vysor.io/download/) nel PC.

* Ottenere una [chiave di sottoscrizione](get-started.md) per il Servizio di riconoscimento vocale. Dal dashboard di Azure è possibile ottenere una versione di valutazione gratuita di 30 giorni o una chiave.

* Se si vuole usare la funzionalità Riconoscimento finalità del Servizio di riconoscimento vocale, abbonarsi al [servizio Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) e [ottenere una chiave di sottoscrizione](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    È possibile [creare un modello LUIS semplice](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) o usare il modello LUIS di esempio, `LUIS-example.json`, disponibile dal [sito di download](https://shares.datatransfer.microsoft.com/) di Speech Devices SDK. Caricare il file JSON del modello nel [portale LUIS](https://www.luis.ai/home) facendo clic su **Import new app** (Importa nuova app) e scegliendo il file JSON.

## <a name="set-up-the-development-kit"></a>Configurare il kit di sviluppo

1. Accendere il kit di sviluppo tramite un cavo USB mini connesso a un PC o a un alimentatore. Sotto il ripiano superiore dovrebbe accendersi la spia verde di accensione.

1. Collegare il kit di sviluppo a un computer tramite un cavo USB mini.

    ![Connessione del kit di sviluppo](media/speech-devices-sdk/qsg-1.png)

1. Orientare il kit di sviluppo in modo appropriato.

    |Configurazione kit di sviluppo|Orientamento|
    |-----------------------------|------------|
    |Circolare|Diritto, con i microfoni rivolti verso l'alto|
    |Lineari|Sul lato, con i microfoni rivolti verso l'utente (vedere la figura seguente)|

    ![Orientamento del kit di sviluppo lineare](media/speech-devices-sdk/qsg-2.png)

1. Installare i certificati e il file della tabella delle parole di attivazione del dispositivo (parole chiave) e impostare le autorizzazioni del dispositivo audio. Digitare i comandi seguenti in una finestra di comando.

    > [!NOTE]
    > Questi comandi usano Android Debug Bridge, `adb.exe`, che fa parte dell'installazione di Android Studio. Questo strumento è disponibile in `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Per richiamare più facilmente `adb`, è possibile aggiungere questa directory al percorso. In caso contrario, è necessario specificare il percorso completo dell'installazione di `adb.exe` in ogni comando che richiama `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Disattivare l'audio del microfono e dell'altoparlante del PC. Ciò consente di assicurarsi che vengano usati i microfoni del kit di sviluppo e che il dispositivo non venga attivato accidentalmente da audio proveniente dal PC.
    
1.  Avviare Vysor nel computer.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Il dispositivo dovrebbe essere presente nell'elenco "Choose a device" (Scegli un dispositivo). Fare clic sul pulsante **View** (Visualizza) accanto al dispositivo. 
 
1.  Connettersi alla rete wireless facendo clic su **Impostazioni** e quindi su **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
 > [!NOTE]
 > Se la società dispone di criteri per quanto riguarda i dispositivi connessi al sistema Wi-Fi, è necessario ottenere l'indirizzo Mac e contattare il reparto IT per sapere come connettersi al sistema Wi-Fi. Per trovare l'indirizzo Mac del kit di sviluppo, fare clic sull'icona della cartella file sul desktop del kit di sviluppo, quindi, nelle **Impostazioni**, cercare "Indirizzo Mac", fare clic su **Indirizzo Mac** per aprire **Advanced WLAN** (Impostazioni WLAN avanzate) e prendere nota dell'indirizzo Mac disponibile nella parte inferiore. Inoltre, alcune aziende potrebbero avere un limite di tempo per cui un dispositivo può restare connesso ai sistemi Wi-Fi aziendali. Potrebbe essere necessario estendere la registrazione del kit di sviluppo con il sistema Wi-Fi, dopo un certo numero di giorni.  
 
 
   ![Cartella di file Vysor](media/speech-devices-sdk/qsg-10.png)
   
   ![Indirizzo Mac Vysor](media/speech-devices-sdk/qsg-11.png)
   
   
 > Per collegare uno speaker al kit di sviluppo, è possibile connetterlo all'uscita Audio. È anche consigliabile scegliere uno speaker da 3,5 mm di buona qualità.
 
   ![Audio Vysor](media/speech-devices-sdk/qsg-14.png)
 
## <a name="run-a-sample-application"></a>Eseguire un'applicazione di esempio

Per eseguire i test di Roobo e convalidare la configurazione del kit di sviluppo, compilare e installare l'applicazione di esempio.

1.  Avviare Android Studio.

1.  Scegliere di aprire un progetto di Android Studio esistente.

    ![Android studio: apertura di un progetto esistente](media/speech-devices-sdk/qsg-5.png)
 
1.  Passare a `C:\SDSDK\Android-Sample-Release\example` e quindi fare clic su **OK** per aprire il progetto di esempio.
 
1.  Aggiungere la chiave di sottoscrizione del Servizio di riconoscimento vocale al codice sorgente. Se si vuole provare la funzionalità Riconoscimento finalità, aggiungere anche la chiave di sottoscrizione e l'ID applicazione del [servizio Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

    Le chiavi e le informazioni relative all'applicazione devono essere inserite nelle righe seguenti del file di origine `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. La parola di attivazione (parola chiave) predefinita è "Computer".  Facoltativamente, è possibile provare una delle altre parole di attivazione disponibili, "Machine" e "Assistant". I file di risorse per queste parole alternative sono disponibili nella cartella "keyword" di Speech Devices SDK. `C:\SDSDK\Android-Sample-Release\keyword\Computer`, ad esempio, contiene i file usati per "Computer".

    È anche possibile [creare una parola di attivazione personalizzata](speech-devices-sdk-create-kws.md).

    Per installare la parola di attivazione desiderata:
 
    * Creare una cartella `keyword` nella cartella \data\ del dispositivo eseguendo i comandi seguenti in una finestra di comando.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Copiare i file `kws.table`, `kws_g.fst`, `kws_k.fst` e `words_kw.txt` nella cartella \data\keyword\ del dispositivo. Eseguire i comandi seguenti in una finestra di comando. Se è stato creato un [word di riattivazione personalizzato](speech-devices-sdk-create-kws.md), il file kws.table generato dal Web si troverà nella stessa directory dei file `kws.table`, `kws_g.fst`, `kws_k.fst` e `words_kw.txt`. Usare il push di adb C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword per effettuare il push del file kws.table al kit di sviluppo.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Fare riferimento a questi file nell'applicazione di esempio. Trovare le righe seguenti in `MainActivity.java`. Assicurarsi che la parola chiave specificata sia quella usata e che il percorso punti al file `kws.table` di cui si è effettuato il push al dispositivo.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > All'interno del proprio codice è possibile usare il file `kws.table` per creare un'istanza del modello di parole chiave e avviare il riconoscimento come segue.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Aggiornare le righe seguenti, che contengono le impostazioni di geometria della matrice di microfoni.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Variabile|Significato|Valori disponibili|
    |--------|-------|----------------|
    |`DeviceGeometry`|Configurazione microfoni fisica|`Circular6+1` per kit di sviluppo circolare|
    ||| `Linear4` per kit di sviluppo lineare|
    |`SelectedGeometry`|Configurazione microfoni software|`Circular6+1` per kit di sviluppo circolare se si usano tutti i microfoni|
    |||`Circular3+1` per kit di sviluppo circolare se si usano quattro microfoni|
    |||`Linear4` per kit di sviluppo lineare se si usano tutti i microfoni|
    |||`Linear2` per kit di sviluppo lineare se si usano due microfoni|


1.  Compilare l'applicazione scegliendo **Run 'app'** (Esegui 'app') dal menu Run (Esegui). Verrà visualizzata la finestra di dialogo Select Deployment Target (Seleziona destinazione di distribuzione). Scegliere il dispositivo e fare clic su **OK** per distribuire l'applicazione al dispositivo.

    ![Seleziona destinazione di distribuzione](media/speech-devices-sdk/qsg-7.png)
 
1.  L'applicazione di esempio di Speech Devices SDK verrà avviata e visualizzerà le opzioni illustrate di seguito.

    ![Applicazione di esempio del dispositivo di riconoscimento vocale](media/speech-devices-sdk/qsg-8.png)

1. Effettuare qualche prova.

## <a name="troubleshooting"></a>risoluzione dei problemi

Se quando si usa il Servizio di riconoscimento vocale vengono visualizzati errori relativi al certificato, assicurarsi che la data e l'ora del dispositivo siano corretti. Passare a **Impostazioni**, fare clic su **Data e ora** in Sistema e su **Seleziona il fuso orario** per selezionare il fuso orario corrente. Mantenere **Automatic date & time** (Data e ora automatiche) su ON. Una volta appurato che l'ora del kit di sviluppo corrisponde a quella del PC, sarà possibile sapere che il kit di sviluppo è connesso a Internet. 

 ![Cartella di file Vysor](media/speech-devices-sdk/qsg-12.png)
 
 ![Cartella di file Vysor](media/speech-devices-sdk/qsg-13.png)

Per altre informazioni sullo sviluppo, vedere la [guida allo sviluppo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf) di Roobo.

Roobo offre uno strumento che acquisisce tutto l'audio e lo inserisce nella memoria flash. Tale strumento può essere utile per la risoluzione di problemi audio. Una versione dello strumento è disponibile con ogni configurazione del kit di sviluppo. Scegliere il dispositivo nel [sito Roobo](http://ddk.roobo.com/) e quindi fare clic sul collegamento **ROOBO Tools** (Strumenti Roobo) nella parte inferiore della pagina.
