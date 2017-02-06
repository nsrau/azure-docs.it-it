---
title: Risoluzione dei problemi | Documentazione Microsoft
description: Pagina sulla risoluzione dei problemi relativi all&quot;esperienza Node.js del dispositivo Raspberry Pi
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: Problemi di IoT, Internet delle cose
ms.assetid: 22cf50dc-8206-42a2-a1fc-f75fa85135fa
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: c0ddd7e68accf28ee65ba70ea992acd413da6f5b
ms.openlocfilehash: 6a617da5135a362a0d6a0670c55242d64b1bdd85


---
# <a name="troubleshooting"></a>Risoluzione dei problemi
## <a name="hardware-issues"></a>Problemi hardware
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>L'applicazione viene eseguita correttamente, ma il LED non lampeggia
Questo problema è sempre correlato alla connettività del circuito hardware. Per identificare i problemi, seguire questa procedura:

1. Assicurarsi di aver scelto il connettore **GPIO** corretto sulla scheda. Le due porte devono essere **GPIO GND (Pin 6)** e **GPIO 04 (Pin 7)**.
2. Assicurarsi che la polarità del LED sia corretta. La parte più lunga deve indicare il pin anodo, **positivo**.
3. Usare il **pin da 3,3 V** e il **pin GND** nel dispositivo Raspberry Pi 3. Trattare il dispositivo Pi come alimentazione CC. Assicurarsi che il LED funzioni correttamente.

![Specifiche del LED](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Altri problemi hardware
Per informazioni sulla risoluzione dei problemi più comuni del dispositivo Raspberry Pi 3, vedere la relativa [pagina ufficiale sulla risoluzione dei problemi](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problemi del pacchetto Node.js
### <a name="no-response-during-gulp-tasks"></a>Nessuna risposta durante le attività gulp
Se si verificano problemi durante l'esecuzione di attività gulp, è possibile aggiungere l'opzione `--verbose` per il debug. Provare a terminare le attività gulp correnti con CTRL + C e quindi eseguire questo comando nella finestra della console per visualizzare i messaggi di debug. È possibile visualizzare i messaggi di errore dettagliati nell'output della console.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemi di individuazione dei dispositivi
Per risolvere più facilmente i problemi comuni relativi al comando `devdisco`, vedere il file [readme](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="npm-issues"></a>Problemi di npm
Provare ad aggiornare il pacchetto npm usando il comando seguente:

```bash
npm install -g npm
```

Se il problema persiste, aggiungere commenti alla fine di questo articolo o definire un problema di GitHub nel [repository degli esempi](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started).

## <a name="remote-debugging"></a>Debug remoto
### <a name="run-the-sample-application-in-debug-mode"></a>Eseguire l'applicazione di esempio in modalità di debug
```bash
gulp run --debug
```

Quando il motore di debug è pronto, nell'output della console dovrebbe essere visibile ```Debugger listening on port 5858```.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configurare Visual Studio Code per la connessione al dispositivo remoto
1. Aprire il pannello **Debug** sul lato sinistro.
2. Fare clic sul pulsante verde **Avvia debug** (F5). Visual Studio Code apre un file launch.json.
3. Aggiornare il file launch.json con il contenuto seguente. Sostituire `[device hostname or IP address]` con il nome host o l'indirizzo IP effettivo del dispositivo.

> [!NOTE]
> Per altre informazioni sul debug in Visual Studio, vedere la sezione relativa al [debug dell'articolo su Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging#_launchjson-attributes).


```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Configurazione del debug remoto](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Collegarsi all'applicazione remota
Fare clic sul pulsante verde **Avvia debug** (F5) per avviare il debug.

Leggere [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) per altre informazioni sul debugger.

![Debug remoto interattivo](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problemi dell'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure è una versione di anteprima. Per cercare le soluzioni ai problemi, vedere la [guida all'installazione della versione di anteprima](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Se si rilevano bug con lo strumento, segnalare un [problema](https://github.com/Azure/azure-cli/issues) nella sezione **Issues** (Problemi) del repository GitHub.

Per risolvere più facilmente i problemi comuni, vedere il file [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problemi di installazione di Python
### <a name="legacy-installation-issues-macos"></a>Problemi di installazione di pacchetti legacy (macOS)
Durante l'installazione di pip, viene generato un errore di autorizzazione quando vengono installati pacchetti legacy con autorizzazioni **su**. Questa situazione si verifica perché l'installazione precedente di Python con brew (macOS) non è stata disinstallata completamente. Alcuni pacchetti pip di un'installazione precedente sono stati creati dall'utente root e questo causa l'errore di autorizzazione. La soluzione consiste nel rimuovere i pacchetti installati dall'utente root. Per completare questa attività, seguire questi passaggi:

1. Passare a /usr/local/lib/python2.7/site-packages
2. Elencare i pacchetti creati dall'utente root: `ls -l | grep root`
3. Disinstallare i pacchetti elencati nel passaggio 2: `sudo rm -rf {package name}`
4. Reinstallare Python.

## <a name="azure-iot-hub-issues"></a>Problemi di Hub IoT di Azure
Se è stato effettuato il provisioning dell'hub IoT di Azure con l'interfaccia della riga di comando di Azure ed è necessario uno strumento per gestire i dispositivi connessi all'hub IoT, provare gli strumenti seguenti.

### <a name="device-explorer"></a>Esplora dispositivi
Lo strumento [Esplora dispositivi](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) viene eseguito nel computer locale di Windows e si connette all'hub IoT in Azure. Comunica con i seguenti [endpoint dell'hub IoT](iot-hub-devguide.md):

* *Gestione delle identità dispositivo* per il provisioning e la gestione dei dispositivi registrati con l'hub IoT.
* *Ricezione da dispositivo a cloud* per il monitoraggio dei messaggi inviati dal dispositivo all'hub IoT.
* *Invio da cloud a dispositivo* per l'invio dei messaggi dall'hub IoT ai dispositivi.

Configurare la stringa di connessione dell'hub IoT in questo strumento per usarne tutte le funzionalità.

### <a name="iothub-explorer"></a>iothub-explorer
[iothub-explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) è uno strumento di esempio dell'interfaccia della riga di comando multipiattaforma che consente di gestire i dispositivi. È possibile usare questo strumento per gestire i dispositivi nel registro delle identità, monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo.

Per installare la versione più recente, non definitiva, dello strumento iothub-explorer, eseguire questo comando nell'ambiente della riga di comando:

```bash
npm install -g iothub-explorer@latest
```

Per ottenere altre informazioni su tutti i comandi di iothub explorer e sui relativi parametri, è possibile usare il comando seguente:

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Portale di Azure
Un'interfaccia della riga di comando completa consente di creare e gestire tutte le risorse di Azure. Può anche essere utile usare il [portale di Azure](../azure-portal-overview.md) per il provisioning, la gestione e il debug delle risorse di Azure.

## <a name="azure-storage-issues"></a>Problemi di Archiviazione di Azure
[Microsoft Azure Storage Explorer (anteprima)](http://storageexplorer.com) è un'app autonoma di Microsoft che consente di utilizzare i dati di Archiviazione di Azure in Windows, macOS e Linux. Questo strumento permette di connettersi alla tabella e visualizzarne i dati. È possibile usare questo strumento per risolvere i problemi di Archiviazione di Azure.




<!--HONumber=Dec16_HO1-->


