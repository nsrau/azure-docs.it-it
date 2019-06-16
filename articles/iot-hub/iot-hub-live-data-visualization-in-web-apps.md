---
title: Visualizzazione dei dati in tempo reale dei dati del sensore dall'hub IoT di Azure in un'app web | Microsoft Docs
description: Usare un'applicazione web per visualizzare i dati di temperatura e umidità raccolti da un sensore e inviati all'hub Iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476023"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure in un'applicazione web

![Diagramma end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questa esercitazione descrive come visualizzare i dati di sensori in tempo reale che ricevuti dall'hub IoT con un'app web Node. js in esecuzione nel computer locale. Dopo aver eseguito l'app web in locale, è facoltativamente possibile seguire i passaggi per ospitare l'app web nel servizio App di Azure. Se si desidera provare a visualizzare i dati nell'hub IoT con Power BI, vedere [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Operazioni da fare

* Aggiungere un gruppo di consumer all'hub IoT che l'applicazione web userà per leggere i dati del sensore
* Scaricare il codice dell'app web da GitHub
* Esaminare il codice dell'app web
* Configurare le variabili di ambiente per contenere gli elementi necessari per l'app web nell'IoT Hub
* Eseguire l'app web nel computer di sviluppo
* Aprire una pagina web per visualizzare dati di temperatura e umidità in tempo reale dall'hub IoT
* (Facoltativo) Usare CLI di Azure per ospitare l'app web in servizio App di Azure

## <a name="what-you-need"></a>Elementi necessari

* Completare la [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) esercitazione, oppure una delle esercitazioni dispositivo; ad esempio [Raspberry Pi con Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi descrivono i requisiti seguenti:

  * Una sottoscrizione di Azure attiva
  * Un hub IoT nella sottoscrizione
  * Un'applicazione client che invia messaggi all'hub IoT

* [Scaricare Git](https://www.git-scm.com/downloads)

* Le procedure descritte in questo articolo presuppongono un computer di sviluppo di Windows; Tuttavia, è possibile eseguire facilmente questi passaggi in un sistema Linux nella shell preferita.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

[Gruppi di consumer](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) forniscono visualizzazioni indipendenti nel flusso di eventi che consentono le App e servizi di Azure per utilizzare in modo indipendente i dati dall'endpoint di Hub eventi stesso. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub IoT che l'app web userà per leggere i dati.

Eseguire il comando seguente per aggiungere un gruppo di consumer nell'endpoint predefinito dell'hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Si noti il nome scelto, sarà necessario più avanti in questa esercitazione.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Ottenere una stringa di connessione del servizio per l'hub IoT

Gli hub IoT vengono creati con diversi criteri di accesso predefinito. Un criterio di questo tipo è il **servizio** criteri, che forniscono autorizzazioni sufficienti per un servizio leggere e scrivere gli endpoint dell'hub IoT. Eseguire il comando seguente per ottenere una stringa di connessione per l'hub IoT che è conforme ai criteri di servizio:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

La stringa di connessione dovrebbe essere simile al seguente:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Annotare la stringa di connessione del servizio, sarà necessario più avanti in questa esercitazione.

## <a name="download-the-web-app-from-github"></a>Scaricare l'app web da GitHub

Aprire una finestra di comando e immettere i comandi seguenti per scaricare l'esempio da GitHub e passare alla directory di esempio:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Esaminare il codice dell'app web

Dalla directory web-apps-node-iot-hub-data-visualization, aprire l'app web nell'editor preferito. Di seguito mostra la struttura dei file visualizzata in Visual Studio Code:

![Struttura file dell'app Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Si consiglia di esaminare i file seguenti:

* **Server. js** è uno script sul lato servizio che inizializza la classe wrapper di Hub eventi e il web socket. Fornisce un callback per la classe wrapper di Hub eventi che la classe viene utilizzata per trasmettere i messaggi in ingresso per il web socket.

* **Eventi-hub-reader.js** è uno script sul lato servizio che si connette all'endpoint predefinito dell'hub IoT usando il gruppo di consumer e stringa di connessione specificata. Estrae l'ID dispositivo ed EnqueuedTimeUtc dai metadati nei messaggi in arrivo e quindi inoltra il messaggio utilizzando il metodo di callback registrato dal server. js.

* **Grafico-device-js** è uno script lato client che è in ascolto nel web socket, tiene traccia di ogni DeviceId e archivia gli ultimi 50 punti di dati in ingresso per ogni dispositivo. I dati del dispositivo selezionato viene quindi associato all'oggetto grafico.

* **Index. HTML** gestisce il layout dell'interfaccia utente per la pagina web e fa riferimento a tutti gli script necessari per la logica lato client.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurare le variabili di ambiente per l'app web

Per leggere dati dall'hub IoT, l'app web necessita di stringa di connessione dell'hub IoT e il nome del gruppo di consumer che dovrebbe essere visualizzato tramite. Queste stringhe Ottiene dall'ambiente di processo nelle righe seguenti nel server. js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Impostare le variabili di ambiente nella finestra di comando con i comandi seguenti. Sostituire i valori segnaposto con la stringa di connessione del servizio per l'hub IoT e il nome del gruppo di consumer creato in precedenza. Non racchiudere le stringhe.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Eseguire l'app Web

1. Assicurarsi che il dispositivo è in esecuzione e l'invio dei dati.

2. Nella finestra di comando, eseguire le righe seguenti per scaricare e installare pacchetti con riferimenti e avviare il sito Web:

   ```cmd
   npm install
   npm start
   ```

3. Verrà visualizzato l'output che indica che l'app web ha eseguito la connessione all'hub IoT ed è in ascolto sulla porta 3000 nella console:

   ![In console all'app Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Aprire una pagina web per visualizzare i dati dall'hub IoT

Aprire un browser per `http://localhost:3000`.

Nel **selezionare un dispositivo** elencare, selezionare il dispositivo per vedere un tracciato in esecuzione le ultime 50 temperatura e umidità punti dati inviati dal dispositivo all'hub IoT.

![Pagina dell'app Web che mostra temperatura e umidità in tempo reale](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

È visualizzato anche l'output nella console che mostra i messaggi che sta trasmettendo l'app web per il browser client:  

![App Web di trasmettere l'output nella console di](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hosting dell'app web nel servizio App

Il [funzionalità App Web del servizio App di Azure](https://docs.microsoft.com/azure/app-service/overview) offre una piattaforma come servizio (PAAS) per l'hosting di applicazioni web. Le applicazioni Web ospitate nel servizio App di Azure possono trarre vantaggio da potenti funzionalità di Azure, ad esempio maggiore sicurezza, bilanciamento del carico, e la scalabilità come nonché di partner DevOps e Azure soluzioni, ad esempio la distribuzione continua, Gestione pacchetti e così via. Servizio App di Azure supporta le applicazioni web sviluppate in molti linguaggi diffusi e distribuiti nell'infrastruttura di Windows o Linux.

In questa sezione eseguire il provisioning di un'app web nel servizio App e distribuire il codice usando i comandi CLI di Azure. È possibile trovare i dettagli dei comandi usati nel [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) documentazione. Prima di iniziare, assicurarsi aver completato i passaggi necessari per [aggiungere un gruppo di risorse per l'hub IoT](#add-a-consumer-group-to-your-iot-hub), [ottenere una stringa di connessione del servizio per l'hub IoT](#get-a-service-connection-string-for-your-iot-hub), e [scaricare l'app web da GitHub](#download-the-web-app-from-github).

1. Un' [piano di servizio App](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definisce un set di risorse di calcolo per un'app ospitata in servizio App di eseguire. In questa esercitazione, usiamo il livello Developer o gratuito per ospitare l'app web. Con il livello gratuito, l'app web viene eseguita su risorse di Windows condivise con altre app di servizio App, incluse le app di altri clienti. Azure inoltre offre che piani di servizio App per la distribuzione di App web in Linux le risorse di calcolo. È possibile ignorare questo passaggio se si dispone già di un piano di servizio App che si desidera utilizzare.

   Per creare un piano di servizio App usando il livello gratuito di Windows, eseguire il comando seguente. Usare lo stesso gruppo di risorse in che dell'hub IoT è. Il nome del piano di servizio può contenere lettere e lettere minuscole, numeri e trattini.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. A questo punto eseguire il provisioning di un'app web nel piano di servizio App. Il `--deployment-local-git` parametro consente il codice dell'app web da caricare e distribuito da un repository Git nel computer locale. Il nome dell'app web deve essere globalmente univoco e può contenere lettere e lettere minuscole, numeri e trattini.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. A questo punto aggiungere le impostazioni dell'applicazione per le variabili di ambiente che specificano la stringa di connessione dell'hub IoT e il gruppo di consumer dell'hub eventi. Le singole impostazioni sono delimitato nello spazio di `-settings` parametro. Usare la stringa di connessione del servizio per l'hub IoT e il gruppo di consumer creato in precedenza in questa esercitazione. Non racchiudere i valori.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Abilitare i WebSocket del protocollo per l'app web e impostare l'app web per la ricezione HTTPS richiede solo (le richieste HTTP vengono reindirizzate a HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Per distribuire il codice nel servizio App, si userà il [credenziali di distribuzione a livello di utente](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Le credenziali di distribuzione a livello di utente sono diverse dalle credenziali di Azure e vengono utilizzate per le distribuzioni di FTP in un'app web e Git locale. Una volta impostato, risultano validi in tutte le app di servizio App in tutte le sottoscrizioni nell'account di Azure. Se è stato impostato in precedenza le credenziali di distribuzione a livello di utente, è possibile utilizzarli.

   Se non è stata impostata in precedenza le credenziali di distribuzione a livello di utente o non si ricorda la password, eseguire il comando seguente. Il nome utente della distribuzione deve essere univoco all'interno di Azure e non deve contenere il ' @' per l'istanza Git locale esegue il push dei simboli. Quando richiesto, immettere e confermare la nuova password. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Ottenere l'URL Git da usare per effettuare il push del codice fino a servizio App.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Aggiungere un server remoto per il clone che fa riferimento il repository Git per l'app web nel servizio App. Per la \<URL clone Git\>, usare l'URL restituito nel passaggio precedente. Eseguire il comando seguente nella finestra di comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Per distribuire il codice nel servizio App, immettere il comando seguente nella finestra di comando. Se viene chiesto di immettere le credenziali, immettere le credenziali di distribuzione a livello di utente che creato nel passaggio 5. Assicurarsi che si esegue il push nel ramo master del servizio App remoto.

    ```cmd
    git push webapp master:master
    ```

9. Lo stato di avanzamento della distribuzione verrà aggiornata nella finestra di comando. Una corretta distribuzione terminerà con righe simili all'output seguente:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Eseguire il comando seguente per eseguire una query lo stato dell'app web e assicurarsi che sia in esecuzione:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Andare a `https://<your web app name>.azurewebsites.net` in un browser. Una pagina web simile a quello che si è visto durante l'esecuzione dell'app web in locale vengono visualizzati. Supponendo che il dispositivo è in esecuzione e l'invio di dati, si dovrebbe vedere un tracciato in esecuzione delle 50 più recenti su temperatura e umidità letture inviati dal dispositivo.

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si riscontra problemi con questo esempio, provare i passaggi nelle sezioni seguenti. Se continuano a verificarsi problemi, inviare commenti e suggerimenti nella parte inferiore di questo argomento.

### <a name="client-issues"></a>Problemi del client

* Se un dispositivo non viene visualizzato nell'elenco o nessun grafico da disegnare, assicurarsi che il codice del dispositivo è in esecuzione nel dispositivo.

* Nel browser, aprire gli strumenti di sviluppo (in molti browser il F12 chiave verrà aperta) e trovare la console. Cercare eventuali avvisi o errori stampati non esiste.

* È possibile eseguire il debug di script sul lato client in /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemi di sito Web locale

* Osservare l'output nella finestra di cui è stata avviata nodo per l'output di console.

* Il debug del codice di server, in particolare /scripts/event-hub-reader.js e server. js.

### <a name="azure-app-service-issues"></a>Problemi di servizio App di Azure

* Nel portale di Azure passare all'App web. Sotto **Monitoring** nel riquadro sinistro, selezionare **log del servizio App**. Attivare **registrazione applicazioni (File System)** , impostare **livello** a errore e quindi selezionare **Salva**. Quindi aprire **flusso di registrazione** (sotto **monitoraggio**).

* Dall'app web nel portale di Azure, sotto **strumenti di sviluppo** selezionate **Console** e convalidare le versioni di node e npm con `node -v` e `npm -v`.

* Se viene visualizzato un errore relativo a non trova un pacchetto, si potrebbero avere eseguito i passaggi descritti non in ordine. Quando il sito viene distribuito (con `git push`) viene eseguito il servizio app `npm install`, che viene eseguito in base alla versione corrente del nodo è configurato. Se questa situazione è cambiata nella configurazione in un secondo momento, è necessario apportare una modifica non significativa per il codice ed eseguire nuovamente il push.

## <a name="next-steps"></a>Passaggi successivi

L'app Web è stata usata correttamente per visualizzare i dati del sensore in tempo reale dall'hub IoT.

Per un altro modo per visualizzare i dati dall'IoT Hub di Azure, vedere [usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
