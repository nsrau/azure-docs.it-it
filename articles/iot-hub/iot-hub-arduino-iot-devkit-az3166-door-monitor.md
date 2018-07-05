---
title: Ricevere un messaggio di posta elettronica quando la porta viene aperta usando il servizio SendGrid e Funzioni di Azure | Microsoft Docs
description: Monitorare il sensore magnetico per rilevare quando viene aperta una porta e usare Funzioni di Azure per inviare una notifica tramite posta elettronica.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 25cb3ba53c663a642f0871becbfbcab39d521c67
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437716"
---
# <a name="door-monitor"></a>Monitoraggio della porta          

MXChip IoT DevKit contiene un sensore magnetico predefinito. In questo progetto si rileva la presenza o l'assenza di un campo magnetico forte nelle vicinanze, in questo caso, proveniente da un piccolo magnete permanente.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questo progetto viene illustrato:
- Come usare il sensore magnetico di MXChip IoT DevKit per rilevare lo spostamento di un magnete nelle vicinanze.
- Come usare il servizio SendGrid per inviare una notifica all'indirizzo di posta elettronica.

> [!NOTE]
> Per un uso pratico di questo progetto:
> - Montare un magnete sul bordo di una porta.
> - Montare il DevKit sullo stipite della porta vicino al magnete. L'apertura o la chiusura della porta attiverà il sensore e si riceverà una notifica tramite posta elettronica dell'evento.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](iot-hub-arduino-iot-devkit-az3166-get-started.md) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo

Una sottoscrizione di Azure attiva. Se non è disponibile, è possibile registrarsi tramite uno di questi metodi:

