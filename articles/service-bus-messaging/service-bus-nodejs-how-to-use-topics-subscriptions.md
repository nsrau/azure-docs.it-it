---
title: Usare argomenti e sottoscrizioni del bus di servizio di Azure con Node.js | Documentazione Microsoft
description: Informazioni su come usare le sottoscrizioni e gli argomenti del bus di servizio in Azure da un&quot;app Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 24375a7c56cdad59363803ef3aae724977961b44
ms.lasthandoff: 04/06/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare gli argomenti e le sottoscrizioni del bus di servizio
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questa guida descrive come usare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Node.js. Gli scenari presentati includono la **creazione di argomenti e sottoscrizioni**, la **creazione di filtri per le sottoscrizioni**, l'**invio di messaggi** a un argomento, la **ricezione di messaggi da una sottoscrizione** e l'**eliminazione di argomenti e sottoscrizioni**. Per altre informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js
Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creare un'app Web Node.js nel servizio app di Azure] oppure [Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure][Node.js Cloud Service] con Windows PowerShell o Sito Web con WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Per usare il bus di servizio, scaricare il pacchetto Azure Node.js, che include un set di librerie di riferimento che comunicano con i servizi REST del bus di servizio.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminal** (Mac,) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.
2. Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire l'output seguente:

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
3. È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. In tale cartella trovare il pacchetto **azure**, che contiene le librerie necessarie per accedere agli argomenti del bus di servizio.

### <a name="import-the-module"></a>Importare il modulo
Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurare una stringa di connessione per il bus di servizio
Il modulo di Azure legge le variabili di ambiente AZURE\_SERVICEBUS\_NAMESPACE and AZURE\_SERVICEBUS\_ACCESS\_KEY per informazioni necessarie per la connessione al bus di servizio. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni relative all'account quando si chiama **createServiceBusService**.

