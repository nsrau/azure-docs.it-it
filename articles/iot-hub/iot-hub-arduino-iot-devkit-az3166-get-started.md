---
title: 'DevKit di IoT al cloud: Connettere DevKit di IoT AZ3166 all''hub IoT di Azure | Microsoft Docs'
description: Questa esercitazione descrive come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.openlocfilehash: 0b8ae318fab2eaa186dca050ce2710b1ff232783
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

È possibile usare il [DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure. Il DevKit include una scheda compatibile con Arduino con periferiche e sensori avanzati, un pacchetto della scheda open source e un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) sempre in crescita.

## <a name="what-you-do"></a>Operazioni da fare
Connettere il [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) a un hub IoT di Azure creato, raccogliere i dati sulla temperatura e l'umidità rilevati dai sensori e inviarli all'hub IoT.

Non hai ancora DevKit? [Ottienine uno](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come connettere il DevKit di IoT al punto di accesso wireless e preparare l'ambiente di sviluppo.
* Come creare un hub IoT e registrare un dispositivo per il DevKit di IoT MXChip.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio nel DevKit di IoT MXChip.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

* Una scheda DevKit di IoT MXChip con un cavo USB micro. [Ottienila ora](https://aka.ms/iot-devkit-purchase).
* Un computer che esegue Windows 10 o macOS 10.10+.
* Una sottoscrizione di Azure attiva. [Attivare una versione di valutazione gratuita di 30 giorni dell'account Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Preparare l'hardware

Collegare l'hardware al computer.

Sono necessari i componenti hardware seguenti:

* Scheda DevKit
* Cavo USB micro

![Requisiti hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Per connettere il DevKit al computer:

1. Collegare l'estremità USB al computer.
2. Collegare l'estremità USB micro al DevKit.
3. Il LED verde dell'alimentazione conferma la connessione.

![Connessioni hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurazione Wi-Fi

I progetti IoT si basano sulla connettività Internet. Usare le istruzioni seguenti per configurare il DevKit per la connessione al Wi-Fi.

### <a name="enter-ap-mode"></a>Passare alla modalità AP

Tenere premuto il pulsante B, premere e rilasciare il pulsante di reimpostazione e quindi rilasciare il pulsante B. Il DevKit passerà alla modalità AP per la configurazione del Wi-Fi. La schermata visualizza l'identificatore SSID del DevKit e l'indirizzo IP del portale di configurazione.

![Pulsante di reimpostazione, pulsante B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Eseguire la connessione a DevKit AP

Usare a questo punto un altro dispositivo abilitato per il Wi-Fi (computer o telefono cellulare) per connettersi al SSID del DevKit (evidenziato nell'immagine precedente). Lasciare vuoto il campo della password.

![Informazioni di rete e pulsante Connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurare il Wi-Fi per il DevKit

Aprire l'indirizzo IP mostrato nella schermata del DevKit nel browser del computer o del telefono cellulare, selezionare la rete Wi-Fi a cui si desidera connettere il DevKit e quindi digitare la password. Selezionare **Connessione**.

![Campo della password e pulsante Connect (Connetti)](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Dopo avere stabilito la connessione, il DevKit si riavvia in pochi secondi. Verranno visualizzati il nome della rete Wi-Fi e l'indirizzo IP:

![Nome della rete Wi-Fi e indirizzo IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> L'indirizzo IP visualizzato nella foto potrebbe non corrispondere all'indirizzo IP effettivo assegnato e visualizzato nella schermata di DevKit. Si tratta di un comportamento normale perché il Wi-Fi usa il protocollo DHCP che assegna gli indirizzi IP in modo dinamico.

Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato. Se, ad esempio, si configura il DevKit per il Wi-Fi di casa e poi lo si sposta in ufficio, sarà necessario riconfigurare la modalità AP (a partire dal passaggio "Passare alla modalità AP") per connettere il DevKit alla rete Wi-Fi dell'ufficio. 

## <a name="start-using-the-devkit"></a>Iniziare a usare il DevKit

L'app predefinita in esecuzione nel DevKit controlla se è installata la versione più recente del firmware e visualizza alcuni dati di diagnostica dei sensori.

### <a name="upgrade-to-the-latest-firmware"></a>Eseguire l'aggiornamento al firmware più recente

> [!NOTE] 
> A partire dalla versione 1.1 DevKit abilita ST-SAFE nel bootloader. Per garantirne il corretto funzionamento, è necessario aggiornare il firmware in caso di esecuzione della versione 1.1.

Se è necessario aggiornare il firmware, la schermata visualizza la versione del firmware attualmente installata e quella più recente. Per eseguire l'aggiornamento, seguire le istruzioni nella guida [Upgrade firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) (Aggiornare il firmware).

![Visualizzazione delle versioni attuale e più recente del firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Questa operazione viene eseguita una sola volta. Dopo avere iniziato a sviluppare nel DevKit e avere caricato l'app, il firmware più recente sarà presente nell'app.

### <a name="test-various-sensors"></a>Testare i diversi sensori

Premere il pulsante B per testare i sensori. Continuare a premere e rilasciare il pulsante B per passare di sensore in sensore.

![Visualizzazione del pulsante B e del sensore](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Ora è possibile configurare l'ambiente di sviluppo: strumenti e pacchetti per compilare straordinarie applicazioni IoT. È possibile scegliere la versione per Windows o macOS in base al sistema operativo in uso.

### <a name="windows"></a>Windows

Si consiglia di usare il pacchetto di installazione per preparare l'ambiente di sviluppo. Se si verificano problemi, è possibile seguire la [procedura manuale](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) per risolverli.

#### <a name="download-the-latest-package"></a>Scaricare il pacchetto più recente

Il file con estensione zip che viene scaricato contiene tutti gli strumenti e i pacchetti necessari per sviluppare il DevKit.

> [!div class="button"]
[Scaricare](https://aka.ms/devkit/prod/installpackage/latest)

Il file con estensione zip contiene gli strumenti e i pacchetti seguenti. Se si dispone già di alcuni componenti installati, lo script li rileverà e li ignorerà.

* Node.js e Yarn: runtime per lo script di configurazione e le attività automatizzate.
* [MSI dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): riga di comando multipiattaforma per la gestione delle risorse di Azure. Il file MSI contiene Python e pip dipendenti.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor di codice semplice per lo sviluppo del DevKit.
* [Estensione di Visual Studio Code per Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): estensione che abilita lo sviluppo di Arduino in Visual Studio Code.
* [IDE Arduino](https://www.arduino.cc/en/Main/Software): strumento su cui si basa l'estensione per Arduino.
* Pacchetto della scheda DevKit: catene di strumenti, librerie e progetti per il DevKit.
* Utilità ST-Link: strumenti e driver essenziali.

#### <a name="run-the-installation-script"></a>Eseguire lo script di installazione

In Esplora file di Windows individuare il file con estensione zip e decomprimerlo. Trovare il file `install.cmd`, fare clic con il pulsante destro del mouse su di esso e selezionare **Esegui come amministratore**.

![Esplora file](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante l'installazione viene visualizzato lo stato di ogni strumento o pacchetto.

![Stato dell'installazione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> A seconda dell'ambiente, a volte si otterrà un errore durante l'installazione dell'IDE Arduino. In questo caso, è possibile provare a [installare singolarmente l'IDE Arduino](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) e a eseguire di nuovo install.cmd. Altrimenti, seguire la [procedura manuale](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) per installare tutti gli strumenti e i pacchetti necessari.

#### <a name="install-drivers"></a>Installare i driver

Il Visual Studio Code per l'estensione di Arduino si basa sull'IDE di Arduino. Se questa è la prima volta che si installa l'IDE di Arduino, viene chiesto di installare i driver pertinenti:

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

L'installazione richiede circa 10 minuti a seconda della velocità della connessione Internet. Al termine dell'installazione, si dovrebbero vedere i collegamenti a Visual Studio Code e all'IDE di Arduino sul desktop.

> [!NOTE] 
> In alcuni casi, all'avvio di Visual Studio Code viene visualizzato un errore che indica che non è possibile trovare l'IDE di Arduino o il pacchetto della scheda correlato. Per risolvere il problema, chiudere Visual Studio Code e riavviare l'IDE di Arduino. Visual Studio Code dovrebbe a questo punto individuare il percorso corretto dell'IDE di Arduino.

### <a name="macos"></a>macOS

Si consiglia di usare l'esperienza di installazione in un solo clic per preparare l'ambiente di sviluppo. Se si verificano problemi, è possibile seguire la [procedura manuale](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) per risolverli.

#### <a name="install-homebrew"></a>Installare Homebrew

> [!NOTE] 
> Se è stato installato Homebrew, è possibile ignorare questo passaggio.

Seguire le [istruzioni di installazione di Homebrew](https://docs.brew.sh/Installation.html) per completare l'installazione.

#### <a name="download-the-latest-package"></a>Scaricare il pacchetto più recente
Il file con estensione zip che viene scaricato contiene tutti gli strumenti e i pacchetti necessari per sviluppare il DevKit.

> [!div class="button"]
[Scaricare](https://aka.ms/devkit/prod/installpackage/mac/latest)

Il file con estensione zip contiene gli strumenti e i pacchetti seguenti. Se si dispone già di alcuni componenti installati, lo script li rileverà e li ignorerà.

* Node.js e Yarn: runtime per lo script di configurazione e le attività automatizzate.
* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): esperienza della riga di comando multipiattaforma per la gestione delle risorse di Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor di codice semplice per lo sviluppo del DevKit.
* [Estensione di Visual Studio Code per Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): estensione che abilita lo sviluppo di Arduino in Visual Studio Code.
* [IDE Arduino](https://www.arduino.cc/en/Main/Software): strumento su cui si basa l'estensione per Arduino.
* Pacchetto della scheda DevKit: catene di strumenti, librerie e progetti per il DevKit.
* Utilità ST-Link: strumenti e driver essenziali.

#### <a name="run-the-installation-script"></a>Eseguire lo script di installazione

In Finder individuare il file con estensione ZIP ed estrarre i file:

Avviare l'app Terminal, individuare la cartella in cui sono stati estratti i file con estensione ZIP ed eseguire:

```bash
./install.sh
```

> [!NOTE] 
> Se si visualizza l'errore di autorizzazione di Homebrew, eseguire `brew doctor` per correggerlo. Per informazioni più dettagliate controllare le [Domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos).

Ora si dispone di tutti gli strumenti necessari e dei pacchetti installati per macOS.

## <a name="open-the-project-folder"></a>Aprire la cartella del progetto

Iniziare creando un hub IoT di Azure, connettervi DevKit, raccogliere i dati sulla temperatura e l'umidità rilevati dai sensori e inviarli all'hub IoT.

### <a name="start-vs-code"></a>Avviare Visual Studio Code

Assicurarsi che DevKit non sia connesso. Avviare per prima cosa Visual Studio Code e connettere il DevKit al computer. Visual Studio Code individua DevKit automaticamente e visualizza una pagina di introduzione:

![Pagina Introduzione](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> In alcuni casi, quando si avvia Visual Studio Code, verrà visualizzato un errore indicante che non è possibile trovare l'IDE di Arduino o un pacchetto di scheda correlati. Chiudere Visual Studio Code, avviare nuovamente l'IDE di Arduino e Visual Studio Code dovrebbe individuare correttamente il percorso dell'IDE di Arduino.


### <a name="open-the-arduino-examples-folder"></a>Aprire la cartella degli esempi di Arduino

Espandere il lato sinistro della sezione **ARDUINO EXAMPLES**, passare a **Examples for MXCHIP AZ3166 > AzureIoT** e selezionare **GetStarted**. Verrà visualizzata una nuova finestra di Visual Studio Code con una cartella di progetto.

![Scheda Arduino Examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione eseguire l'attività desiderata premendo `Ctrl+P` (macOS: `Cmd+P`) e immettendo `task cloud-provision`:

Nel terminale di Visual Studio Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari:

![Riga di comando interattiva](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Compilare e caricare la definizione di Arduino

### <a name="install-the-required-library"></a>Installare la libreria necessaria

1. Premere `F1` o `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Library Manager**.

2. Cercare la libreria **ArduinoJson** e selezionare **Install**: ![Install Arduino library](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/arduino-json.png)

### <a name="build-and-upload-the-device-code-windows"></a>Compilare e caricare il codice del dispositivo (Windows)
1. Usare `Ctrl+P` per eseguire `task device-upload`.
2. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. La schermata visualizza l'ID di DevKit e la voce 'Configuration'.

Questa operazione serve per impostare la stringa di connessione recuperata dal passaggio `task cloud-provision`.

Il terminal inizia quindi a verificare e a caricare la definizione di Arduino:

![Verifica e caricamento della definizione di Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

### <a name="build-and-upload-the-device-code-mac"></a>Compilare e caricare il codice del dispositivo (Mac)

1. Impostare DevKit in modalità di configurazione: tenere premuto il pulsante A e quindi premere e rilasciare il pulsante di reimpostazione. Verrà visualizzata la schermata 'Configuration'.
2. Usare `Cmd+P` per eseguire `task device-upload`.

Questa operazione serve per impostare la stringa di connessione recuperata dal passaggio `task cloud-provision`.

Visual Studio Code inizia quindi a verificare e a caricare la definizione di Arduino:

![device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

## <a name="test-the-project"></a>Verificare il progetto

In Visual Studio Code attenersi alla procedura seguente per aprire e configurare Serial Monitor:

1. Fare clic sulla parola `COM[X]` sulla barra di stato per impostare la porta COM corretta su `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Fare clic sull'icona del plug di accensione sulla barra di stato per aprire Serial Monitor: ![serial-monitor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/serial-monitor.png)

3. Sulla barra di stato fare clic sul numero che rappresenta la velocità di trasmissione in baud e impostarlo su `115200`: ![baud-rate](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

L'applicazione di esempio viene eseguita correttamente quando vengono visualizzati i risultati seguenti:

* Serial Monitor (Monitoraggio seriale) visualizza le stesse informazioni presenti nel contenuto nella schermata seguente.
* Il LED sul DevKit di IoT MXChip lampeggia.

![Output finale in Visual Studio Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi è possibile consultare le [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). È possibile anche possibile lasciare commenti e suggerimenti personali in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata a questo punto connessa all'hub IoT e i dati acquisiti dai sensori sono stati inviati all'hub IoT.

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

- [Gestire la messaggistica dei dispositivi cloud con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Salvare i messaggi dell'hub IoT nell'archivio dati di Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Usare App Web di Azure per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Previsioni meteo usando i dati sensore dell'hub IoT in Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gestione dei dispositivi con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Monitoraggio remoto e notifiche con App per la logica](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
