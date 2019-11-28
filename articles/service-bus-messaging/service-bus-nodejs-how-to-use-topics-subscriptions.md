---
title: 'Guida introduttiva: Come usare gli argomenti e le sottoscrizioni del bus di servizio di Azure con Node.js'
description: "Guida introduttiva: Informazioni su come usare le sottoscrizioni e gli argomenti del bus di servizio in Azure da un'app Node.js."
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 80ca9589e7181518874dc26a9a42b57774f60498
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561583"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Guida introduttiva: Come usare gli argomenti e le sottoscrizioni del bus di servizio con Node.js e il pacchetto azure-sb
> [!div class="op_multi_selector" title1="Linguaggio di programmazione" title2="Pacchetto Node.js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Questa esercitazione illustra come creare applicazioni Node.js per inviare messaggi a un argomento del bus di servizio e ricevere messaggi da una sottoscrizione del bus di servizio usando il pacchetto [azure-sb](https://www.npmjs.com/package/azure-sb). Gli esempi sono scritti in JavaScript e usano il [modulo di Azure](https://www.npmjs.com/package/azure) per Node.js che internamente usa il pacchetto `azure-sb`.

Il pacchetto [azure-sb](https://www.npmjs.com/package/azure-sb) usa le [API di runtime REST del bus di servizio](/rest/api/servicebus/service-bus-runtime-rest). L'esperienza può essere più rapida se si usa il nuovo pacchetto [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) che impiega il [protocollo AMQP 1.0](service-bus-amqp-overview.md) più veloce. Per altre informazioni sul nuovo pacchetto, vedere [Come usare gli argomenti e le sottoscrizioni del bus di servizio con Node.js e il pacchetto @azure/service-bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), in caso contrario continuare a leggere per sapere come usare il pacchetto [azure](https://www.npmjs.com/package/azure).

Gli scenari trattati in questo articolo sono i seguenti:

- Creazione di argomenti e sottoscrizioni 
- Creazione di filtri di sottoscrizione 
- Invio di messaggi a un argomento 
- Ricezione di messaggi da una sottoscrizione
- Eliminazione di argomenti e sottoscrizioni 

Per altre informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps).

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi per i sottoscrittori di Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Seguire la procedura descritta in [Guida introduttiva: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni all'argomento](service-bus-quickstart-topics-subscriptions-portal.md) per creare uno **spazio dei nomi** del bus di servizio e ottenere la **stringa di connessione**.

    > [!NOTE]
    > In questa guida introduttiva verranno creati un **argomento** e una **sottoscrizione** all'argomento usando **Node.js**. 

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js
Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere come [creare e distribuire un'applicazione Node.js in un sito Web Azure] o [Servizio cloud Node.js][Node.js Cloud Service] usando Windows PowerShell o Sito Web con WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Per usare il bus di servizio, scaricare il pacchetto Azure Node.js, che include un set di librerie di riferimento che comunicano con i servizi REST del bus di servizio.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto
1. Aprire un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Passare alla cartella in cui è stata creata l'applicazione di esempio.
3. Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire l'output seguente:

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
3. È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. In tale cartella individuare il pacchetto **azure**, che contiene le librerie necessarie per accedere agli argomenti del bus di servizio.

### <a name="import-the-module"></a>Importare il modulo
Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurare una stringa di connessione per il bus di servizio
Il modulo Azure legge la variabile di ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` per la stringa di connessione ottenuta dal passaggio "Ottenere le credenziali" precedente. Se questa variabile di ambiente non è impostata, è necessario specificare le informazioni relative all'account durante la chiamata di `createServiceBusService`.

Per un esempio di impostazione delle variabili di ambiente per un servizio cloud di Azure, vedere [Impostare variabili di ambiente](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Creare un argomento
L'oggetto **ServiceBusService** consente di usare gli argomenti. Il codice seguente consente di creare un oggetto **ServiceBusService**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Chiamando `createTopicIfNotExists` nell'oggetto **ServiceBusService**, viene restituito l'argomento specificato, se esistente, oppure viene creato un nuovo argomento con il nome specificato. Il codice seguente usa `createTopicIfNotExists` per connettersi all'argomento denominato `MyTopic` o crearlo:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Il metodo `createTopicIfNotExists` supporta anche opzioni aggiuntive che consentono di eseguire l'override delle impostazioni predefinite degli argomenti, come la durata (TTL) dei messaggi o le dimensioni massime dell'argomento. 

L'esempio seguente illustra come impostare la dimensione massima dell'argomento su 5 GB con una durata di un minuto:

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

Dopo l'elaborazione di `returnObject` (ossia della risposta della richiesta al server), questo callback deve richiamare next, se esistente, per continuare a elaborare altri filtri o richiamare `finalCallback` per terminare la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi. Sono **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente crea un oggetto **ServiceBusService** che usa **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Creare sottoscrizioni
È possibile creare le sottoscrizioni di un argomento anche con l'oggetto **ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita il set di messaggi recapitati alla coda virtuale della sottoscrizione.

> [!NOTE]
> Per impostazione predefinita, le sottoscrizioni sono persistenti fintanto che esse, o l'argomento a cui sono associate, non vengono eliminati. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è prima di tutto necessario verificare se la sottoscrizione esiste usando il metodo `getSubscription`.
>
> È possibile eliminare automaticamente le sottoscrizioni impostando la [proprietà AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare una sottoscrizione con il filtro (MatchAll) predefinito
**MatchAll** è il filtro predefinito usato quando viene creata una sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e viene usato il filtro predefinito **MatchAll**.

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

È possibile aggiungere filtri a una sottoscrizione con il metodo `createRule` dell'oggetto **ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

> [!NOTE]
> Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima di tutto rimuovere il filtro predefinito, altrimenti **MatchAll** eseguirà l'override di qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita con il metodo `deleteRule` dell'oggetto **ServiceBusService**.
>
>

L'esempio seguente crea una sottoscrizione denominata `HighMessages` con un filtro **SqlFilter** che seleziona solo i messaggi che hanno una proprietà `messagenumber` personalizzata con valore maggiore di 3:

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

Analogamente, l'esempio seguente crea una sottoscrizione denominata `LowMessages` con un filtro **SqlFilter** che seleziona solo i messaggi che hanno una proprietà `messagenumber` con valore minore o uguale a 3:

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

Un messaggio inviato a `MyTopic` viene recapitato ai ricevitori con sottoscrizione all'argomento `AllMessages` e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti `HighMessages` e `LowMessages` (a seconda del contenuto del messaggio).

## <a name="how-to-send-messages-to-a-topic"></a>Come inviare messaggi a un argomento
Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo `sendTopicMessage` dell'oggetto **ServiceBusService**.
I messaggi inviati ad argomenti del bus di servizio sono oggetti **BrokeredMessage**.
Gli oggetti **BrokeredMessage** includono un set di proprietà standard, ad esempio `Label` e `TimeToLive`, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa a `sendTopicMessage` e popolare le proprietà standard necessarie con i valori predefiniti.

Il seguente esempio illustra come inviare cinque messaggi di test a `MyTopic`. Il valore della proprietà `messagenumber` di ogni messaggio varia nell'iterazione del ciclo, determinando quali sottoscrizioni lo ricevono:

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

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è previsto un limite alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
I messaggi vengono ricevuti da una sottoscrizione usando il metodo `receiveSubscriptionMessage` nell'oggetto **ServiceBusService**. Per impostazione predefinita, i messaggi vengono eliminati dalla sottoscrizione non appena vengono letti. Tuttavia, per leggere e bloccare il messaggio senza eliminarlo dalla sottoscrizione è possibile impostare il parametro facoltativo `isPeekLock` su **True**.

Il comportamento predefinito di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo comportamento, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio ha contrassegnato il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo del sistema risulta perso.

Se il parametro `isPeekLock` è impostato su **True**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione.
Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo **deleteMessage** e specificando il messaggio da eliminare come parametro. Il metodo **deleteMessage** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra come ricevere ed elaborare messaggi tramite `receiveSubscriptionMessage`. L'esempio prima di tutto riceve ed elimina un messaggio dalla sottoscrizione "LowMessages" e quindi riceve un messaggio dalla sottoscrizione "HighMessages" con il parametro `isPeekLock` impostato su true. Elimina infine il messaggio usando `deleteMessage`:

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
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non riesce a elaborare il messaggio per qualsiasi motivo, può chiamare il metodo `unlockMessage` nell'oggetto **ServiceBusService**. Con questo metodo, il bus di servizio sblocca il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione in questa istanza, da parte della stessa applicazione consumer o di un'altra.

Al messaggio bloccato nell'argomento è anche associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout del blocco, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo `deleteMessage`, il messaggio viene nuovamente recapitato all'applicazione al riavvio. Questo comportamento viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, aggiungere logica all'applicazione per gestire il secondo recapito del messaggio. È possibile usare la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare argomenti e sottoscrizioni
Gli argomenti e le sottoscrizioni sono persistenti a meno che la [proprietà autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) sia impostata, e devono essere eliminati in modo esplicito nel [portale di Azure][Azure portal] oppure a livello di codice.
L'esempio seguente illustra come eliminare l'argomento denominato `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. L'esempio seguente illustra come eliminare una sottoscrizione denominata `HighMessages` dall'argomento `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i seguenti collegamenti per altre informazioni.

* Vedere [Code, argomenti e sottoscrizioni][Queues, topics, and subscriptions].
* Materiale di riferimento dell'API per [SqlFilter][SqlFilter].
* Visitare il repository [Azure SDK per Node][Azure SDK for Node] su GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Creare e distribuire un'applicazione Node.js in un sito Web Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