Per un esempio di impostazione delle variabili di ambiente in un file di configurazione per un servizio cloud di Azure, vedere [Creazione di un'applicazione Web Node.js con Archiviazione][Node.js Cloud Service with Storage].

Per un esempio di impostazione delle variabili di ambiente nel [portale di Azure classico][Azure classic portal], vedere [Creazione di un'applicazione Web Node.js con Archiviazione][Node.js Web Application with Storage].

## <a name="create-a-topic"></a>Creare un argomento
L'oggetto **ServiceBusService** consente di usare gli argomenti. Il codice seguente consente di creare un oggetto **ServiceBusService**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Quando si esegue la chiamata di **createTopicIfNotExists** nell'oggetto **ServiceBusService**, viene restituito l'argomento specificato, se esiste, o viene creato un nuovo argomento con il nome specificato. Il codice seguente usa **createTopicIfNotExists** per creare o connettersi all'argomento denominato 'MyTopic':

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** supporta anche opzioni aggiuntive che consentono di eseguire l'override delle impostazioni degli argomenti predefinite, ad esempio la durata dei messaggi o la dimensione massima dell'argomento. L'esempio seguente illustra come impostare la dimensione massima dell'argomento su 5 GB con una durata di 1 minuto:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtri
Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite usando **ServiceBusService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automatica dei tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

```javascript
function handle (requestOptions, next)
```

Dopo aver eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo chiama `next` passando un callback con la firma seguente:

```javascript
function (returnObject, finalCallback, next)
```

In questo callback, e dopo l'elaborazione di **returnObject**, ossia la risposta della richiesta al server, il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare **finalCallback** per concludere la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi. Sono **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **ServiceBusService** che usa **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Creare sottoscrizioni
È possibile creare le sottoscrizioni di un argomento anche con l'oggetto **ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita il set di messaggi recapitati alla coda virtuale della sottoscrizione.

> [!NOTE]
> Le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono associate, non vengono eliminate. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è prima necessario verificare se la sottoscrizione esiste già usando il metodo **getSubscription**.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare una sottoscrizione con il filtro (MatchAll) predefinito
Il filtro **MatchAll** è il filtro predefinito e viene usato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e viene usato il filtro predefinito **MatchAll**.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Creare sottoscrizioni con i filtri
È anche possibile creare filtri che consentono di definire l'ambito dei messaggi inviati a un argomento che devono essere visualizzati in una specifica sottoscrizione dell'argomento.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un subset di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

È possibile aggiungere filtri a una sottoscrizione usando il metodo **createRule** dell'oggetto **ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

> [!NOTE]
> Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima di tutto rimuovere il filtro predefinito, altrimenti **MatchAll** eseguirà l'override di qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo **deleteRule** dell'oggetto **ServiceBusService**.
>
>

L'esempio seguente crea una sottoscrizione denominata `HighMessages` con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata **messagenumber** è maggiore di 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Analogamente, l'esempio seguente crea una sottoscrizione denominata `LowMessages` con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **messagenumber** è minore o uguale a 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Un messaggio inviato a `MyTopic` verrà sempre recapitato ai ricevitori con sottoscrizione all'argomento `AllMessages` e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti `HighMessages` e `LowMessages` (a seconda del contenuto del messaggio).

## <a name="how-to-send-messages-to-a-topic"></a>Come inviare messaggi a un argomento
Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo **sendTopicMessage** dell'oggetto **ServiceBusService**.
I messaggi inviati ad argomenti del bus di servizio sono oggetti **BrokeredMessage**.
Gli oggetti **BrokeredMessage** includono un insieme di proprietà standard, ad esempio **Label** e **TimeToLive**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa a **sendTopicMessage** in modo da popolare le proprietà standard necessarie con valori predefiniti.

Nell'esempio seguente viene illustrato come impostare cinque messaggi di test su 'MyTopic'. Si noti come il valore della proprietà **messagenumber** di ogni messaggio varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno:

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
I messaggi vengono ricevuti da una sottoscrizione usando il metodo **receiveSubscriptionMessage** per l'oggetto **ServiceBusService**. Per impostazione predefinita, i messaggi vengono eliminati dalla sottoscrizione non appena vengono letti. È tuttavia possibile leggere (visualizzarne l'anteprima) e bloccare il messaggio senza eliminarlo dalla sottoscrizione, impostando il parametro facoltativo **isPeekLock** su **true**.

Il comportamento predefinito di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Se il parametro **isPeekLock** è impostato su **true**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione.
Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo **deleteMessage** e specificando il messaggio da eliminare come parametro. Il metodo **deleteMessage** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando **receiveSubscriptionMessage**. L'esempio prima riceve ed elimina un messaggio dalla sottoscrizione "LowMessages" e poo riceve un messaggio dalla sottoscrizione "HighMessages" con **isPeekLock** impostato su true. Il messaggio viene quindi eliminato usando il metodo **deleteMessage**:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non riesce a elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage** nell'oggetto **ServiceBusService**. In questo modo, il bus di servizio sbloccherà il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa applicazione consumer o da un'altra.

Al messaggio bloccato nella sottoscrizione è anche associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima della chiamata del metodo **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare argomenti e sottoscrizioni
Gli argomenti e le sottoscrizioni sono persistenti e devono essere eliminati in modo esplicito tramite il [portale di Azure classico][Azure classic portal] oppure a livello di codice.
L'esempio seguente illustra come eliminare l'argomento denominato `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. L'esempio seguente illustra come eliminare una sottoscrizione denominata `HighMessages` dall'argomento `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i seguenti collegamenti per altre informazioni.

* Vedere [Code, argomenti e sottoscrizioni del bus di servizio][Queues, topics, and subscriptions].
* Informazioni di riferimento sulle API per [SqlFilter][SqlFilter].
* Visitare il repository [Azure SDK per Node][Azure SDK for Node] su GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure classic portal]: https://manage.windowsazure.com
[SqlFilter.SqlExpression]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Creare un'app Web Node.js nel servizio app di Azure]: ../app-service-web/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js Web Application with Storage]: ../storage/storage-nodejs-use-table-storage-cloud-service-app.md

