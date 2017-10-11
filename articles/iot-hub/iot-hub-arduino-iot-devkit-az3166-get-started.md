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
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 253a2d34ea5b3f4f8253900be27c54fe5ca1dacf
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

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

Aprire l'indirizzo IP mostrato nella schermata del DevKit nel browser del computer o del telefono cellulare, selezionare la rete Wi-Fi a cui si desidera connettere il DevKit e quindi digitare la password. Selezionare **Connect** (Connetti).

![Campo della password e pulsante Connect (Connetti)](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Dopo avere stabilito la connessione, il DevKit si riavvia in pochi secondi. Verranno visualizzati il nome della rete Wi-Fi e l'indirizzo IP:

![Nome della rete Wi-Fi e indirizzo IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> L'indirizzo IP visualizzato nella foto potrebbe non corrispondere all'indirizzo IP effettivo assegnato e visualizzato nella schermata di DevKit. Si tratta di un comportamento normale perché il Wi-Fi usa il protocollo DHCP che assegna gli indirizzi IP in modo dinamico.

Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato. Se, ad esempio, si configura il DevKit per il Wi-Fi di casa e poi lo si sposta in ufficio, sarà necessario riconfigurare la modalità AP (a partire dal passaggio "Passare alla modalità AP") per connettere il DevKit alla rete Wi-Fi dell'ufficio. 

## <a name="start-using-the-devkit"></a>Iniziare a usare il DevKit

L'app predefinita in esecuzione nel DevKit controlla se è installata la versione più recente del firmware e visualizza alcuni dati di diagnostica dei sensori.

### <a name="upgrade-to-the-latest-firmware"></a>Eseguire l'aggiornamento al firmware più recente

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
[Scaricare](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


Il file con estensione zip contiene gli strumenti e i pacchetti seguenti. Se si dispone già di alcuni componenti installati, lo script li rileverà e li ignorerà.

* Node.js e Yarn: runtime per lo script di configurazione e le attività automatizzate.
* [MSI dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): riga di comando multipiattaforma per la gestione delle risorse di Azure. Il file MSI contiene Python e pip dipendenti.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor di codice semplice per lo sviluppo del DevKit.
* [Estensione di Visual Studio Code per Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): estensione che abilita lo sviluppo di Arduino in Visual Studio Code.
* [IDE Arduino](https://www.arduino.cc/en/Main/Software): strumento su cui si basa l'estensione per Arduino.
* Pacchetto della scheda DevKit: catene di strumenti, librerie e progetti per il DevKit.
* Utilità ST-Link: strumenti e driver essenziali.

#### <a name="run-the-installation-script"></a>Eseguire lo script di installazione

In Esplora file di Windows individuare il file con estensione zip e decomprimerlo. Trovare il file install.cmd, fare clic con il pulsante destro del mouse su di esso e selezionare **Esegui come amministratore**.

![Esplora file](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante l'installazione viene visualizzato lo stato di ogni strumento o pacchetto.

![Stato dell'installazione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="install-drivers"></a>Installare i driver

Il Visual Studio Code per l'estensione di Arduino si basa sull'IDE di Arduino. Se questa è la prima volta che si installa l'IDE di Arduino, viene chiesto di installare i driver pertinenti:

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

L'installazione richiede circa 10 minuti a seconda della velocità della connessione Internet. Al termine dell'installazione, si dovrebbero vedere i collegamenti a Visual Studio Code e all'IDE di Arduino sul desktop.

> [!NOTE] 
> In alcuni casi, all'avvio di Visual Studio Code viene visualizzato un errore che indica che non è possibile trovare l'IDE di Arduino o il pacchetto della scheda correlato. Chiudere Visual Studio Code e riavviare l'IDE di Arduino. Visual Studio Code dovrebbe a questo punto individuare il percorso corretto dell'IDE di Arduino.


### <a name="macos-preview"></a>macOS (anteprima)

Seguire questa procedura per preparare l'ambiente di sviluppo in macOS.

#### <a name="install-azure-cli-20"></a>Installare l'interfaccia della riga di comando di Azure 2.0

1. Installare l'interfaccia della riga di comando di Azure 2.0 usando un comando `curl`:

   ```bash
   curl -L https://aka.ms/InstallAzureCli | bash
   ```

2. Riavviare la shell dei comandi per rendere effettive le modifiche apportate:

   ```bash
   exec -l $SHELL
   ```

Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure 2.0, vedere la [guida ufficiale](https://docs.microsoft.com//cli/azure/install-azure-cli).

#### <a name="install-the-arduino-ide"></a>Installare l'IDE di Arduino

L'estensione di Arduino di Visual Studio Code si basa sull'IDE di Arduino. Scaricare e installare l'[IDE di Arduino per macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Scaricare e installare [Visual Studio Code per macOS](https://code.visualstudio.com/). Questo sarà lo strumento di sviluppo principale per la compilazione di applicazioni DevKit di IoT.

####  <a name="download-the-latest-package"></a>Scaricare il pacchetto più recente

1. Installare Node.js. È possibile usare la gestione dei pacchetti macOS di uso più comune [Homebrew](https://brew.sh/) o lo [strumento di installazione pre-compilato](https://nodejs.org/en/download/) per installarlo.

2. Scaricare il file con estensione zip che contiene gli script delle attività necessarie per lo sviluppo di DevKit in Visual Studio Code.

   > [!div class="button"]
   [Scaricare](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

3. Individuare il file con estensione zip e decomprimerlo. Avviare quindi l'app **Terminal** ed eseguire i comandi seguenti:

   a. Spostare la cartella estratta nella cartella utente macOS:
      ```bash
      mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
      ```
  
   b. Installare i pacchetti npm:
      ```
      npm install
      ```

#### <a name="install-the-vs-code-extension-for-arduino"></a>Installare l'estensione di Visual Studio Code per Arduino

È possibile installare le estensioni di Azure Marketplace direttamente in Visual Studio Code. Selezionare l'icona delle estensioni nel riquadro a sinistra, cercare **Arduino** e quindi selezionare **Install** (Installa):

![Ricerca di un'estensione Arduino](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-the-devkit-board-package"></a>Installare il pacchetto della scheda DevKit

Aggiungere la scheda DevKit tramite Board Manager (Gestione scheda) in Visual Studio Code.

1. Premere Cmd+MAIUSC+P per aprire il riquadro comandi, digitare **Arduino**, cercare e selezionare **Arduino: Board Manager** (Arduino: Gestione scheda).

2. Selezionare **Additional URLs** (URL aggiuntivi) in basso a destra.
   ![Collegamento a URL aggiuntivi](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. Nel file settings.json aggiungere una riga in fondo al riquadro **USER SETTINGS** e salvare il file.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![Codice aggiunto al riquadro USER SETTINGS](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. In Board Manager (Gestione scheda) cercare **az3166** e installare la versione più recente.
   ![Installazione di az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Ora si dispone di tutti gli strumenti necessari e dei pacchetti installati per macOS.


## <a name="open-the-project-folder"></a>Aprire la cartella del progetto

### <a name="start-vs-code"></a>Avviare Visual Studio Code

Assicurarsi che DevKit non sia connesso. Avviare per prima cosa Visual Studio Code e connettere il DevKit al computer. Visual Studio Code individua DevKit automaticamente e visualizza una pagina di introduzione:

![Pagina Introduzione](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> In alcuni casi, all'avvio di Visual Studio Code viene visualizzato un errore che indica che non è possibile trovare l'IDE di Arduino o il pacchetto della scheda correlato. Chiudere Visual Studio Code e riavviare l'IDE di Arduino. Visual Studio Code dovrebbe a questo punto individuare il percorso corretto dell'IDE di Arduino.


### <a name="open-the-arduino-examples-folder"></a>Aprire la cartella degli esempi di Arduino

Nella scheda **Arduino Examples** (Esempi di Arduino) passare a **Examples for MXCHIP AZ3166** > **AzureIoT** e selezionare **GetStarted**.

![Scheda Arduino Examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere CTRL+MAIUSC+P (macOS: Cmd+MAIUSC+P) per aprire il riquadro comandi, digitare **Arduino**, quindi cercare e selezionare **Arduino: Examples** (Arduino: Esempi).

## <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione eseguire l'attività desiderata premendo CTRL+P (macOS: Cmd+P) immettendo `task cloud-provision`.

Nel terminale di Visual Studio Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari:

![Riga di comando interattiva](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Compilare e caricare la definizione di Arduino

### <a name="install-the-required-library"></a>Installare la libreria necessaria

1. Premere F1 o CTRL+MAIUSC+P (macOS: Cmd+MAIUSC+P) per aprire il riquadro comandi, digitare **Arduino**, quindi cercare e selezionare **Arduino: Library Manager** (Arduino: Gestione libreria).

2. Cercare la libreria **ArduinoJson** e selezionare **Install** (Installa).

### <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

Usare CTRL+P (macOS: Cmd+P) per eseguire `task device-upload`. Il terminale richiederà di passare alla modalità di configurazione. Tenere quindi premuto il pulsante A e premere e rilasciare il pulsante di reimpostazione. Verrà visualizzata la schermata **Configuration** (Configurazione). Qui viene impostata la stringa di connessione recuperata dal passaggio `task cloud-provision`.

Il terminal inizia quindi a verificare e a caricare la definizione di Arduino:

![Verifica e caricamento della definizione di Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

## <a name="test-the-project"></a>Verificare il progetto

In Visual Studio Code selezionare l'icona della spina di alimentazione sulla barra di stato per aprire Serial Monitor (Monitoraggio seriale).

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

