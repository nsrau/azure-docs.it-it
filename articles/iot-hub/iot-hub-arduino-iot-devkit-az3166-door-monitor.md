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
ms.openlocfilehash: a620b592a33f9de11de53d623d257f203da2157b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61370366"
---
# <a name="door-monitor"></a>Monitoraggio della porta          

MXChip IoT DevKit contiene un sensore magnetico predefinito. In questo progetto si rileva la presenza o l'assenza di un campo magnetico forte nelle vicinanze, in questo caso, proveniente da un piccolo magnete permanente.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questo progetto viene illustrato:
- Come usare il sensore magnetico di MXChip IoT DevKit per rilevare lo spostamento di un magnete nelle vicinanze.
- Come usare il servizio SendGrid per inviare una notifica all'indirizzo di posta elettronica.

> [!NOTE]
> Per un uso pratico di questo progetto, eseguire le attività seguenti:
> - Montare un magnete sul bordo di una porta.
> - Montare il DevKit sullo stipite della porta vicino al magnete. L'apertura o la chiusura della porta attiverà il sensore e si riceverà una notifica tramite posta elettronica dell'evento.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](iot-hub-arduino-iot-devkit-az3166-get-started.md) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo

Una sottoscrizione di Azure attiva. Se non è disponibile, è possibile registrarsi tramite uno di questi metodi:

