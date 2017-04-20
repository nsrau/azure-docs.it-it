---
title: 'Connettere Intel Edison (Node) ad Azure IoT: lezione 4: Risoluzione dei problemi | Documentazione Microsoft'
description: Pagina sulla risoluzione dei problemi per Node.js in Intel Edison
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: risoluzione dei problemi in arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: f11c5521-8a36-44c0-baad-f5ec26ab4618
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: d77ee0be35a3f4b38c50cf9322124e4dfbba6ddc
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>Risoluzione dei problemi
## <a name="hardware-issues"></a>Problemi hardware
Per informazioni sulla risoluzione dei problemi più comuni di Intel Edison, vedere la [pagina ufficiale sulla risoluzione dei problemi](https://software.intel.com/en-us/node/637974) relativa.

## <a name="nodejs-package-issues"></a>Problemi del pacchetto Node.js
### <a name="no-response-during-gulp-tasks"></a>Nessuna risposta durante le attività gulp
Se si verificano problemi durante l'esecuzione di attività gulp, è possibile aggiungere l'opzione `--verbose` per il debug. Provare a terminare le attività gulp correnti con `Ctrl + C` e quindi eseguire questo comando nella finestra della console per visualizzare i messaggi di debug. È possibile visualizzare i messaggi di errore dettagliati nell'output della console. 

```bash
gulp --verbose
```

### <a name="npm-issues"></a>Problemi di NPM
Provare ad aggiornare il pacchetto NPM con il comando seguente:

```bash
npm install -g npm
```

Se il problema persiste, aggiungere commenti alla fine di questo articolo o definire un problema di GitHub nel [repository degli esempi][sample-repository].

## <a name="remote-debugging"></a>Debug remoto

### <a name="run-the-sample-application-in-debug-mode"></a>Eseguire l'applicazione di esempio in modalità di debug

```bash
gulp run --debug
```

Quando il motore di debug è pronto, è possibile visualizzare ```Debugger listening on port 5858``` nell'output della console.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Configurare Visual Studio Code per la connessione al dispositivo remoto

Aprire il pannello **Debug** sul lato sinistro.

Fare clic sul pulsante verde **Avvia debug** (F5). In Visual Studio Code viene aperto un file **launch.json** che è necessario aggiornare.

Aggiornare il file **launch.json** con il contenuto seguente e sostituire `[device hostname or IP address]` con l'indirizzo IP o il nome host del dispositivo effettivo.  

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

![Configurazione del debug remoto](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Collegarsi all'applicazione remota

Fare clic sul pulsante verde **Avvia debug** (F5) per avviare il debug.

Leggere [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) per altre informazioni sul debugger.

![Debug remoto interattivo](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problemi dell'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure è una versione di anteprima. Per cercare le soluzioni ai problemi, vedere la [guida all'installazione della versione di anteprima](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md). Quando i comandi non funzionano come previsto, provare ad aggiornare l'interfaccia della riga di comando di Azure alla versione più recente.

Se si rilevano bug con lo strumento, segnalare un [problema](https://github.com/Azure/azure-cli/issues) nella sezione **Issues** (Problemi) del repository GitHub.

Per risolvere più facilmente i problemi comuni, controllare il file [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

Se viene visualizzato un messaggio simile a "Impossibile trovare una versione che soddisfi il requisito", eseguire questo comando per aggiornare pip all'ultima versione.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problemi di installazione di Python
### <a name="legacy-installation-issues-macos"></a>Problemi di installazione di pacchetti legacy (macOS)
Durante l'installazione di **pip**, viene generato un errore di autorizzazione quando vengono installati pacchetti legacy con autorizzazioni **su**. Questa situazione si verifica perché l'installazione precedente di Python con brew (macOS) non è stata disinstallata completamente. Alcuni pacchetti **pip** di un'installazione precedente sono stati creati dall'utente root e questo causa l'errore di autorizzazione. La soluzione consiste nel rimuovere i pacchetti installati dall'utente root. Per completare questa attività, seguire questi passaggi:

1. Passare a /usr/local/lib/python2.7/site-packages
2. Elencare i pacchetti creati dall'utente root: `ls -l | grep root`
3. Disinstallare i pacchetti elencati nel passaggio 2: `sudo rm -rf {package name}`
4. Reinstallare Python.

## <a name="azure-iot-hub-issues"></a>Problemi di Hub IoT di Azure
Se è stato completato il provisioning dell'hub IoT di Azure con `azure-cli` ed è necessario uno strumento per gestire i dispositivi che si connettono all'hub IoT, provare gli strumenti seguenti:

### <a name="device-explorer"></a>Esplora dispositivi
[Esplora dispositivi](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) viene eseguito sul computer locale di Windows e si connette all'hub IoT in Azure. Comunica con i seguenti [endpoint dell'hub IoT](iot-hub-devguide.md):

- _Gestione delle identità dispositivo_ per il provisioning e la gestione dei dispositivi registrati con l'hub IoT.
- _Ricezione da dispositivo a cloud_ per il monitoraggio dei messaggi inviati dal dispositivo all'hub IoT.
- _Invio da cloud a dispositivo_ per l'invio dei messaggi dall'hub IoT ai dispositivi.

Configurare il valore di `IoT hub connection string` all'interno di questo strumento per usarne tutte le funzionalità.

### <a name="iot-hub-explorer"></a>IoT Hub Explorer
[IoT Hub Explorer](https://github.com/Azure/iothub-explorer) è uno strumento di esempio dell'interfaccia della riga di comando multipiattaforma per gestire i dispositivi client. È possibile usare questo strumento per gestire i dispositivi nel registro di identità, monitorare i messaggi da dispositivo a cloud e inviare comandi da cloud a dispositivo.

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
[Microsoft Azure Storage Explorer (anteprima)](http://storageexplorer.com) è un'app autonoma di Microsoft che permette di usare i dati di [Archiviazione di Azure](https://azure.microsoft.com/en-us/services/storage/) in Windows, macOS e Linux. Questo strumento permette di connettersi alla tabella e visualizzarne i dati. È possibile usare questo strumento per risolvere i problemi di Archiviazione di Azure.

## <a name="next-steps"></a>Passaggi successivi
Questa pagina include solo i problemi più comuni del kit per Intel Edison. Per segnalare altri problemi da risolvere, è possibile lasciare commenti nella parte inferiore della pagina.

Tornare a [Introduzione a Intel Edison (Node.js)](iot-hub-intel-edison-kit-node-get-started.md)

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-node-edison-getting-started
