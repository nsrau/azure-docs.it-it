---
title: Reagire agli eventi del modulo Archiviazione BLOB - Azure Event Grid IoT Edge Documenti Microsoft
description: Reagire agli eventi del modulo di archiviazione BLOBReact to Blob Storage module events
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086599"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Esercitazione: Reagire agli eventi di archiviazione BLOB in Edge Edge (anteprima)Tutorial: React to Blob Storage events on IoT Edge (Preview)
Questo articolo illustra come distribuire l'archiviazione BLOB di Azure nel modulo IoT, che fungerebbe da autore di Griglia di eventi per inviare eventi durante la creazione di BLOB e l'eliminazione blob a Griglia di eventi.  

Per una panoramica dell'archiviazione BLOB di Azure in EdgeEdge, vedere Archiviazione BLOB di Azure in [Edge Edge](../../iot-edge/how-to-store-data-blob.md) e le relative funzionalità.

> [!WARNING]
> Azure Blob Storage on IoT Edge integration with Event Grid is in Preview

Per completare questa esercitazione è necessario disporre degli elementi seguenti:

* Sottoscrizione di **Azure:** creare un [account gratuito,](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub IoT di Azure e dispositivo Edge IoT:** seguire i passaggi della guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows,](../../iot-edge/quickstart.md) se non ne è già presente uno.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuire il modulo IoT Edge griglia eventiDeploy Event Grid IoT Edge module

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per Griglia di eventi di Azure in IoT Edge.There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Event Grid on IoT Edge. Questo articolo descrive i passaggi per distribuire Griglia di eventi in Edge Edge dal portale di Azure.This article describes the steps to deploy Event Grid on IoT Edge from the Azure portal.

>[!NOTE]
> In questa esercitazione verrà distribuito il modulo Griglia di eventi senza persistenza. Ciò significa che tutti gli argomenti e le sottoscrizioni creati in questa esercitazione verranno eliminati quando si ridistribuisce il modulo. Per ulteriori informazioni su come configurare la persistenza, vedere i seguenti articoli: [Persist state in Linux](persist-state-linux.md) o Persist state in [Windows](persist-state-windows.md). Per i carichi di lavoro di produzione, è consigliabile installare il modulo Griglia di eventi con persistenza.


