---
title: Recuperare un messaggio di Twitter con Funzioni di Azure | Microsoft Docs
description: Usare il sensore di movimento per rilevare lo scuotimento e usare Funzioni di Azure per trovare un tweet casuale con un hashtag specificato.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: d9d03d35aa5d78d83e0f195c804cfe09fece3c07
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Usare lo scuotimento per recuperare un messaggio di Twitter con Funzioni di Azure

In questo progetto si apprenderà a usare il sensore di movimento per attivare un evento usando Funzioni di Azure. L'applicazione recupera un tweet casuale con un hashtag configurato nello sketch Arduino. Il tweet viene visualizzato sullo schermo del dispositivo DevKit.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi.
* Preparare l'ambiente di sviluppo.

Una sottoscrizione di Azure attiva. Se non è disponibile, è possibile registrarsi tramite uno di questi metodi:

* Attivare una [versione di prova gratuita di 30 giorni dell'account di Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html)
* Richiedere il [credito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio

## <a name="open-the-project-folder"></a>Aprire la cartella del progetto

### <a name="start-vs-code"></a>Avviare Visual Studio Code

- Assicurarsi che il DevKit **non** sia connesso al computer.
- Avviare Visual Studio Code.
- Connettere il dispositivo DevKit al computer.

Visual Studio Code individua il DevKit automaticamente e visualizza una pagina di introduzione:

![mini-solution-vscode](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_start.png)

> [!NOTE]
> Quando si avvia Visual Studio Code può essere visualizzato un messaggio di errore che indica che l'IDE Arduino o il pacchetto della scheda correlato non è stato trovato. Se si verifica questo errore, chiudere Visual Studio Code e avviare nuovamente l'IDE Arduino. Visual Studio Code dovrebbe a questo punto individuare correttamente il percorso dell'IDE Arduino.

### <a name="open-arduino-examples-folder"></a>Aprire la cartella degli esempi di Arduino

Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > AzureIoT** (Esempi per MXCHIP AZ3166 > AzureIoT) e selezionare **ShakeShake**. Si apre una nuova finestra di Visual Studio Code con una cartella di progetto all'interno.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione eseguire l'attività desiderata premendo `Ctrl+P` (macOS: `Cmd+P`) e immettendo `task cloud-provision`.

Nel terminale di Visual Studio Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari:

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Se la pagina resta in stato di caricamento quando si prova ad accedere ad Azure, fare riferimento a questo [passaggio delle domande frequenti]({{"/docs/faq/#page-hangs-when-log-in-azure" | 
 
## <a name="modify-the-hashtag"></a>Modificare l'hashtag

Aprire `ShakeShake.ino` e cercare la riga di codice seguente:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Sostituire la stringa `iot` tra le parentesi graffe con il proprio hashtag preferito. Più avanti, il dispositivo DevKit recupererà un tweet casuale che include l'hashtag specificato in questo passaggio.

## <a name="deploy-azure-functions"></a>Distribuire Funzioni di Azure

Usare `Ctrl+P` (macOS: `Cmd+P`) per eseguire `task cloud-deploy` e avviare la distribuzione del codice di Funzioni di Azure:

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> In alcuni casi la funzione di Azure potrebbe non funzionare correttamente. Per risolvere il problema, fare riferimento a questo [passaggio delle domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

### <a name="windows"></a>Windows

1. Usare `Ctrl+P` per eseguire `task device-upload`.
2. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, procedere come segue:

   * Tenere premuto il pulsante A
   * Premere e rilasciare il pulsante di reimpostazione.

3. La schermata visualizza l'ID DevKit e la voce 'Configuration' (Configurazione).
4. Questo imposta la stringa di connessione recuperata dal passaggio `task cloud-provision`.
5. Visual Studio Code inizia a verificare e caricare lo sketch Arduino nel DevKit: ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> È possibile che venga visualizzato il messaggio "Errore: AZ3166: Pacchetto sconosciuto". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per risolvere il problema, fare riferimento a questo [passaggio delle domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Impostare il DevKit in modalità di configurazione: tenere premuto il pulsante A e quindi premere e rilasciare il pulsante di reimpostazione. Verrà visualizzata la schermata 'Configuration'.
2. Usare `Cmd+P` per eseguire `task device-upload` e impostare la stringa di connessione recuperata dal passaggio `task cloud-provision`.
3. Visual Studio Code inizia a verificare e caricare lo sketch Arduino nel DevKit: ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> È possibile che venga visualizzato il messaggio "Errore: AZ3166: Pacchetto sconosciuto". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per risolvere il problema, fare riferimento a questo [passaggio delle domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Verificare il progetto

Dopo l'inizializzazione dell'app, premere e rilasciare il pulsante A, quindi scuotere delicatamente la scheda DevKit. Questa azione recupera un tweet casuale, che contiene l'hashtag specificato in precedenza. Entro pochi secondi, sullo schermo del DevKit compare un tweet:

### <a name="arduino-application-initializing"></a>Inizializzazione dell'applicazione Arduino...
![Inizializzazione dell'applicazione Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Premere A per scuotere...
![Premere A per scuotere](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Pronto allo scuotimento...
![Pronto allo scuotimento](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Elaborazione in corso...
![Elaborazione in corso](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Premere B per leggere...
![Premere B per leggere](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Visualizzazione di un tweet casuale...
![Visualizzazione di un tweet casuale](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Premere di nuovo il pulsante A, quindi scuotere per visualizzare un nuovo tweet.
- Premere il pulsante B per scorrere nella parte restante del tweet.

## <a name="how-it-works"></a>Funzionamento

![diagramma](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Lo sketch Arduino invia un evento all'hub IoT di Azure. Questo evento attiva l'app Funzioni di Azure. L'app Funzioni di Azure contiene la logica per la connessione all'API di Twitter e il recupero di un tweet. Successivamente, esegue il wrapping del testo visualizzato in un messaggio da cloud a dispositivo (C2D) e lo invia al dispositivo.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Facoltativo: usare i propri token di connessione a Twitter

A scopo di test, questo progetto di esempio usa un token di connessione a Twitter configurato in precedenza. Tuttavia, per ogni account Twitter è previsto un [limite di frequenza](https://dev.twitter.com/rest/reference/get/search/tweets). Se si vuole provare a usare un proprio token, seguire questa procedura:

1. Passare al [portale per sviluppatori di Twitter](https://dev.twitter.com/) per registrare una nuova app Twitter.

2. [Recuperare i valori chiave utente e segreto consumer](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) dell'app.

3. Servirsi di [un'utilità](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) per generare un token di connessione a Twitter da queste due chiavi.

4. Nel [portale di Azure](https://portal.azure.com/){:target="_blank"} passare a **Gruppo di risorse** e trovare la funzione di Azure (Tipo: Servizio app) per questo progetto. Il nome contiene sempre la stringa "shake".
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Aggiornare il codice per `run.csx`in **Funzioni > shakeshake-cs** con il proprio token:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Salvare il file e fare clic su **Esegui**.


## <a name="problems-and-feedback"></a>Problemi e commenti

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>Ogni passaggio è stato eseguito correttamente, ma sullo schermo non compaiono tweet

Questa condizione si verifica in genere la prima volta che si distribuisce ed esegue l'esempio, perché l'avvio a freddo dell'app per le funzioni richiede da due secondi a un minuto. Può anche capitare che, durante l'esecuzione del codice, si verifichi un problema che causa il riavvio dell'app. Quando si verifica questa condizione, l'app per il dispositivo non riceve un timeout per il recupero del tweet. In questo caso si può provare a risolvere il problema applicando uno dei metodi seguenti o entrambi:

1. Fare clic sul pulsante di reimpostazione sul DevKit per eseguire di nuovo di sviluppo per eseguire di nuovo l'app per il dispositivo.

2. Nel [portale di Azure](https://portal.azure.com/) trovare l'app Funzioni di Azure creata e riavviarla: ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Commenti e suggerimenti

Se si verificano altri problemi, fare riferimento alle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare Microsoft tramite i canali seguenti:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come connettere un dispositivo DevKit ad Azure IoT Suite e recuperare un tweet, ecco i passaggi successivi consigliati:

* [Panoramica di Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)