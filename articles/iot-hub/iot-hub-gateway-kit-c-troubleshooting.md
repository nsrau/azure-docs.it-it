---
title: Risoluzione dei problemi del gateway IoT di Azure | Documentazione Microsoft
description: Pagina sulla risoluzione dei problemi del gateway Intel NUC
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: Problemi di IoT, Internet delle cose
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 44b61dbdad51e3d6b5505477c97de75c8fc5a1a4
ms.openlocfilehash: d704d21a89a8fde0128063430476c14317243e33


---
# <a name="troubleshooting"></a>Risoluzione dei problemi

## <a name="hardware-issues"></a>Problemi hardware

### <a name="ti-sensortag-cannot-be-connected"></a>Impossibile connettere SensorTag di Texas Instruments

Per risolvere i problemi di connettività di SensorTag, usare l'[app SensorTag](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Problema relativo a Intel NUC

Per risolvere i problemi di avvio, vedere l'articolo relativo alla [risoluzione dei problemi di mancato avvio su Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Per risolvere i problemi del sistema operativo, vedere l'articolo relativo alla [risoluzione dei problemi del sistema operativo su Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Per risolvere altri problemi, vedere l'articolo relativo ai [codici dei lampeggiamenti e dei segnali acustici per Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html).

## <a name="nodejs-package-issues"></a>Problemi del pacchetto Node.js

### <a name="no-response-during-gulp-tasks"></a>Nessuna risposta durante le attività gulp

Se si verificano problemi durante l'esecuzione di attività gulp, è possibile aggiungere l'opzione `--verbose` per il debug. Provare a terminare le attività gulp correnti con `Ctrl + C` e quindi eseguire questo comando nella finestra della console per visualizzare i messaggi di debug. È possibile visualizzare i messaggi di errore dettagliati nell'output della console.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemi di individuazione dei dispositivi

Per risolvere più facilmente i problemi comuni relativi al comando `discover-sensortag`, vedere la [pagina wiki](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>Problemi di npm

Provare ad aggiornare il pacchetto npm eseguendo questo comando:

```bash
npm install -g npm
```

Se il problema persiste, aggiungere commenti alla fine di questo articolo o definire un problema di GitHub nel [repository degli esempi](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Debug remoto
> Le istruzioni di seguito consentono di eseguire il debug degli script node.js usati in questa esercitazione.
### <a name="run-the-sample-application-in-debug-mode"></a>Eseguire l'applicazione di esempio in modalità di debug

Eseguire l'applicazione di esempio in modalità di debug eseguendo questo comando:

```bash
gulp run --debug
```

Quando il motore di debug è pronto, nell'output della console dovrebbe essere visibile `Debugger listening on port 5858`.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configurare Visual Studio Code per la connessione al dispositivo remoto

1. Aprire il pannello **Debug** sul lato sinistro.
2. Fare clic sul pulsante verde **Avvia debug** (F5). In Visual Studio Code viene aperto un file `launch.json`.
3. Aggiornare il file `launch.json` con il contenuto seguente. Sostituire `[device hostname or IP address]` con il nome host o l'indirizzo IP effettivo del dispositivo.

   ``` json
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
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Configurazione del debug remoto](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Collegarsi all'applicazione remota

Fare clic sul pulsante verde **Avvia debug** (F5) per avviare il debug.

Leggere [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) per altre informazioni sul debugger.

![Debug dell'esempio BLE](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Problemi dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è una versione di anteprima. Per cercare le soluzioni ai problemi, vedere la [guida all'installazione della versione di anteprima](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Se si rilevano bug con lo strumento, segnalare un [problema](https://github.com/Azure/azure-cli/issues) nella sezione **Issues** (Problemi) del repository GitHub.

Per risolvere più facilmente i problemi comuni, vedere il file [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

Se viene visualizzato un messaggio simile a "Impossibile trovare una versione che soddisfi il requisito", eseguire questo comando per aggiornare pip all'ultima versione.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problemi di installazione di Python

### <a name="legacy-installation-issues-macos"></a>Problemi di installazione di pacchetti legacy (macOS)

Durante l'installazione di pip, viene generato un errore di autorizzazione quando vengono installati pacchetti legacy con autorizzazioni **su**. Questa situazione si verifica perché l'installazione precedente di Python con brew (macOS) non è stata disinstallata completamente. Alcuni pacchetti pip di un'installazione precedente sono stati creati dall'utente root e questo causa l'errore di autorizzazione. La soluzione consiste nel rimuovere i pacchetti installati dall'utente root. Per completare questa attività, seguire questi passaggi:

1. Passare a `/usr/local/lib/python2.7/site-packages`.
2. Elencare i pacchetti creati dall'utente root: `ls -l | grep root`
3. Disinstallare i pacchetti elencati nel passaggio 2: `sudo rm -rf {package name}`
4. Reinstallare Python.

## <a name="azure-iot-hub-issues"></a>Problemi di Hub IoT di Azure

Se è stato completato il provisioning dell'hub IoT di Azure con l'interfaccia della riga di comando di Azure ed è necessario uno strumento per gestire i dispositivi che si connettono all'hub IoT, provare gli strumenti seguenti.

### <a name="device-explorer"></a>Esplora dispositivi

[Esplora dispositivi](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) viene eseguito sul computer locale di Windows e si connette all'hub IoT in Azure. Comunica con i seguenti [endpoint dell'hub IoT](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- Gestione delle identità dei dispositivi per il provisioning e la gestione dei dispositivi registrati nell'hub IoT.
- Ricezione da dispositivo a cloud per il monitoraggio dei messaggi inviati dal dispositivo all'hub IoT.
- Invio da cloud a dispositivo per l'invio dei messaggi dall'hub IoT ai dispositivi.

Configurare la stringa di connessione dell'hub IoT in questo strumento per usarne tutte le funzionalità.

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) è uno strumento di esempio dell'interfaccia della riga di comando multipiattaforma che permette di gestire i dispositivi client. È possibile usare questo strumento per gestire i dispositivi nel registro di identità, monitorare i messaggi da dispositivo a cloud e inviare comandi da cloud a dispositivo.

Per installare l'ultima versione, non definitiva, dello strumento iothub-explorer, eseguire questo comando:

```bash
npm install -g iothub-explorer@latest
```

Per ottenere altre informazioni su tutti i comandi di iothub explorer e sui relativi parametri, eseguire questo comando:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Portale di Azure

Un'interfaccia della riga di comando completa consente di creare e gestire tutte le risorse di Azure. Può anche essere utile usare il [portale di Azure](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) per il provisioning, la gestione e il debug delle risorse di Azure.

## <a name="azure-storage-issues"></a>Problemi di Archiviazione di Azure

[Microsoft Azure Storage Explorer (anteprima)](http://storageexplorer.com/) è un'app autonoma di Microsoft che consente di utilizzare i dati di Archiviazione di Azure in Windows, macOS e Linux. Questo strumento permette di connettersi alla tabella e visualizzarne i dati. È possibile usare questo strumento per risolvere i problemi di Archiviazione di Azure.



<!--HONumber=Dec16_HO3-->


