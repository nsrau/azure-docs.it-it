---
title: Visualizzazione in tempo reale dei dati dell'hub IoT in un'app Web
description: Utilizzare un'applicazione Web per visualizzare i dati di temperatura e umidità raccolti da un sensore e inviati all'hub Iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675326"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizzare i dati dei sensori in tempo reale dall'hub IoT di Azure in un'applicazione Web

![Diagramma end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questa esercitazione imparerai a visualizzare i dati dei sensori in tempo reale che l'hub IoT riceve con un'app Web node.js in esecuzione nel computer locale. Dopo aver eseguito l'app Web in locale, è possibile seguire facoltativamente i passaggi per ospitare l'app Web nel servizio app di Azure.After running the web app locally, you can optionally follow steps to host the web app in Azure App Service. Se si desidera provare a visualizzare i dati nell'hub IoT con Power BI, vedere [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Operazioni da fare

* Aggiungere un gruppo di consumer all'hub IoT che verrà usato dall'applicazione Web per leggere i dati del sensore
* Scaricare il codice dell'app Web da GitHubDownload the web app code from GitHub
* Esaminare il codice dell'app Web
* Configurare le variabili di ambiente per contenere gli elementi dell'hub IoT necessari per l'app WebConfigure environment variables to hold the IoT Hub artifacts needed by your web app
* Eseguire l'app Web nel computer di sviluppoRun the web app on your development machine
* Aprire una pagina Web per visualizzare i dati in tempo reale su temperatura e umidità dall'hub IoT
* (Facoltativo) Usare l'interfaccia della riga di comando di Azure per ospitare l'app Web nel servizio app di AzureUse Azure CLI to host your web app in Azure App Service

## <a name="what-you-need"></a>Elementi necessari

* Completa il simulatore online tutorial [Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o uno dei tutorial del dispositivo; ad esempio, [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi riguardano i seguenti requisiti:

  * Una sottoscrizione di Azure attiva
  * Un hub IoT nella sottoscrizione
  * Un'applicazione client che invia messaggi all'hub IoT

* [Scarica Git](https://www.git-scm.com/downloads)

* I passaggi descritti in questo articolo presuppongono un computer di sviluppo Windows. tuttavia, è possibile eseguire facilmente questi passaggi su un sistema Linux nella shell preferita.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

[I gruppi di](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) consumer forniscono visualizzazioni indipendenti nel flusso di eventi che consentono alle app e ai servizi di Azure di utilizzare in modo indipendente i dati dello stesso endpoint dell'hub eventi. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub IoT che verrà usato dall'app Web per leggere i dati.

Eseguire il comando seguente per aggiungere un gruppo di consumer all'endpoint predefinito dell'hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Annotare il nome scelto, sarà necessario più avanti in questo tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Ottenere una stringa di connessione del servizio per l'hub IoTGet a service connection string for your IoT hub

Gli hub IoT vengono creati con diversi criteri di accesso predefiniti. Uno di questi criteri è il criterio del **servizio,** che fornisce autorizzazioni sufficienti per un servizio per leggere e scrivere gli endpoint dell'hub IoT.One such policy is the service policy, which provides sufficient permissions for a service to read and write the IoT hub's endpoints. Eseguire il comando seguente per ottenere una stringa di connessione per l'hub IoT conforme ai criteri del servizio:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

La stringa di connessione dovrebbe essere simile alla seguente:The connection string should look similar to the following:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Annotare la stringa di connessione del servizio, sarà necessario più avanti in questa esercitazione.

## <a name="download-the-web-app-from-github"></a>Scarica l'app Web da GitHub

Aprire una finestra di comando e immettere i comandi seguenti per scaricare l'esempio da GitHub e passare alla directory di esempio:Open a command window, and enter the following commands to download the sample from GitHub and change to the sample directory:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Esaminare il codice dell'app Web

Dalla directory web-apps-node-iot-hub-data-visualization, apri l'app Web nel tuo editor preferito. Di seguito viene illustrata la struttura di file visualizzata in codice VS:

![Struttura dei file dell'app Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Si è un'applicazione di un momento per esaminare i seguenti file:

* **Server.js** è uno script del lato servizio che inizializza il socket Web e la classe wrapper Hub eventi. Fornisce un callback alla classe wrapper Hub eventi utilizzata dalla classe per trasmettere i messaggi in arrivo al socket Web.

* **Event-hub-reader.js** è uno script sul lato del servizio che si connette all'endpoint predefinito dell'hub IoT usando la stringa di connessione e il gruppo di consumer specificati. Estrae DeviceId ed EnqueuedTimeUtc dai metadati nei messaggi in arrivo e quindi inoltra il messaggio utilizzando il metodo di callback registrato da server.js.

* **Chart-device-data.js** è uno script sul lato client che ascolta sul socket Web, tiene traccia di ogni DeviceId e archivia gli ultimi 50 punti di dati in ingresso per ogni dispositivo. Associa quindi i dati del dispositivo selezionato all'oggetto grafico.

* **Index.html** gestisce il layout dell'interfaccia utente per la pagina Web e fa riferimento agli script necessari per la logica lato client.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurare le variabili di ambiente per l'app Web

Per leggere i dati dall'hub IoT, l'app Web richiede la stringa di connessione dell'hub IoT e il nome del gruppo di consumer che deve leggere. Ottiene queste stringhe dall'ambiente di processo nelle seguenti righe in server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Impostare le variabili di ambiente nella finestra di comando con i comandi seguenti. Sostituire i valori segnaposto con la stringa di connessione del servizio per l'hub IoT e il nome del gruppo di consumer creato in precedenza. Non citare le corde.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Eseguire l'app Web

1. Assicurarsi che il dispositivo sia in esecuzione e che i dati vengano inviati.

2. Nella finestra di comando, eseguire le righe seguenti per scaricare e installare i pacchetti di riferimento e avviare il sito Web:

   ```cmd
   npm install
   npm start
   ```

3. Verrà visualizzato l'output nella console che indica che l'app Web si è connessa correttamente all'hub IoT ed è in ascolto sulla porta 3000:

   ![App Web avviata sulla console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Aprire una pagina Web per visualizzare i dati dall'hub IoT

Aprire un browser all'indirizzo `http://localhost:3000`.

Nell'elenco **Selezionare un dispositivo,** selezionare il dispositivo per visualizzare un grafico in esecuzione degli ultimi 50 punti dati di temperatura e umidità inviati dal dispositivo all'hub IoT.

![Pagina dell'app Web che mostra temperatura e umidità in tempo reale](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Dovresti anche vedere l'output nella console che mostra i messaggi che l'app Web sta trasmettendo al client browser:  

![Output di trasmissione dell'app Web sulla console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Ospitare l'app Web nel servizio app

La [funzionalità App Web del servizio app di Azure](https://docs.microsoft.com/azure/app-service/overview) offre una piattaforma come servizio (PAAS) per l'hosting di applicazioni Web.The Web Apps feature of Azure App Service provides a platform as a service (PAAS) for hosting web applications. Le applicazioni Web ospitate nel servizio app di Azure possono trarre vantaggio da potenti funzionalità di Azure come sicurezza aggiuntiva, bilanciamento del carico e scalabilità, nonché soluzioni DevOps di Azure e partner come distribuzione continua, gestione dei pacchetti e così via. Il servizio app di Azure supporta le applicazioni Web sviluppate in molte lingue comuni e distribuite nell'infrastruttura Windows o Linux.Azure App Service supports web applications developed in many popular languages and deployed on Windows or Linux infrastructure.

In questa sezione viene eseguito il provisioning di un'app Web nel servizio app e ne viene distribuito il codice usando i comandi dell'interfaccia della riga di comando di Azure.In this section, you provision a web app in App Service and deploy your code to it by using Azure CLI commands. È possibile trovare i dettagli dei comandi utilizzati nella documentazione di [az webapp.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Prima di iniziare, assicurarsi di aver completato i passaggi per aggiungere un gruppo di [risorse all'hub IoT](#add-a-consumer-group-to-your-iot-hub), ottenere una stringa di connessione del servizio per l'hub [IoT](#get-a-service-connection-string-for-your-iot-hub)e [scaricare l'app Web da GitHub.](#download-the-web-app-from-github)

1. Un [piano di servizio app](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definisce un set di risorse di calcolo per l'esecuzione di un'app ospitata nel servizio app. In questa esercitazione viene usato il livello Sviluppatore/Gratuito per ospitare l'app Web.In this tutorial, we use the Developer/Free tier to host the web app. Con il livello Gratuito, l'app Web viene eseguita in risorse di Windows condivise con altre app del servizio app, incluse quelle di altri clienti. Azure offre anche piani di servizio app per distribuire app Web in risorse di calcolo Linux.Azure also offers App Service plans to deploy web apps on Linux compute resources. È possibile ignorare questo passaggio se si dispone già di un piano di servizio app che si vuole usare.

   Per creare un piano di servizio app usando il livello gratuito di Windows, eseguire il comando seguente. Usare lo stesso gruppo di risorse in cui si trova l'hub IoT.Use the same resource group your IoT hub is in. Il nome del piano di servizio può contenere lettere maiuscole e minuscole, numeri e trattini.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Eseguire ora il provisioning di un'app Web nel piano di servizio app. Il `--deployment-local-git` parametro consente di caricare e distribuire il codice dell'app Web da un repository Git nel computer locale. Il nome dell'app Web deve essere univoco a livello globale e può contenere lettere maiuscole e minuscole, numeri e trattini. Assicurarsi di specificare Node versione 10.6 o successiva per il `--runtime` parametro, a seconda della versione del runtime Node.js in uso. È possibile `az webapp list-runtimes` utilizzare il comando per ottenere un elenco dei runtime supportati.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Aggiungere ora le impostazioni dell'applicazione per le variabili di ambiente che specificano la stringa di connessione hub IoT e il gruppo di consumer hub eventi. Le singole impostazioni sono `-settings` delimitate da spazi nel parametro. Usare la stringa di connessione del servizio per l'hub IoT e il gruppo di consumer creato in precedenza in questa esercitazione. Non racchiudere i valori.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Abilitare il protocollo Web Sockets per l'app Web e impostare l'app Web in modo che riceva solo richieste HTTPS (le richieste HTTP vengono reindirizzate a HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Per distribuire il codice nel servizio app, è necessario usare le credenziali di distribuzione a livello di [utente.](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials) Le credenziali di distribuzione a livello utente sono diverse dalle credenziali di Azure e vengono usate per le distribuzioni Git locali e FTP in un'app Web.Your user-level deployment credentials are different from your Azure credentials and are used for Git local and FTP deployments to a web app. Una volta impostate, sono valide in tutte le app del servizio app in tutte le sottoscrizioni nell'account Azure.Once set, they's valid across all of your App Service apps in all subscriptions in your Azure account. Se in precedenza sono state impostate credenziali di distribuzione a livello di utente, è possibile utilizzarle.

   Se in precedenza non sono state impostate credenziali di distribuzione a livello di utente o non si ricorda la password, eseguire il comando seguente. Il nome utente della distribuzione deve essere univoco all'interno di Azure e non deve contenere il simbolo '' per i push Git locali. Quando richiesto, immettere e confermare la nuova password. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Ottenere l'URL Git da usare per eseguire il push del codice al servizio app.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Aggiungere un telecomando al clone che fa riferimento al repository Git per l'app Web nel servizio app. Per \<l'URL\>del clone Git , utilizzare l'URL restituito nel passaggio precedente. Eseguire il comando seguente nella finestra di comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Per distribuire il codice nel servizio app, immettere il comando seguente nella finestra di comando. Se vengono richieste le credenziali, immettere le credenziali di distribuzione a livello utente create nel passaggio 5.If is prompted for credentials, enter the user-level deployment credentials that you created in step 5. Assicurarsi di eseguire il push nel ramo master del servizio app remoto.

    ```cmd
    git push webapp master:master
    ```

9. L'avanzamento della distribuzione verrà aggiornato nella finestra di comando. Una distribuzione corretta terminerà con righe simili all'output seguente:A successful deployment will end with lines similar to the following output:

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

11. Andare a `https://<your web app name>.azurewebsites.net` in un browser. Una pagina Web simile a quella visualizzata durante l'esecuzione locale dell'app Web. Supponendo che il dispositivo sia in esecuzione e che invii dati, dovrebbe essere visualizzato un grafico in esecuzione delle 50 letture di temperatura e umidità più recenti inviate dal dispositivo.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi con questo esempio, provare i passaggi descritti nelle sezioni seguenti. Se hai ancora problemi, inviaci un feedback in fondo a questo argomento.

### <a name="client-issues"></a>Problemi dei client

* Se un dispositivo non compare nell'elenco o non viene disegnato alcun grafico, assicurati che il codice del dispositivo sia in esecuzione sul dispositivo.

* Nel browser aprire gli strumenti di sviluppo (in molti browser il tasto F12 lo aprirà) e trova la console. Cercare eventuali avvisi o errori stampati.

* È possibile eseguire il debug dello script sul lato client in /js/chat-device-data.js.You can debug client-side script in /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemi relativi al sito Web locale

* Osservare l'output nella finestra in cui è stato avviato il nodo per l'output della console.

* Eseguire il debug del codice server, in particolare server.js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemi del servizio app di AzureAzure App Service issues

* Nel portale di Azure passare all'app Web. In **Monitoraggio** nel riquadro sinistro selezionare **Registri del servizio app**. Attivare **Registrazione applicazione (File System),** impostare **Livello** su Errore, quindi selezionare **Salva**. Aprire quindi **Flusso di log** (in **Monitoraggio**).

* Dal portale Web nel portale di Azure, in **Strumenti** di sviluppo `npm -v`selezionare **Console** e convalidare le versioni di nodi e npm con `node -v` e .

* Se viene visualizzato un errore relativo al non trovare un pacchetto, è possibile che i passaggi non siano in ordine. Quando il sito viene `git push`distribuito (con ) viene eseguito `npm install`il servizio app , che viene eseguito in base alla versione corrente del nodo che ha configurato. Se questo viene modificato nella configurazione in un secondo momento, è necessario apportare una modifica senza significato al codice e spingere di nuovo.

## <a name="next-steps"></a>Passaggi successivi

L'app Web è stata usata correttamente per visualizzare i dati del sensore in tempo reale dall'hub IoT.

Per un altro modo per visualizzare i dati dall'hub IoT di Azure, vedere Usare Power BI per visualizzare i dati dei sensori in tempo reale dall'hub IoT.For another way to visualize data from Azure IoT Hub, see [Use Power BI to visualize real-time sensor data from your IoT hub.](iot-hub-live-data-visualization-in-power-bi.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