* Attivare una [versione di valutazione gratuita di 30 giorni dell'account Microsoft Azure](https://azure.microsoft.com/free/).
* Richiedere il [credito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Distribuire il servizio SendGrid in Azure

[SendGrid](https://sendgrid.com/) è una piattaforma per il recapito tramite posta elettronica basata sul cloud. Questo servizio verrà usato per inviare notifiche tramite posta elettronica.

> [!NOTE]
> Se è già stato distribuito un servizio SendGrid, è possibile procedere direttamente alla [distribuzione dell'hub IoT in Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Distribuzione di SendGrid

Per effettuare il provisioning dei servizi di Azure, usare il pulsante **Distribuisci in Azure**. Questo pulsante consente una distribuzione semplice e rapida dei progetti open source in Microsoft Azure.

Fare clic sul pulsante **Distribuisci in Azure** sotto. 

[![Distribuzione in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Viene quindi visualizzata la pagina seguente.

> [!NOTE]
> Se non viene visualizzata la pagina seguente, potrebbe essere necessario accedere prima all'account di Azure.

Completare il modulo di iscrizione:

  * **Gruppo di risorse**: creare un gruppo di risorse per ospitare il servizio SendGrid o usarne uno esistente. Vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nome**: nome del servizio SendGrid. Scegliere un nome univoco, diverso da quello di altri servizi già presenti.

  * **Password**: il servizio richiede una password, che non servirà per tutti gli elementi di questo progetto.

  * **Posta elettronica**: il servizio SendGrid invierà la verifica a questo indirizzo di posta elettronica.

  > [!NOTE]
  > Controllare l'opzione **Aggiungi al dashboard** per trovare più facilmente questa applicazione in futuro.
 
![Distribuzione di SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Creazione della chiave API di SendGrid

Al termine della distribuzione, fare clic su di essa e quindi fare clic sul pulsante **Gestisci**. Si apre la pagina di SendGrid ed è necessario verificare l'indirizzo di posta elettronica.

![Gestione di SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Nella pagina di SendGrid fare clic su **Impostazioni** > **Chiavi API** > **Crea chiave API**. Immettere il **nome della chiave API** e fare clic su **Crea & visualizzazione** (Crea e visualizza).

![Creazione dell'API in SendGrid - Primo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Creazione dell'API in SendGrid - Secondo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

La chiave API viene visualizzata solo una volta. Assicurarsi di copiarla e archiviarla in modo sicuro, perché viene usata nel passaggio successivo.

## <a name="deploy-iot-hub-in-azure"></a>Distribuire l'hub IoT in Azure

La procedura seguente effettuerà il provisioning di altri servizi correlati ad Azure IoT e distribuirà Funzioni di Azure per questo progetto.

Fare clic sul pulsante **Distribuisci in Azure** sotto. 

[![Distribuzione in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Viene quindi visualizzata la pagina seguente.

> [!NOTE]
> Se non viene visualizzata la pagina seguente, potrebbe essere necessario accedere prima all'account di Azure.

Completare il modulo di iscrizione:

  * **Gruppo di risorse**: creare un gruppo di risorse per ospitare il servizio SendGrid o usarne uno esistente. Vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nome hub IoT**: nome dell'hub IoT. Scegliere un nome univoco, diverso da quello di altri servizi già presenti.

  * **Iot Hub Sku** (SKU per hub IoT): F1 (solo uno per ogni sottoscrizione) è gratuito. È possibile visualizzare altre informazioni sui prezzi relativamente a [piano tariffario e livello di scalabilità](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Indirizzo di posta elettronica del mittente**: deve essere lo stesso indirizzo di posta elettronica usato per la configurazione del servizio SendGrid.

  > [!NOTE]
  > Controllare l'opzione **Aggiungi al dashboard** per trovare più facilmente questa applicazione in futuro.
 
![Distribuzione dell'hub IoT](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Compilare e caricare il codice

### <a name="start-vs-code"></a>Avviare Visual Studio Code

- Assicurarsi che il DevKit **non** sia connesso al computer.
- Avviare Visual Studio Code.
- Connettere il dispositivo DevKit al computer.

> [!NOTE]
> Quando si avvia VS Code, può essere visualizzato un messaggio di errore indicante che non è possibile trovare l'IDE di Arduino o il pacchetto della scheda correlato. Se questo errore viene visualizzato, chiudere VS Code, avviare nuovamente l'IDE di Arduino e VS Code dovrebbe individuare correttamente il percorso dell'IDE di Arduino.

### <a name="open-arduino-examples-folder"></a>Aprire la cartella degli esempi di Arduino

Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > AzureIoT** (Esempi per MXCHIP AZ3166 > AzureIoT) e selezionare **DoorMonitor**. Questa azione apre una nuova finestra di VS Code con una cartella di progetto all'interno.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> È anche possibile aprire l'esempio dal riquadro comandi. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

### <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione eseguire l'attività di provisioning cloud:
- Digitare `Ctrl+P` (macOS: `Cmd+P`).
- Immettere `task cloud-provision` nell'apposita casella di testo.

Nel terminale di VS Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari. Selezionare tutti gli stessi elementi dall'elenco richiesto di cui è stato effettuato il provisioning in precedenza in [Distribuire l'hub IoT in Azure](#deploy-iot-hub-in-azure).

![Provisioning cloud](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se la pagina resta in stato di caricamento quando si prova ad accedere ad Azure, vedere la [domanda frequente](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) corrispondente per risolvere questo problema. 

### <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

#### <a name="windows"></a>Windows

1. Usare `Ctrl+P` per eseguire `task device-upload`.
2. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. La schermata visualizza il numero di identificazione del DevKit e la parola *Configuration* (Configurazione).

Questa procedura imposta la stringa di connessione recuperata dal passaggio [Eseguire il provisioning dei servizi di Azure](#provision-azure-services).

VS Code inizia a verificare e caricare lo sketch Arduino nel DevKit:

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> È possibile che a volte venga visualizzato il messaggio "Error: AZ3166: Unknown package". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per correggere l'errore, vedere questa [domanda frequente](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Impostare il DevKit in modalità di configurazione: tenere premuto il pulsante A e quindi premere e rilasciare il pulsante di reimpostazione. Verrà visualizzata la schermata 'Configuration'.
2. Usare `Cmd+P` per eseguire `task device-upload`.

Questa procedura imposta la stringa di connessione recuperata dal passaggio [Eseguire il provisioning dei servizi di Azure](#provision-azure-services).

VS Code inizia a verificare e caricare lo sketch Arduino nel DevKit:

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> È possibile che a volte venga visualizzato il messaggio "Error: AZ3166: Unknown package". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per correggere l'errore, vedere questa [domanda frequente](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Verificare il progetto

Il programma viene prima di tutto inizializzato quando il DevKit è in presenza di un campo magnetico stabile.

Dopo l'inizializzazione, il messaggio `Door closed` viene visualizzato sullo schermo. Quando si verifica una variazione nel campo magnetico, lo stato passa a `Door opened`. Ogni volta che lo stato della porta cambia, si riceve una notifica tramite posta elettronica. La ricezione di questi messaggi di posta elettronica potrebbe richiedere fino a cinque minuti.

![Magneti vicino al sensore: Door closed](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magneti vicino al sensore: Door closed")

![Magnete allontanato dal sensore: Door Opened](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnete allontanato dal sensore: Door Opened")

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, vedere [FAQs](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) (Domande frequenti) o mettersi in contatto usando i canali seguenti:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come connettere un dispositivo DevKit all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT e come usare il servizio SendGrid per inviare un messaggio di posta elettronica. Ecco i passaggi successivi suggeriti:

* [Panoramica dell'acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Azure IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central)