* Attivare una [versione di valutazione gratuita di 30 giorni dell'account Microsoft Azure](https://azure.microsoft.com/free/).
* Richiedere il [credito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Distribuire il servizio SendGrid in Azure

[SendGrid](https://sendgrid.com/) è una piattaforma per il recapito tramite posta elettronica basata sul cloud. Questo servizio verrà usato per inviare notifiche tramite posta elettronica.

> [!NOTE]
> Se è già stato distribuito un servizio SendGrid, è possibile procedere direttamente alla [distribuzione dell'hub IoT in Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Distribuzione di SendGrid

Per effettuare il provisioning dei servizi di Azure, usare il pulsante **Distribuisci in Azure**. Questo pulsante consente una distribuzione semplice e rapida dei progetti open source in Microsoft Azure.

Fare clic sul pulsante **Distribuisci in Azure** sotto. 

[![Distribuzione in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Se non è già stato eseguito l'accesso all'account Azure, accedere ora. 

Verrà visualizzato il modulo di iscrizione di SendGrid.

![Distribuzione di SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Completare il modulo di iscrizione:

   * **Gruppo di risorse**: creare un gruppo di risorse per ospitare il servizio SendGrid o usarne uno esistente. Vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nome**: nome del servizio SendGrid. Scegliere un nome univoco, diverso da quello di altri servizi già presenti.

   * **Password**: il servizio richiede una password, che non verrà usata per tutti gli elementi di questo progetto.

   * **Messaggio di posta elettronica**: il servizio SendGrid invierà la verifica a questo indirizzo di posta elettronica.

Selezionare l'opzione **Aggiungi al dashboard** per trovare più facilmente questa applicazione in futuro, quindi fare clic su **Acquista** per inviare il modulo di accesso.
 
### <a name="sendgrid-api-key-creation"></a>Creazione della chiave API di SendGrid

Al termine della distribuzione, fare clic su di essa e quindi fare clic sul pulsante **Gestisci**. Viene visualizzata la pagina dell'account SendGrid, in cui è necessario verificare l'indirizzo di posta elettronica.

![Gestione di SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Nella pagina di SendGrid fare clic su **Impostazioni** > **Chiavi API** > **Crea chiave API**.

![Creazione dell'API in SendGrid - Primo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Nella pagina **Crea chiave API** immettere il **nome della chiave API** e fare clic su **Create & View** (Crea e visualizza).

![Creazione dell'API in SendGrid - Secondo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

La chiave API viene visualizzata solo una volta. Assicurarsi di copiarla e archiviarla in modo sicuro, perché viene usata nel passaggio successivo.

## <a name="deploy-iot-hub-in-azure"></a>Distribuire l'hub IoT in Azure

La procedura seguente effettuerà il provisioning di altri servizi correlati ad Azure IoT e distribuirà Funzioni di Azure per questo progetto.

Fare clic sul pulsante **Distribuisci in Azure** sotto. 

[![Distribuzione in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Viene visualizzato il modulo di iscrizione.

![Distribuzione dell'hub IoT](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Compilare i campi nel modulo di iscrizione.

   * **Gruppo di risorse**: creare un gruppo di risorse per ospitare il servizio SendGrid o usarne uno esistente. Vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nome hub IoT**: il nome dell'hub IoT. Scegliere un nome univoco, diverso da quello di altri servizi già presenti.

   * **Iot Hub Sku** (SKU per hub IoT): F1 (solo uno per ogni sottoscrizione) è gratuito. Per altre informazioni sui prezzi, vedere la [pagina Prezzi](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Indirizzo di posta elettronica del mittente**: questo campo deve corrispondere all'indirizzo di posta elettronica usato per la configurazione del servizio SendGrid.

Selezionare l'opzione **Aggiungi al dashboard** per trovare più facilmente questa applicazione in futuro, quindi fare clic su **Acquista** quando si è pronti per continuare con il passaggio successivo.
 
## <a name="build-and-upload-the-code"></a>Compilare e caricare il codice

Caricare ora il codice di esempio in VS Code ed effettuare il provisioning dei servizi di Azure necessari.

### <a name="start-vs-code"></a>Avviare Visual Studio Code

- Assicurarsi che il DevKit **non** sia connesso al computer.
- Avviare Visual Studio Code.
- Connettere il dispositivo DevKit al computer.

> [!NOTE]
> Quando si avvia VS Code, può essere visualizzato un messaggio di errore indicante che non è possibile trovare l'IDE di Arduino o il pacchetto della scheda correlato. Se questo errore viene visualizzato, chiudere VS Code, avviare nuovamente l'IDE di Arduino e VS Code dovrebbe individuare correttamente il percorso dell'IDE di Arduino.

### <a name="open-arduino-examples-folder"></a>Aprire la cartella degli esempi di Arduino

Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > AzureIoT** (Esempi per MXCHIP AZ3166 > AzureIoT) e selezionare **DoorMonitor**. Questa azione apre una nuova finestra di VS Code con una cartella di progetto all'interno.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

È anche possibile aprire l'app di esempio dal riquadro comandi. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

### <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione eseguire l'attività di provisioning cloud:
- Digitare `Ctrl+P` (macOS: `Cmd+P`).
- Immettere `task cloud-provision` nell'apposita casella di testo.

Nel terminale di VS Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari. Selezionare tutti gli stessi elementi dall'elenco richiesto di cui è stato effettuato il provisioning in precedenza in [Distribuire l'hub IoT in Azure](#deploy-iot-hub-in-azure).

![Provisioning cloud](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se la pagina resta in stato di caricamento quando si prova ad accedere ad Azure, vedere la [sezione "page hanges when logging in" (La pagina resta in stato di caricamento durante l'accesso) delle domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) per risolvere questo problema. 

### <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

Caricare ora il codice per il dispositivo.

#### <a name="windows"></a>Windows

1. Usare `Ctrl+P` per eseguire `task device-upload`.

2. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. La schermata visualizza il numero di identificazione del DevKit e la parola *Configuration* (Configurazione).

#### <a name="macos"></a>macOS

1. Attivare la modalità di configurazione per DevKit: Tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. Verrà visualizzata la schermata 'Configuration'.

2. Fare clic su `Cmd+P` per eseguire `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Verificare, caricare ed eseguire l'app di esempio

La stringa di connessione recuperata dal passaggio [Eseguire il provisioning dei servizi di Azure](#provision-azure-services) viene ora impostata. 

VS Code inizia a verificare e caricare lo sketch Arduino nel DevKit.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> In alcuni casi, è possibile che venga visualizzato il messaggio "Errore: AZ3166: Pacchetto sconosciuto". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per risolvere questo errore, vedere la [sezione sullo sviluppo delle domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Verificare il progetto

Il programma viene prima di tutto inizializzato quando il DevKit è in presenza di un campo magnetico stabile.

Dopo l'inizializzazione, il messaggio `Door closed` viene visualizzato sullo schermo. Quando si verifica una variazione nel campo magnetico, lo stato passa a `Door opened`. Ogni volta che lo stato della porta cambia, si riceve una notifica tramite posta elettronica. La ricezione di questi messaggi di posta elettronica potrebbe richiedere fino a cinque minuti.

![Magneti vicino al sensore: Door closed](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magneti vicino al sensore: Door closed")

![Magnete allontanato dal sensore: Door Opened](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnete allontanato dal sensore: Door Opened")

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, vedere le [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o mettersi in contatto usando i canali seguenti:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come connettere un dispositivo DevKit all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT ed è stato usato il servizio SendGrid per inviare un messaggio di posta elettronica. Ecco i passaggi successivi suggeriti:

* [Panoramica dell'acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Azure IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central)
