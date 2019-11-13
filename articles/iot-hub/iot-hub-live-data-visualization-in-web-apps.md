---
title: Visualizzazione dei dati in tempo reale dei dati dell'hub delle cose in un'app Web
description: Usare un'applicazione Web per visualizzare i dati di temperatura e umidità raccolti da un sensore e inviati all'hub Internet.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 073a766662b2ead4b816276fa7fda6dc5e6caca7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954640"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizzare i dati del sensore in tempo reale dall'hub Azure Internet in un'applicazione Web

![Diagramma end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questa esercitazione si apprenderà come visualizzare i dati del sensore in tempo reale che l'hub delle cose riceve con un'app Web Node. js in esecuzione nel computer locale. Dopo aver eseguito l'app Web in locale, è possibile seguire la procedura per ospitare l'app Web nel servizio app Azure. Se si desidera provare a visualizzare i dati nell'hub IoT con Power BI, vedere [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Operazioni da fare

* Aggiungere un gruppo di consumer all'hub delle cose che l'applicazione Web userà per leggere i dati dei sensori
* Scaricare il codice dell'app Web da GitHub
* Esaminare il codice dell'app Web
* Configurare le variabili di ambiente in modo che contengano gli elementi dell'hub degli elementi necessari per l'app Web
* Eseguire l'app Web nel computer di sviluppo
* Aprire una pagina Web per visualizzare i dati di temperatura e umidità in tempo reale dall'hub Internet delle cose
* Opzionale Usare l'interfaccia della riga di comando di Azure per ospitare l'app Web nel servizio app Azure

## <a name="what-you-need"></a>Elementi necessari

* Completare l'esercitazione del [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sui dispositivi; ad esempio, [Raspberry Pi con node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi requisiti coprono i requisiti seguenti:

  * Una sottoscrizione di Azure attiva
  * Un hub IoT nella sottoscrizione
  * Un'applicazione client che invia messaggi all'hub IoT

* [Scaricare Git](https://www.git-scm.com/downloads)

* I passaggi descritti in questo articolo presuppongono un computer di sviluppo Windows; Tuttavia, è possibile eseguire facilmente questi passaggi in un sistema Linux nella shell preferita.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Eseguire il comando seguente per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I [gruppi di consumer](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) forniscono visualizzazioni indipendenti nel flusso di eventi che consentono alle app e ai servizi di Azure di utilizzare i dati in modo indipendente dallo stesso endpoint dell'hub eventi. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub Internet che verrà usato dall'app Web per leggere i dati.

Eseguire il comando seguente per aggiungere un gruppo di consumer all'endpoint predefinito dell'hub Internet.

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Annotare il nome scelto, che sarà necessario più avanti in questa esercitazione.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Ottenere una stringa di connessione del servizio per l'hub Internet delle cose

Gli hub Internet vengono creati con diversi criteri di accesso predefiniti. Uno di questi criteri è il criterio del **servizio** , che fornisce autorizzazioni sufficienti a un servizio per la lettura e la scrittura degli endpoint dell'hub. Eseguire il comando seguente per ottenere una stringa di connessione per l'hub delle cose che rispetta i criteri del servizio:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

La stringa di connessione avrà un aspetto simile al seguente:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Annotare la stringa di connessione del servizio, che sarà necessaria più avanti in questa esercitazione.

## <a name="download-the-web-app-from-github"></a>Scaricare l'app Web da GitHub

Aprire una finestra di comando e immettere i comandi seguenti per scaricare l'esempio da GitHub e passare alla directory di esempio:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Esaminare il codice dell'app Web

Dalla directory Web-Apps-node-Internet-Hub-Data-Visualization aprire l'app Web nell'editor preferito. Di seguito viene illustrata la struttura di file visualizzata in VS Code:

![Struttura del file dell'app Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Esaminare i file seguenti:

* **Server. js** è uno script sul lato servizio che inizializza il Web socket e la classe wrapper dell'hub eventi. Fornisce un callback alla classe wrapper dell'hub eventi utilizzata dalla classe per trasmettere i messaggi in ingresso al socket Web.

* **Event-Hub-Reader. js** è uno script sul lato del servizio che si connette all'endpoint predefinito dell'hub Internet con la stringa di connessione e il gruppo di consumer specificati. Estrae DeviceId e EnqueuedTimeUtc dai metadati dei messaggi in ingresso e quindi inoltra il messaggio usando il metodo di callback registrato da server. js.

* **Chart-Device-Data. js** è uno script sul lato client che è in ascolto sul Web socket, tiene traccia di ogni DeviceID e archivia gli ultimi 50 punti di dati in arrivo per ogni dispositivo. Associa quindi i dati del dispositivo selezionati all'oggetto grafico.

* **Index. html** gestisce il layout dell'interfaccia utente per la pagina Web e fa riferimento agli script necessari per la logica lato client.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurare le variabili di ambiente per l'app Web

Per leggere i dati dall'hub Internet delle cose, l'app Web richiede la stringa di connessione dell'hub Internet e il nome del gruppo di consumer da leggere. Ottiene queste stringhe dall'ambiente del processo nelle righe seguenti in server. js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Impostare le variabili di ambiente nella finestra di comando con i comandi seguenti. Sostituire i valori segnaposto con la stringa di connessione del servizio per l'hub Internet e il nome del gruppo di consumer creato in precedenza. Non racchiudere tra virgolette le stringhe.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Eseguire l'app Web

1. Verificare che il dispositivo sia in esecuzione e che invii i dati.

2. Nella finestra di comando eseguire le righe seguenti per scaricare e installare i pacchetti a cui si fa riferimento e avviare il sito Web:

   ```cmd
   npm install
   npm start
   ```

3. Nella console verrà visualizzato l'output che indica che l'app Web è stata connessa correttamente all'hub Internet e che è in ascolto sulla porta 3000:

   ![App Web avviata nella console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Aprire una pagina Web per visualizzare i dati dall'hub Internet delle cose

Aprire un browser per `http://localhost:3000`.

Nell'elenco **selezionare un dispositivo** selezionare il dispositivo per visualizzare un tracciato di esecuzione degli ultimi 50 punti dati di temperatura e umidità inviati dal dispositivo all'hub Internet.

![Pagina dell'app Web che mostra temperatura e umidità in tempo reale](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Dovrebbe essere visualizzato anche l'output nella console che mostra i messaggi trasmessi dall'app Web al client browser:  

![Output broadcast dell'app Web nella console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Ospitare l'app Web nel servizio app

La [funzionalità app Web di app Azure servizio](https://docs.microsoft.com/azure/app-service/overview) fornisce una piattaforma distribuita come servizio (PaaS) per l'hosting di applicazioni Web. Le applicazioni Web ospitate nel servizio app Azure possono trarre vantaggio dalle potenti funzionalità di Azure, ad esempio sicurezza aggiuntiva, bilanciamento del carico e scalabilità, nonché soluzioni DevOps di Azure e partner come la distribuzione continua, la gestione dei pacchetti e così via. App Azure servizio supporta applicazioni Web sviluppate in molti linguaggi comuni e distribuite in un'infrastruttura Windows o Linux.

In questa sezione si esegue il provisioning di un'app Web nel servizio app e si distribuisce il codice usando i comandi dell'interfaccia della riga di comando di Azure. Per informazioni dettagliate sui comandi usati nella documentazione [AZ webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) , vedere. Prima di iniziare, assicurarsi di aver completato i passaggi per [aggiungere un gruppo di risorse all'hub delle](#add-a-consumer-group-to-your-iot-hub)cose, [ottenere una stringa di connessione del servizio per l'hub Internet](#get-a-service-connection-string-for-your-iot-hub)e [scaricare l'app Web da GitHub](#download-the-web-app-from-github).

1. Un [piano di servizio app](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definisce un set di risorse di calcolo per l'esecuzione di un'app ospitata nel servizio app. In questa esercitazione viene usato il livello Developer/Free per ospitare l'app Web. Con il livello gratuito, l'app Web viene eseguita su risorse Windows condivise con altre app del servizio app, incluse le app di altri clienti. Azure offre anche piani di servizio app per distribuire app Web in risorse di calcolo Linux. È possibile ignorare questo passaggio se si dispone già di un piano di servizio app che si vuole usare.

   Per creare un piano di servizio app usando il livello gratuito di Windows, eseguire il comando seguente. Usare lo stesso gruppo di risorse in cui si trova l'hub Internet. Il nome del piano di servizio può contenere lettere maiuscole e minuscole, numeri e trattini.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Eseguire ora il provisioning di un'app Web nel piano di servizio app. Il `--deployment-local-git` parametro consente di caricare e distribuire il codice dell'app Web da un repository git nel computer locale. Il nome dell'app Web deve essere globalmente univoco e può contenere lettere maiuscole e minuscole, numeri e trattini.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Aggiungere ora le impostazioni dell'applicazione per le variabili di ambiente che specificano la stringa di connessione dell'hub e il gruppo di consumer dell'hub eventi. Le singole impostazioni sono delimitate da spazi nel parametro `-settings`. Usare la stringa di connessione del servizio per l'hub e il gruppo di consumer creato in precedenza in questa esercitazione. Non racchiudere i valori.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Abilitare il protocollo Web Sockets per l'app Web e impostare l'app Web in modo che riceva solo richieste HTTPS (le richieste HTTP vengono reindirizzate a HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Per distribuire il codice nel servizio app, si useranno le [credenziali di distribuzione a livello di utente](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Le credenziali di distribuzione a livello di utente sono diverse dalle credenziali di Azure e vengono usate per le distribuzioni FTP e locali git in un'app Web. Una volta impostati, sono validi in tutte le app del servizio app in tutte le sottoscrizioni dell'account Azure. Se in precedenza sono state impostate le credenziali di distribuzione a livello di utente, è possibile utilizzarle.

   Se in precedenza non sono state impostate le credenziali di distribuzione a livello di utente o non è possibile ricordare la password, eseguire il comando seguente. Il nome utente della distribuzione deve essere univoco all'interno di Azure e non deve contenere il simbolo ' @' per le notifiche push git locali. Quando viene richiesto, immettere e confermare la nuova password. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Ottenere l'URL git da usare per eseguire il push del codice nel servizio app.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Aggiungere un oggetto remoto al clone che fa riferimento al repository Git per l'app Web nel servizio app. Per \<URL del clone git\>, usare l'URL restituito nel passaggio precedente. Eseguire il comando seguente nella finestra di comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Per distribuire il codice nel servizio app, immettere il comando seguente nella finestra di comando. Se vengono richieste le credenziali, immettere le credenziali di distribuzione a livello di utente create nel passaggio 5. Assicurarsi di effettuare il push nel ramo master del servizio app remoto.

    ```cmd
    git push webapp master:master
    ```

9. Lo stato di avanzamento della distribuzione viene aggiornato nella finestra di comando. Una distribuzione corretta terminerà con righe simili all'output seguente:

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

Se si verificano problemi con questo esempio, provare a eseguire i passaggi nelle sezioni riportate di seguito. Se si verificano ancora problemi, inviare commenti e suggerimenti nella parte inferiore di questo argomento.

### <a name="client-issues"></a>Problemi relativi ai client

* Se un dispositivo non viene visualizzato nell'elenco o non viene disegnato alcun grafico, verificare che il codice del dispositivo sia in esecuzione nel dispositivo.

* Nel browser aprire gli strumenti di sviluppo (in molti browser il tasto F12 lo aprirà) e trovare la console. Cercare eventuali avvisi o errori stampati.

* È possibile eseguire il debug di script lato client in/JS/chat-Device-Data.js.

### <a name="local-website-issues"></a>Problemi relativi ai siti Web locali

* Controllare l'output nella finestra in cui è stato avviato il nodo per l'output della console.

* Eseguire il debug del codice server, in particolare server. js e/scripts/Event-Hub-Reader.js.

### <a name="azure-app-service-issues"></a>Problemi del servizio app Azure

* In portale di Azure passare all'app Web. In **monitoraggio** nel riquadro sinistro selezionare log del **servizio app**. Attivare **registrazione applicazioni (file System)** su on, impostare **livello** su errore, quindi selezionare **Salva**. Aprire quindi il **flusso di log** (in **monitoraggio**).

* Dall'app Web in portale di Azure, in **strumenti di sviluppo** selezionare **console** e convalidare le versioni node e npm con `node -v` e `npm -v`.

* Se viene visualizzato un errore relativo alla mancata ricerca di un pacchetto, è possibile che siano stati eseguiti i passaggi non in ordine. Quando il sito viene distribuito (con `git push`), il servizio app viene eseguito `npm install`, che viene eseguito in base alla versione corrente del nodo che è stato configurato. Se la configurazione viene modificata in un secondo momento, sarà necessario apportare una modifica non significativa al codice e premere nuovamente.

## <a name="next-steps"></a>Passaggi successivi

L'app Web è stata usata correttamente per visualizzare i dati del sensore in tempo reale dall'hub IoT.

Per un altro modo per visualizzare i dati dall'hub di Azure, vedere [usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub](iot-hub-live-data-visualization-in-power-bi.md)Internet.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
