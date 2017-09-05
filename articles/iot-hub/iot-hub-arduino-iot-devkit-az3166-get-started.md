---
title: DevKit di IoT nel cloud - Connettere Devkit di IoT AZ3166 all'hub IoT di Azure | Microsoft Docs
description: Informazioni su come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure in questa esercitazione.
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
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) può essere usato per sviluppare e creare il prototipo delle soluzioni di Internet delle cose (IoT) sfruttando i servizi di Microsoft Azure. Include una scheda compatibile Arduino con periferiche avanzate e sensori, un pacchetto della scheda open source e un aumento delle dimensioni del [catalogo progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Operazioni da fare
Connettere [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) a un hub IoT di Azure creato, raccogliere i dati relativi a temperatura e umidità dai sensori e inviare i dati all'hub IoT.

Non hai ancora DevKit? Ricevine uno nuovo [qui](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come connettere DevKit di IoT al punto di accesso Wireless e preparare l'ambiente di sviluppo.
* Come creare un hub IoT e registrare un dispositivo per DevKit di IoT MXChip.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in DevKit di IoT MXChip.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

* Una scheda DevKit di IoT MXChip con un cavo USB micro. [Ottienila adesso](https://aka.ms/iot-devkit-purchase)
* Un computer che esegue Windows 10 o macOS 10.10+
* Una sottoscrizione di Azure attiva
  * Attivare una [versione di prova gratuita di 30 giorni dell'account di Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html)

## <a name="prepare-your-hardware"></a>Preparare l'hardware

Collegare l'hardware al computer.

### <a name="hardware-you-need"></a>Hardware necessario

* Scheda DevKit
* Micro cavo USB

![getting-started-hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>Connettere DevKit al computer

1. Collegare l'estremità USB al PC
2. Collegare l'estremità Micro USB a DevKit
3. Il LED verde accanto all'alimentazione conferma la connessione

![getting-started-connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>Configurare il WiFi

I progetti IoT si basano sulla connettività Internet. Usare le istruzioni seguenti per configurare il DevKit da connettere al WiFi.

### <a name="enter-ap-mode"></a>Passare alla modalità AP

Tenere premuto il pulsante B, poi premere e rilasciare il pulsante di reimpostazione e quindi rilasciare il pulsante B. Il DevKit passerà alla modalità AP per la configurazione del WiFi. Nella schermata verrà visualizzato l'identificatore SSID del DevKit, nonché l'indirizzo IP del portale di configurazione:

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Eseguire la connessione a DevKit AP

A questo punto, usare un altro dispositivo con WiFi (PC o telefono cellulare) per connettersi al SSID di DevKit (evidenziato nella schermata precedente) e lasciare vuota la password.

![getting-started-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>Configurare il WiFi per DevKit

Aprire l'indirizzo IP mostrato nella schermata di DevKit nel browser del computer o del telefono cellulare, selezionare la rete WiFi con cui si desidera connettere DevKit, quindi digitare la password. Fare clic su **Connetti** per completare:

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Una volta stabilita la connessione, in pochi secondi verrà riavviato il DevKit. In caso di esito positivo, verrà visualizzato il nome del WiFi e l'indirizzo IP sullo schermo:

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
L'indirizzo IP visualizzato nella foto potrebbe non corrispondere all'indirizzo IP effettivo assegnato e visualizzato sullo schermo di DevKit. Si tratta di un comportamento normale perché il WiFi usa DHCP per assegnare in modo dinamico gli indirizzi IP.

Dopo la configurazione del WiFi, le credenziali verranno mantenute nel dispositivo per tale connessione, anche se scollegato. Ad esempio, se DevKit è stato configurato per il WiFi di casa e poi si sposta DevKit in ufficio, sarà necessario riconfigurare la modalità AP (a partire dal passaggio **Passare alla modalità AP**) per connettere il DevKit alla rete WiFi dell'ufficio. 

## <a name="start-using-devkit"></a>Iniziare a usare DevKit

L'app predefinita in esecuzione in DevKit controllerà la versione più recente del firmware e visualizzerà alcuni dati di diagnostica del sensore per l'utente.

### <a name="upgrade-to-the-latest-firmware"></a>Eseguire l'aggiornamento al firmware più recente

Verranno visualizzate sullo schermo sia la versione attuale sia quella più recente del firmware se è presente un aggiornamento necessario. Seguire la guida [Aggiorna firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) per l'aggiornamento.

![getting-started-firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
Si tratta di un'attività una tantum: una volta iniziato lo sviluppo del DevKit e caricata l'app, il firmware più recente sarà fornito con l'app.

### <a name="test-various-sensors"></a>Testare i diversi sensori

Premere il pulsante B per testare i sensori, continuare a premere e rilasciare il pulsante B per scorrere ogni sensore.

![getting-started-sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>Preparare l'ambiente di sviluppo

Ora è possibile configurare l'ambiente di sviluppo: strumenti e pacchetti per compilare straordinarie applicazioni IoT. È possibile scegliere una versione di Windows o macOS in base al sistema operativo.


### <a name="windows"></a>Windows

Si consiglia di usare il pacchetto di installazione per preparare l'ambiente di sviluppo. Se si verificano problemi, è possibile seguire la [procedura del manuale](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) per risolverli.

#### <a name="download-latest-package"></a>Scaricare il pacchetto più recente

Il file `.zip` scaricato contiene tutti gli strumenti e i pacchetti necessari per lo sviluppo di DevKit.

> [!div class="button"]
[Scaricare](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> Il file `.zip` contiene gli strumenti e i pacchetti seguenti. Se si dispone già di alcuni componenti installati, lo script li rileverà e li ignorerà.
> * Node.js e Yarn: runtime per lo script di configurazione e le attività automatizzate
> * [Interfaccia della riga di comando di Azure 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): esperienza della riga di comando multipiattaforma per la gestione delle risorse di Azure, il file MSI contiene Python e pip dipendenti.
> * [Visual Studio Code](https://code.visualstudio.com/): editor del codice semplice per lo sviluppo di DevKit
> * [Estensione di Visual Studio Code per Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): abilita lo sviluppo di Arduino in Visual Studio Code
> * [IDE Arduino](https://www.arduino.cc/en/Main/Software): l'estensione per Arduino si basa su questo strumento
> * Pacchetto della scheda DevKit: catene dello strumento, librerie e progetti per il DevKit
> * Utilità ST-Link: utilità essenziali e driver

#### <a name="run-installation-script"></a>Eseguire lo script di installazione

In Esplora risorse di Windows individuare `.zip` ed estrarlo, trovare `install.cmd`, fare clic on il tasto destro del mouse e selezionare **"Esegui come amministratore"** per iniziare.

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante l'installazione, si noterà lo stato di avanzamento di ogni strumento o pacchetto.

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>Confermare per installare i driver

Il Visual Studio Code per l'estensione di Arduino si basa sull'IDE di Arduino. Se questa è la prima volta che si sta installando l'IDE di Arduino, viene chiesto di installare i driver corrispondenti:

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Il completamento dell'installazione dovrebbe richiedere circa 10 minuti, a seconda della velocità di Internet. Al termine dell'installazione, si dovrebbero visualizzare i collegamenti a Visual Studio Code e all'IDE di Arduino sul desktop.

> [!NOTE] 
In alcuni casi, quando si avvia Visual Studio Code, verrà visualizzato un errore che non è possibile trovare nell'IDE di Arduino o in un pacchetto di scheda correlati. Per risolvere l'errore, chiudere Visual Studio Code, avviare una volta l'IDE di Arduino e Visual Studio Code dovrebbe individuare correttamente il percorso dell'IDE di Arduino.


### <a name="macos-preview"></a>macOS (anteprima)

Seguire questa procedura per preparare l'ambiente di sviluppo in macOS.

#### <a name="install-azure-cli-20"></a>Installare l'interfaccia della riga di comando di Azure 2.0

Seguire la [guida ufficiale](https://docs.microsoft.com//cli/azure/install-azure-cli) per installare l'interfaccia della riga di comando di Azure 2.0:

Installare l'interfaccia della riga di comando di Azure 2.0 con il solo comando `curl`:

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

Riavviare quindi la shell dei comandi per rendere effettive le modifiche apportate:

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Installare l'IDE di Arduino

L'estensione di Arduino di Visual Studio Code si basa sull'IDE di Arduino. Scaricare e installare l'[IDE di Arduino per macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Scaricar e installare [Visual Studio Code per macOS](https://code.visualstudio.com/). Questo sarà lo strumento di sviluppo primario per la compilazione di applicazioni DevKit di IoT.

####  <a name="download-latest-package"></a>Scaricare il pacchetto più recente

1. Installare Node.js. È possibile usare la comune gestione dei pacchetti macOS [Homebrew](https://brew.sh/) o [uno strumento di installazione pre-compilato](https://nodejs.org/en/download/) per installarlo.

2. Scaricare il file `.zip` che contiene gli script delle attività necessari per lo sviluppo di DevKit in Visual Studio Code.

   > [!div class="button"]
   [Scaricare](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   Individuare `.zip` ed estrarlo. Avviare quindi l'app**Terminal** ed eseguire i comandi seguenti per configurare:

   Spostare la cartella estratta nella cartella utente macOS:
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   Installare i pacchetti `npm`:
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Installare l'estensione di Visual Studio Code per Arduino

Visual Studio Code consente di installare le estensioni di Marketplace direttamente nello strumento, facendo semplicemente clic sull'icona delle estensioni nel riquadro sinistro e quindi cercando `Arduino` per installare:

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>Installare il pacchetto della scheda DevKit

È necessario aggiungere la scheda DevKit tramite Board Manager (Gestione scheda) in Visual Studio Code.

1. Usare `Cmd+Shift+P` per richiamare il riquadro comandi e digitare **Arduino**, quindi individuare e selezionare **Arduino: Board Manager** (Arduino: Gestione scheda).

2. Fare clic su **"Additional URLs"** ("URL aggiuntivi") in basso a destra.
   ![installation-additional-urls](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. Nel file `settings.json` aggiungere una riga nella parte inferiore del riquadro `USER SETTINGS` e salvare.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. Ora in Board Manager (Gestione scheda) cercare "az3166" e installare la versione più recente.
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Ora si dispone di tutti gli strumenti necessari e dei pacchetti installati per macOS.


## <a name="open-project-folder"></a>Aprire la cartella del progetto

### <a name="launch-vs-code"></a>Avviare Visual Studio Code

Assicurarsi che DevKit non sia connesso. Innanzitutto avviare Visual Studio Code e collegare il DevKit al computer. Visual Studio Code lo troverà automaticamente e mostrerà una pagina di introduzione:

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
In alcuni casi, quando si avvia Visual Studio Code, verrà visualizzato un errore che non è possibile trovare nell'IDE di Arduino o in un pacchetto di scheda correlati. Per risolvere l'errore, chiudere Visual Studio Code, avviare nuovamente l'IDE di Arduino e Visual Studio Code dovrebbe individuare correttamente il percorso dell'IDE di Arduino.

### <a name="open-arduino-examples-folder"></a>Aprire la cartella degli esempi di Arduino

Passare alla scheda **"Arduino Examples"**("Esempi di Arduino"), procedere con `Examples for MXCHIP AZ3166 > AzureIoT` e fare clic su `GetStarted`.

![mini-solution-examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Se è capitato di richiudere il pannello, per ricaricarlo, usare `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per richiamare il riquadro comandi e digitare **Arduino** per individuare e selezionare **Arduino: Examples** (Arduino: esempi).

## <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione eseguire le attività `Ctrl+P` (macOS: `Cmd+P`) digitando "task cloud-provision":

Nel terminale di Visual Studio Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari:

![mini-solution-cloud-provision](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Compilare e caricare la definizione di Arduino

### <a name="install-required-library"></a>Installare la raccolta richiesta

1. Premere `F1` o `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per richiamare il riquadro comandi e digitare **Arduino**, quindi individuare e selezionare **Arduino: Library Manager** (Arduino: Gestione raccolte).

2. Cercare la raccolta `ArduinoJson` e fare clic su **Install** (Installa)

### <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

Usare `Ctrl+P` (macOS: `Cmd+P`) per eseguire di "task device-upload". Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. Nella schermata verrà visualizzato "Configuration" (Configurazione). Questa operazione serve per impostare la stringa di connessione che si recupera dal passaggio "task cloud-provision".

Quindi è possibile avviare la verifica e il caricamento della definizione di Arduino:

![mini-solution-device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Il DevKit riavvierà il computer e inizierà l'esecuzione del codice.

## <a name="test-the-project"></a>Verificare il progetto

In Visual Studio Code fare clic sull'icona del plug di accensione sulla barra di stato per aprire Serial Monitor (Monitoraggio seriale).

L'applicazione di esempio viene eseguita correttamente quando vengono visualizzati i risultati seguenti:

* Serial Monitor (Monitoraggio seriale) visualizza le stesse informazioni presenti nel contenuto nella schermata seguente.
* Il LED sul DevKit di IoT MXChip lampeggia.

![Output finale in Visual Studio Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, è possibile consultare le [Domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattarci tramite i canali riportati di seguito.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata connessa all'hub IoT correttamente e i dati acquisiti dal sensore sono stati inviati all'hub IoT.

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

- [Gestire la messaggistica dei dispositivi cloud con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Salvare i messaggi dell'hub IoT nell'archivio dati di Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Usare App Web di Azure per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Previsioni meteo usando i dati sensore dell'hub IoT in Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gestione dei dispositivi con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Monitoraggio remoto e notifiche con App per la logica](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