### <a name="select-your-iot-edge-device"></a>Selezionare il dispositivo IoT Edge

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu nella sezione **Gestione automatica dispositivi.** 
1. Fare clic sull'ID del dispositivo di destinazione dall'elenco dei dispositivi
1. Selezionare **Set Modules** (Configura i moduli). Tenere aperta la pagina. I passaggi verranno continuati nella sezione successiva.

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure include una procedura guidata che illustra come creare un manifesto di distribuzione, anziché compilare manualmente il documento JSON.  Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli di distribuzione** selezionare **Aggiungi**
1. Dai tipi di moduli nell'elenco a discesa, selezionare **Modulo edge IoT**
1. Specificare il nome, l'immagine e le opzioni di creazione del contenitore:

   * **Nome**: eventgridmodule
   * **URI immagine**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opzioni di creazione del contenitore**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Fare clic su **Save**.
 1. Passare alla sezione successiva per aggiungere il modulo Sottoscrittore Griglia di eventi di Azure prima di distribuirlo insieme.

    >[!IMPORTANT]
    > In questa esercitazione si apprenderà a distribuire il modulo Griglia di eventi per consentire sia le richieste HTTP/HTTPTTP, l'autenticazione client disabilitata. Per i carichi di lavoro di produzione, è consigliabile abilitare solo le richieste HTTPs e i sottoscrittori con l'autenticazione client abilitata. Per ulteriori informazioni su come configurare il modulo Griglia di eventi in modo sicuro, vedere [Sicurezza e autenticazione](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Distribuire il modulo IoT Edge del sottoscrittore Griglia di eventiDeploy Event Grid Subscriber IoT Edge module

In questa sezione viene illustrato come distribuire un altro modulo IoT che fungerebbe da gestore eventi a cui gli eventi possono essere recapitati.

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli di distribuzione** selezionare di nuovo Aggiungi.In the Deployment Modules section, select **Add** again. 
1. Dai tipi di moduli nell'elenco a discesa, selezionare **Modulo edge IoT**
1. Specificare le opzioni di creazione di nome, immagine e contenitore del contenitore:

   * **Nome**: abbonato
   * **URI immagine**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opzioni di creazione contenitore**: NessunaContainer Create Options : None
1. Fare clic su **Save**.
1. Passare alla sezione successiva per aggiungere il modulo Archiviazione BLOB di AzureContinue to the next section to add the Azure Blob Storage module

## <a name="deploy-azure-blob-storage-module"></a>Distribuire il modulo Archiviazione BLOB di AzureDeploy Azure Blob Storage module

Questa sezione illustra come distribuire il modulo Archiviazione BLOB di Azure, che fungerebbe da BLOB di pubblicazione di Griglia di eventi creato ed eventi eliminati.

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli di distribuzione** selezionare **Aggiungi**
2. Dai tipi di moduli nell'elenco a discesa, selezionare **Modulo edge IoT**
3. Specificare le opzioni di creazione di nome, immagine e contenitore del contenitore:

   * **Nome**: azureblobstorageoniotedge
   * **URI immagine**: mcr.microsoft.com/azure-blob-storage:latest
   * **Opzioni di creazione del contenitore**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Il modulo di archiviazione BLOB può pubblicare eventi usando HTTPS e HTTP. 
   > - Se è stata abilitata l'autenticazione basata su client per EventGrid, assicurarsi di aggiornare il valore di EVENTGRID_ENDPOINT per consentire https, in questo modo: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`.
   > - Aggiungere anche un'altra variabile `AllowUnknownCertificateAuthority=true` di ambiente al json precedente. Quando si parla con EventGrid tramite HTTPS, **AllowUnknownCertificateAuthority** consente al modulo di archiviazione di considerare attendibili i certificati server EventGrid autofirmati.

4. Aggiornare il codice JSON copiato con le informazioni seguenti:

   - Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account devono avere una lunghezza da 3 a 24 caratteri, con lettere minuscole e numeri. Nessuno spazio.

   - Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

   - Sostituire `<event grid module name>` con il nome del modulo Griglia di eventi.
   - Sostituire `<storage mount>` in base al sistema operativo del contenitore.
     - Per i contenitori Linux, **my-volume:/blobrootFor Linux containers, my-volume:/blobroot**
     - Per i contenitori di Windows,**my-volume:C:/BlobRootFor Windows containers, my-volume:C:/BlobRoot**

5. Fare clic su **Save**.
6. Fare clic su **Avanti** per passare alla sezione percorsi

    > [!NOTE]
    > Se si usa una macchina virtuale di Azure come dispositivo perimetrale, aggiungere una regola di porta in ingresso per consentire il traffico in ingresso sulle porte host usate in questa esercitazione: 4438, 5888, 8080 e 11002. Per istruzioni sull'aggiunta della regola, vedere [Come aprire le porte a una macchina virtuale.](../../virtual-machines/windows/nsg-quickstart-portal.md)

### <a name="setup-routes"></a>Impostare i percorsi

Mantenere i percorsi predefiniti e selezionare **Avanti** per continuare con la sezione di revisione

### <a name="review-deployment"></a>Verificare la distribuzione

1. La sezione di revisione mostra il manifesto di distribuzione JSON creato in base alle selezioni effettuate nella sezione precedente. Verificare che siano visualizzati i quattro moduli seguenti: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **subscriber** e **azureblobstorageoniotedge** che vengono tutti distribuiti.
2. Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

## <a name="verify-your-deployment"></a>Verificare la distribuzione

1. Dopo l'invio della distribuzione, si torna alla pagina IoT Edge dell'hub IoT.
2. Selezionare il **dispositivo IoT Edge** di destinazione della distribuzione per aprirne i dettagli.
3. Nei dettagli del dispositivo verificare che i moduli eventgridmodule, subscriber e azureblobstorageoniotedge siano elencati come **specificati nella distribuzione** e Reported by **device**.

   Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Pubblicare gli eventi BlobCreated e BlobDeletedPublish BlobCreated and BlobDeleted events

1. Questo modulo crea automaticamente l'argomento **MicrosoftStorage**. Verificare che esista
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Output di esempio:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Per il flusso HTTPS, se l'autenticazione client è abilitata tramite la chiave di accesso utenti database, la chiave di accesso utente specificato in precedenza deve essere aggiunta come intestazione. Quindi la richiesta di curl sarà:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Per il flusso HTTPS, se l'autenticazione client è abilitata tramite certificato, la richiesta di curl sarà:For the HTTPS flow, if the client authentication is enabled via certificate, the curl request will be:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, è necessario creare una sottoscrizione di Griglia di eventi per **microsoftStorage** argomento.
    1. Creare blobsubscription.json con il contenuto seguente. Per informazioni dettagliate sul payload, consulta la [documentazione dell'API](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > La proprietà **endpointType** specifica che il sottoscrittore è un **Webhook.**  **EndpointUrl** specifica l'URL in cui il sottoscrittore è in ascolto di eventi. Questo URL corrisponde all'esempio di funzione di Azure distribuito in precedenza.

    2. Eseguire il comando seguente per creare una sottoscrizione per l'argomento. Verificare che venga visualizzato `200 OK`il codice di stato HTTP .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Per il flusso HTTPS, se l'autenticazione client è abilitata tramite la chiave di accesso utenti database, la chiave di accesso utente specificato in precedenza deve essere aggiunta come intestazione. Quindi la richiesta di curl sarà:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Per il flusso HTTPS, se l'autenticazione client è abilitata tramite certificato, la richiesta di curl sarà:For the HTTPS flow, if the client authentication is enabled via certificate, the curl request will be:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Output di esempio:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Per il flusso HTTPS, se l'autenticazione client è abilitata tramite la chiave di accesso utenti database, la chiave di accesso utente specificato in precedenza deve essere aggiunta come intestazione. Quindi la richiesta di curl sarà:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Per il flusso HTTPS, se l'autenticazione client è abilitata tramite certificato, la richiesta di curl sarà:For the HTTPS flow, if the client authentication is enabled via certificate, the curl request will be:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Scaricare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [connetterlo all'archiviazione localeDownload](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer) Azure Storage Explorer and connect it to your local storage

## <a name="verify-event-delivery"></a>Verificare il recapito degli eventi

### <a name="verify-blobcreated-event-delivery"></a>Verificare il recapito dell'evento BlobCreatedVerify BlobCreated event delivery

1. Caricare i file come BLOB di blocchi nell'archivio locale da Azure Storage Explorer e il modulo pubblicherà automaticamente gli eventi di creazione. 
2. Estrarre i registri del sottoscrittore per l'evento create. Seguire i passaggi per [verificare il recapito dell'evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Output di esempio:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verificare il recapito dell'evento BlobDeletedVerify BlobDeleted event delivery

1. Eliminare i BLOB dall'archiviazione locale usando Azure Storage Explorer e il modulo pubblicherà automaticamente gli eventi delete. 
2. Estrarre i registri del sottoscrittore per l'evento di eliminazione. Seguire i passaggi per [verificare il recapito dell'evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Output di esempio:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Congratulazioni! L'esercitazione è stata completata. Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà dell'evento.

### <a name="event-properties"></a>Proprietà degli eventi

Ecco l'elenco delle proprietà degli eventi supportate e dei relativi tipi e descrizioni. 

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| api | string | L'operazione che ha attivato l'evento. Può essere uno dei valori seguenti: <ul><li>BlobCreated - i `PutBlob` valori consentiti sono: e`PutBlockList`</li><li>BlobDeleted: i `DeleteBlob`valori `DeleteAfterUpload` `AutoDelete`consentiti sono , e . <p>L'evento `DeleteAfterUpload` viene generato quando il BLOB viene eliminato automaticamente perché la proprietà desiderata deleteAfterUpload è impostata su true. </p><p>`AutoDelete`event viene generato quando il BLOB viene eliminato automaticamente perché il valore della proprietà desiderato deleteAfterMinutes è scaduto.</p></li></ul>|
| clientRequestId | string | un ID di richiesta fornito dal client per l'operazione dell'API di archiviazione. Questo ID può essere usato per correlare i log di diagnostica di Archiviazione di Azure usando il campo "client-request-id" nei log e può essere fornito nelle richieste client usando l'intestazione "x-ms-client-request-id". Per informazioni dettagliate, vedere [Formato registro](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID richiesta generato dal servizio per l'operazione dell'API di archiviazione. Può essere usato per la correlazione ai log di diagnostica di Archiviazione di Azure usando il campo "request-id-header" nei log e viene restituito dall'avvio di una chiamata API nell'intestazione 'x-ms-request-id'. Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| eTag | string | Il valore che è possibile usare per eseguire le operazioni in modo condizionale. |
| contentType | string | Il tipo di contenuto specificato per il BLOB. |
| contentLength | integer | La dimensione del BLOB in byte. |
| blobType | string | Il tipo di BLOB. I valori validi sono "BlockBlob" o "PageBlob". |
| url | string | Percorso del BLOB. <br>Se il client usa un'API REST BLOB, l'URL ha questa struttura: * \<\>storage-account-name\<.blob.core.windows.net/ nome-contenitore nome-contenitore\>/\<.\>* <br>Se il client usa un'API REST di Data Lake Storage, l'URL ha questa struttura: * \<\>storage-account-name\<.dfs.core.windows.net/ nome-file-system\>/\<nome-file.\>* |


## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti della documentazione relativa all'archiviazione BLOB:See the following articles from the Blob Storage documentation:

- [Filtrare gli eventi di archiviazione BLOBFilter Blob Storage events](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Procedure consigliate per l'utilizzo di eventi di archiviazione BLOBRecommended practices for consuming Blob Storage events](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

In this tutorial, you published events by creating or deleting blobs in an Azure Blob Storage. Vedere le altre esercitazioni per informazioni su come inoltrare gli eventi al cloud (Azure Event Hub o Azure IoT Hub): 

- [Inoltrare eventi a Griglia di eventi di Azure](forward-events-event-grid-cloud.md)
- [Inoltrare eventi a Hub IoT di Azure](forward-events-iothub.md)
