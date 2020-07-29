---
title: Visualizzazione in tempo reale dei dati dell'hub IoT in un'app Web
description: Usare un'applicazione Web per visualizzare i dati di temperatura e umidità raccolti da un sensore e inviati all'hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 6a8f39ae5d73bade2c86a7e15efe75956c2aed24
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327566"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizzare i dati di un sensore in tempo reale dall'hub IoT di Azure in un'applicazione Web

![Diagramma end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questa esercitazione si imparerà a visualizzare i dati del sensore in tempo reale che l'hub IoT riceve con un'app Web node.js in esecuzione sul computer locale. Dopo aver eseguito l'app Web in locale, è possibile seguire la procedura per ospitare l'app Web nel servizio app di Azure. Se si desidera provare a visualizzare i dati nell'hub IoT con Power BI, vedere [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Operazioni da fare

* Aggiungere un gruppo di consumer all'hub IoT che l'applicazione Web userà per leggere i dati dei sensori
* Scaricare il codice dell'app Web da GitHub
* Esaminare il codice dell'app Web
* Configurare le variabili di ambiente in modo che contengano gli artefatti dell'hub IoT necessari per l'app Web
* Eseguire l'app Web sul computer di sviluppo
* Aprire una pagina Web per visualizzare i dati di temperatura e umidità in tempo reale dall'hub IoT
* (Opzionale) Usare l'interfaccia della riga di comando di Azure per ospitare l'app Web nel servizio app di Azure

## <a name="what-you-need"></a>Elementi necessari

* Completare l'esercitazione del [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sui dispositivi; ad esempio, [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Sono previsti i requisiti seguenti:

  * Una sottoscrizione di Azure attiva
  * Un hub IoT nella sottoscrizione
  * Un'applicazione client che invia messaggi all'hub IoT

* [Scaricare Git](https://www.git-scm.com/downloads)

* I passaggi descritti in questo articolo presuppongono un computer di sviluppo Windows, tuttavia è possibile eseguire facilmente questi passaggi in un sistema Linux nella shell preferita.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I [gruppi di consumer](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) offrono visualizzazioni indipendenti nel flusso di eventi che consentono alle app e ai servizi di Azure di usare i dati in modo indipendente dallo stesso endpoint dell'hub eventi. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub IoT che l'app Web userà per leggere i dati.

Eseguire il comando seguente per aggiungere un gruppo di consumer all'endpoint predefinito dell'hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Annotare il nome scelto, che sarà necessario più avanti in questa esercitazione.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Ottenere una stringa di connessione del servizio per l'hub IoT

Gli hub IoT vengono creati con diversi criteri di accesso predefiniti. Uno di questi è il criterio del **servizio**, che offre a un servizio autorizzazioni sufficienti per la lettura e la scrittura degli endpoint dell'hub IoT. Eseguire il comando seguente per ottenere una stringa di connessione per l'hub IoT che rispetti i criteri del servizio:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

La stringa di connessione avrà un aspetto simile al seguente:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Prendere nota della stringa di connessione del servizio, che sarà necessaria più avanti in questa esercitazione.

## <a name="download-the-web-app-from-github"></a>Scaricare l'app Web da GitHub

Aprire una finestra di comando e immettere i comandi seguenti per scaricare l'esempio da GitHub e passare alla directory di esempio:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Esaminare il codice dell'app Web

Dalla directory web-app-nodo-iot-hub-dati-visualizzazione aprire l'app Web nell'editor preferito. Di seguito viene illustrata la struttura di file visualizzata in VS Code:

![Struttura dei file dell'app Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Esaminare i file seguenti:

* **Server.js** è uno script lato servizio che inizializza il Web socket e la classe wrapper dell'hub eventi. Offre un callback alla classe wrapper dell'hub eventi usato dalla classe per trasmettere i messaggi in ingresso al socket Web.

* **Event-hub-reader.js** è uno script lato servizio che si connette all'endpoint predefinito dell'hub IoT usando la stringa di connessione e il gruppo di consumer specificati. Esso estrae DeviceId ed EnqueuedTimeUtc dai metadati dei messaggi in ingresso e quindi inoltra il messaggio usando il metodo di callback registrato da server.js.

* **Chart-device-data.js** è uno script lato client che è in ascolto sul Web socket; esso tiene traccia di ogni DeviceId e archivia gli ultimi 50 punti di dati in arrivo per ogni dispositivo. Associa quindi i dati del dispositivo selezionati all'oggetto grafico.

* **Index.html** gestisce il layout dell'interfaccia utente per la pagina Web e fa riferimento agli script necessari per la logica lato client.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurare le variabili di ambiente per l'app Web

Per leggere i dati dall'hub IoT, l'app Web necessita della stringa di connessione dell'hub IoT e del nome del gruppo di consumer da leggere. Ottiene queste stringhe dall'ambiente del processo nelle righe seguenti in server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Impostare le variabili di ambiente nella finestra di comando con i comandi seguenti. Sostituire i valori segnaposto con la stringa di connessione del servizio per l'hub IoT e il nome del gruppo di consumer creato in precedenza. Non racchiudere tra virgolette le stringhe.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Eseguire l'app Web

1. Verificare che il dispositivo sia in esecuzione e che invii i dati.

2. Nella finestra di comando eseguire le righe seguenti per scaricare e installare i pacchetti a cui si fa riferimento, quindi avviare il sito Web:

   ```cmd
   npm install
   npm start
   ```

3. Nella console verrà visualizzato l'output che indica che l'app Web è stata connessa correttamente all'hub IoT e che è in ascolto sulla porta 3000:

   ![App Web avviata nella console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Aprire una pagina Web per visualizzare i dati dall'hub IoT

Aprire una finestra in `http://localhost:3000`.

Nell'elenco **Seleziona un dispositivo**, selezionare il dispositivo per visualizzare un tracciato di esecuzione degli ultimi 50 punti dati di temperatura e umidità inviati dal dispositivo all'hub IoT.

![Pagina dell'app Web che mostra temperatura e umidità in tempo reale](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Si dovrebbe visualizzare anche l'output nella console che mostra i messaggi trasmessi dall'app Web al client browser:  

![Output della trasmissione dell'app Web nella console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Ospitare l'app Web nel Servizio app di Azure

La [funzionalità app Web del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/overview) offre una piattaforma distribuita come servizio (PAAS) per l'hosting di applicazioni Web. Le applicazioni Web ospitate nel Servizio app di Azure possono trarre vantaggio dalle potenti funzionalità di Azure, ad esempio sicurezza aggiuntiva, bilanciamento del carico e scalabilità, nonché soluzioni DevOps di Azure e partner come la distribuzione continua, la gestione dei pacchetti e così via. Il Servizio app di Azure supporta applicazioni Web sviluppate in molti linguaggi comuni e distribuite in un'infrastruttura Windows o Linux.

In questa sezione si esegue il provisioning di un'app Web in Servizio app e si distribuisce il codice usando i comandi dell'interfaccia della riga di comando di Azure. Per informazioni dettagliate sui comandi usati, vedere la documentazione di [AZ webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest). Prima di iniziare, assicurarsi di aver completato i passaggi per [aggiungere un gruppo di risorse all'hub IoT](#add-a-consumer-group-to-your-iot-hub), [ottenere una stringa di connessione del servizio per l'hub IoT](#get-a-service-connection-string-for-your-iot-hub) e [scaricare l'app Web da GitHub](#download-the-web-app-from-github).

1. Un [piano di servizio app](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definisce un set di risorse di calcolo per l'esecuzione di un'app ospitata in Servizio app. In questa esercitazione per ospitare l'app Web viene usato il livello Sviluppatore/gratuito. Con il livello gratuito, l'app Web viene eseguita su risorse Windows condivise con altre app del servizio app, incluse le app di altri clienti. Azure offre anche piani di servizio app per distribuire app Web in risorse di calcolo Linux. Se si dispone già di un piano di servizio app che si desidera usare, è possibile ignorare questo passaggio.

   Per creare un piano di servizio app usando il livello gratuito di Windows, eseguire il comando seguente. Usare lo stesso gruppo di risorse usato dall'hub IoT dell'utente. Il nome del piano di servizio può contenere lettere maiuscole e minuscole, numeri e trattini.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Eseguire ora il provisioning di un'app Web nel piano di servizio app. Il parametro `--deployment-local-git` consente di caricare e distribuire il codice dell'app Web da un repository Git nel computer locale. Il nome dell'app Web deve essere globalmente univoco e può contenere lettere maiuscole e minuscole, numeri e trattini. Assicurarsi di specificare la versione del nodo 10.6 o successiva per il parametro `--runtime`, a seconda della versione del runtime Node.js in uso. È possibile usare il comando `az webapp list-runtimes` per ottenere un elenco di runtime supportati.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Aggiungere ora le impostazioni applicazione per le variabili di ambiente che specificano la stringa di connessione dell'hub IoT e il gruppo di consumer dell'hub eventi. Le singole impostazioni sono delimitate da spazi nel parametro `-settings`. Usare la stringa di connessione del servizio per l'hub IoT e il gruppo di consumer creato in precedenza in questa esercitazione. Non racchiudere tra virgolette i valori.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Abilitare il protocollo Web Sockets per l'app Web e impostare l'app Web affinché riceva solo richieste HTTPS (le richieste HTTP vengono reindirizzate a HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Per distribuire il codice nel servizio app, si useranno le [credenziali di distribuzione livello utente](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Le credenziali di distribuzione livello utente sono diverse dalle credenziali di Azure e vengono usate per le distribuzioni FTP e locali Git in un'app Web. Una volta impostate, sono valide in tutte le app del servizio app in tutte le sottoscrizioni dell'account Azure. Se in precedenza sono state impostate delle credenziali di distribuzione livello utente, è possibile usare tali credenziali.

   Se in precedenza non sono state impostate delle credenziali di distribuzione livello utente o non è possibile ricordare la password, eseguire il comando seguente. Il nome utente della distribuzione deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo "@". Quando viene richiesto, immettere e confermare la nuova password. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Ottenere l'URL Git da usare per eseguire il push del codice nel servizio app.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Aggiungere un oggetto remoto al clone che fa riferimento al repository Git per l'app Web nel servizio app. Per \<Git clone URL\> , usare l'URL restituito nel passaggio precedente. Eseguire il comando seguente nella finestra di comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Per distribuire il codice nel servizio app, immettere il comando seguente nella finestra di comando. Se vengono richieste le credenziali, immettere le credenziali di distribuzione livello utente create nel passaggio 5. Assicurarsi di effettuare il push nel ramo master del servizio app remoto.

    ```cmd
    git push webapp master:master
    ```

9. Lo stato di avanzamento della distribuzione viene aggiornato nella finestra di comando. Se la distribuzione ha avuto esito positivo, verrà visualizzato un output simile al seguente:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Eseguire il comando seguente per eseguire una query sullo stato dell'app Web e verificare che sia in esecuzione:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Andare a `https://<your web app name>.azurewebsites.net` in un browser. Una pagina Web simile a quella visualizzata quando è stata eseguita l'app Web in locale. Supponendo che il dispositivo sia in esecuzione e invii dati, dovrebbe essere visualizzato un tracciato in esecuzione delle 50 più recenti letture di temperatura e umidità inviate dal dispositivo.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi con questo esempio, provare a eseguire i passaggi nelle sezioni riportate di seguito. Se si verificano ancora dei problemi, inviare un feedback alla fine di questo argomento.

### <a name="client-issues"></a>Problemi relativi al client

* Se un dispositivo non viene visualizzato nell'elenco o non viene disegnato alcun grafico, verificare che il codice del dispositivo sia in esecuzione nel dispositivo.

* Nel browser, aprire gli strumenti di sviluppo (per molti browser si può usare il tasto F12) e individuare la console. Cercare eventuali avvisi o errori.

* È possibile eseguire il debug dello script lato client in /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemi del sito Web locale

* Controllare l'output nella finestra in cui è stato avviato il nodo per l'output della console.

* Eseguire il debug del codice server, in particolare server.js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemi relativi al servizio app di Azure

* Nel portale di Azure passare all'app Web. Nel riquadro sinistro, in **Monitoraggio** selezionare **App Service logs** (Log del servizio app). Attivare **Registrazione applicazioni (file system)** , impostare **Livello** su Errore, quindi selezionare **Salva**. Aprire quindi **Flusso di registrazione** (in **Monitoraggio**).

* Dall'app Web nel portale di Azure, in **Strumenti di sviluppo** selezionare **Console** e convalidare le versioni di nodo e npm con `node -v` e `npm -v`.

* Se viene visualizzato un errore relativo alla mancata individuazione di un pacchetto, è possibile che siano stati eseguiti i passaggi non in ordine. Quando il sito viene distribuito (con `git push`), il servizio app esegue `npm install`, che viene eseguito in base alla versione corrente del nodo che è stato configurato. Se la configurazione viene modificata in un secondo momento, sarà necessario apportare una modifica non significativa al codice e premere nuovamente.

## <a name="next-steps"></a>Passaggi successivi

L'app Web è stata usata correttamente per visualizzare i dati del sensore in tempo reale dall'hub IoT.

Per un modo alternativo di visualizzazione dei dati dall'hub IoT di Azure, vedere [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
