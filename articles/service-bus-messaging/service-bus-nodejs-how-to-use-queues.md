---
title: Come usare le code del Bus di servizio di Azure in Node. js | Microsoft Docs
description: Informazioni su come usare le code del bus di servizio in Azure da un'app Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 1426b3d31159280ad9aac2dd240a5f083c40752d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988299"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azure-sb-package"></a>Come usare le code del Bus di servizio con Node. js e il pacchetto azure-sb
> [!div class="op_multi_selector" title1="Linguaggio di programmazione" title2="Pacchetto Node. js"]
> - [(Node. js | Service bus di azure)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

In questa esercitazione descrive come creare applicazioni Node. js per inviare e ricevere messaggi da una coda del Bus di servizio tramite il [azure-sb](https://www.npmjs.com/package/azure-sb) pacchetto. Gli esempi sono scritti in JavaScript e usare Node. js [modulo di Azure](https://www.npmjs.com/package/azure) che usa internamente il `azure-sb` pacchetto.

Il [azure-sb](https://www.npmjs.com/package/azure-sb) pacchetto Usa [le API di runtime REST del Bus di servizio](/rest/api/servicebus/service-bus-runtime-rest). È possibile ottenere un'esperienza più veloce usando le nuove [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pacchetto che usa più veloci [protocollo AMQP 1.0](service-bus-amqp-overview.md). Per altre informazioni relative al nuovo pacchetto, vedere [come usare le code del Bus di servizio con Node. js e @azure/service-bus pacchetto](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), in caso contrario, continuare a leggere per informazioni su come usare i [azure](https://www.npmjs.com/package/azure) pacchetto.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici per sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) oppure iscriversi per una [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si dispone di una coda per lavorare con, seguire i passaggi nel [portale di Azure Usa per creare una coda del Bus di servizio](service-bus-quickstart-portal.md) articolo creare una coda.
    1. Leggere la rapida **overview** del Bus di servizio **code**. 
    2. Creare un Bus di servizio **dello spazio dei nomi**. 
    3. Ottenere il **stringa di connessione**. 

        > [!NOTE]
        > Si creerà una **coda** nello spazio dei nomi del Bus di servizio con Node. js in questa esercitazione. 
 

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js
Creare un'applicazione Node.js vuota. Per istruzioni su come creare un'applicazione Node.js, vedere [Creare un'app Web Node.js nel servizio app di Azure][Create and deploy a Node.js application to an Azure Website] oppure [Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure][Node.js Cloud Service] con Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Per usare il bus di servizio di Azure, scaricare e usare il pacchetto Azure Node.js che include un set di librerie di riferimento che comunicano con i servizi REST del bus di servizio.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto
1. Usare la finestra di comando **Windows PowerShell per Node.js** per passare alla cartella **c:\\node\\sbqueues\\WebRole1** in cui è stata creata l'applicazione di esempio.
2. Tipo di **npm installare azure** nella finestra di comando, che dovrebbe restituire output simile all'esempio seguente:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node_modules**. All'interno di tale cartella trovare il **azure** pacchetto che contiene le librerie necessarie per accedere alle code del Bus di servizio.

### <a name="import-the-module"></a>Importare il modulo
Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurare una connessione del bus di servizio di Azure
Il modulo di Azure legge la variabile di ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` per ottenere le informazioni necessarie per la connessione al bus di servizio. Se non viene impostata questa variabile di ambiente, è necessario specificare le informazioni sull'account quando si chiama `createServiceBusService`.

Per un esempio di impostazione delle variabili di ambiente nel [portale di Azure][Azure portal] per un sito Web di Azure, vedere [Node.js Web Application with Storage][Node.js Web Application with Storage] (Applicazione Web Node.js con Archiviazione di Azure).

## <a name="create-a-queue"></a>Creare una coda
L'oggetto **ServiceBusService** consente di usare le code del bus di servizio. Il codice seguente consente di creare un oggetto **ServiceBusService**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per importare il modulo Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Chiamando `createQueueIfNotExists` nell'oggetto **ServiceBusService**, viene restituita la coda specificata, se esistente, oppure viene creata una nuova coda con il nome specificato. Il codice seguente usa `createQueueIfNotExists` per connettersi alla coda denominata `myqueue` o crearla:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Il metodo `createServiceBusService` supporta anche opzioni aggiuntive che consentono di eseguire l'override delle impostazioni predefinite delle code, come la durata (TTL) dei messaggi o le dimensioni massime della coda. Il seguente esempio illustra come impostare la dimensione massima della coda su 5 GB e una durata (TTL) di 1 minuto:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtri
Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite usando **ServiceBusService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automatica dei tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

```javascript
function handle (requestOptions, next)
```

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare `next` passando un callback con la seguente firma:

```javascript
function (returnObject, finalCallback, next)
```

In questo callback e dopo l'elaborazione di `returnObject` (la risposta della richiesta al server), il callback deve richiamare `next` se esistente, per continuare a elaborare altri filtri oppure richiamare `finalCallback`, che termina la chiamata al servizio .

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. Il codice seguente crea un oggetto `ServiceBusService` che usa `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiama il metodo `sendQueueMessage` per l'oggetto **ServiceBusService**. I messaggi inviati e ricevuti dalle code del bus di servizio sono oggetti **BrokeredMessage** e includono un set di proprietà standard, ad esempio **Label** e **TimeToLive**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando una stringa come messaggio. Le proprietà standard necessarie vengono popolate con i valori predefiniti.

L'esempio seguente illustra come inviare un messaggio di prova alla coda denominata `myqueue` usando `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non sono previsti limiti al numero di messaggi mantenuti in una coda, ma è prevista una limitazione alla dimensione totale dei messaggi da una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
I messaggi vengono ricevuti da una coda usando il metodo `receiveQueueMessage` nell'oggetto **ServiceBusService**. Per impostazione predefinita, i messaggi vengono eliminati dalla coda non appena vengono letti. È tuttavia possibile leggere (visualizzare) e bloccare il messaggio senza eliminarlo dalla coda impostando il parametro facoltativo `isPeekLock` su **true**.

Il comportamento predefinito di lettura ed eliminazione del messaggio come parte dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio quando si verifica un errore. Per comprendere meglio questo comportamento, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il Bus di servizio contrassegna il messaggio come consumato, quindi quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, risulterà perso il messaggio consumato prima dell'arresto anomalo.

Se il `isPeekLock` parametro è impostato su **true**, la ricezione diventa un'operazione in due fasi, che rende possibile per le applicazioni di supporto che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo `deleteMessage` e specificando il messaggio da eliminare come parametro. Il metodo `deleteMessage` contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando `receiveQueueMessage`. L'esempio prima di tutto riceve ed elimina un messaggio, quindi riceve un messaggio con `isPeekLock` impostato su **true** e infine elimina il messaggio con `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non riesce a elaborare il messaggio per qualsiasi motivo, può chiamare il metodo `unlockMessage` nell'oggetto **ServiceBusService**. si verificherà Bus di servizio sbloccherà il messaggio nella coda e renderla disponibile per essere nuovamente ricevuto dalla stessa applicazione consumer o da un'altra applicazione consumer.

È inoltre disponibile un timeout associato a un messaggio bloccato nella coda e se l'applicazione non riesce a elaborare il messaggio prima del timeout di blocco scade (ad esempio, se l'arresto anomalo dell'applicazione), quindi verrà sbloccherà automaticamente il messaggio e renderlo del Bus di servizio può essere ricevuto nuovamente.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo `deleteMessage`, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo approccio viene spesso chiamato *almeno una volta elaborazione*, vale a dire, ogni messaggio verrà elaborato almeno una volta ma in alcuni casi lo stesso messaggio potrebbe essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori di applicazioni dovranno aggiungere logica aggiuntiva all'applicazione per gestire il recapito dei messaggi duplicati. Lo scopo viene spesso usata la **MessageId** proprietà del messaggio, che rimane costante in tutti i tentativi di recapito.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle code, vedere le risorse seguenti.

* [Code, argomenti e sottoscrizioni del bus di servizio][Queues, topics, and subscriptions]
* Repository [Azure SDK per Node][Azure SDK for Node] su GitHub
* [Centro per sviluppatori di Node. js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
