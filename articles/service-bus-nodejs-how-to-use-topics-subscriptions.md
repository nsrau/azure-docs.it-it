<properties urlDisplayName="Service Bus Topics" pageTitle="Come usare gli argomenti del bus di servizio (Node.js) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Node.js" description="Informazioni su come usare le sottoscrizioni e gli argomenti di Bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Topics/Subscriptions" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Come usare gli argomenti e le sottoscrizioni del bus di servizio

Questa guida descrive come usare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Node.js. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi** a un argomento, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per altre informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni su argomenti e sottoscrizioni del bus di servizio][]
-   [Creare uno spazio dei nomi servizio][]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][]
-   [Creazione di un'applicazione Node.js](#create-app)
-   [Configurare l'applicazione per l'uso del bus di servizio](#configure-app)
-   [Procedura: Creare un argomento](#create-topic)
-   [Procedura: Creare sottoscrizioni](#create-subscription)
-   [Procedura: Inviare messaggi a un argomento](#send-messages)
-   [Procedura: Ricevere messaggi da una sottoscrizione](#receive-messages)
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione](#handle-crashes)
-   [Procedura: Eliminare argomenti e sottoscrizioni](#delete)
-   [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a name="create-app"></a> Creazione di un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Sito Web con WebMatrix].

##<a name="configure-app"></a> Configurare l'applicazione per l'uso del bus di servizio

Per usare il bus di servizio di Azure, è necessario scaricare e usare il pacchetto Azure Node.js che include un set di librerie di riferimento che comunicano con i servizi REST del bus di servizio.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (UNIX) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe 
restituire il seguente output:

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

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella individuare il pacchetto **azure**, che contiene le librerie necessarie per accedere agli argomenti del bus di servizio.

### Importare il modulo

Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore
del file **server.js** dell'applicazione:

    var azure = require('azure');

### Configurazione di una connessione del bus di servizio di Azure

Il modulo azure leggerà le variabili di ambiente AZURE\_SERVICEBUS\_NAMESPACE e AZURE\_SERVICEBUS\_ACCESS\_KEY per recuperare le informazioni necessarie per la connessione al bus di servizio di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni dell'account durante la chiamata a **createServiceBusService**.

Per un esempio di impostazione delle variabili di ambiente in un file di configurazione per un servizio cloud di Azure, vedere [Servizio cloud Node.js con archiviazione].

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione]

##<a name="create-topic"></a> Come creare un argomento

L'oggetto **ServiceBusService** consente di usare gli argomenti. Il codice seguente consente di creare un oggetto **ServiceBusService**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

    var serviceBusService = azure.createServiceBusService();

Quando si effettua la chiamata a **createTopicIfNotExists** sull'oggetto **ServiceBusService**, viene restituito l'argomento specificato, se esiste, o viene creato un nuovo argomento con il nome specificato. Il codice seguente usa **createTopicIfNotExists** per creare o connettersi all'argomento denominato 'MyTopic':

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** supporta inoltre opzioni aggiuntive che consentono di sostituire le impostazioni degli argomenti predefinite, come ad esempio la durata dei messaggi o la dimensione massima dell'argomento. L'esempio seguente illustra come impostare la dimensione massima dell'argomento su 5 GB e una durata di 1 minuto:

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

###Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite usando **ServiceBusService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **ServiceBusService** che usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

##<a name="create-subscription"></a> Come creare le sottoscrizioni

È possibile creare le sottoscrizioni a un argomento tramite l'oggetto **ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi recapitati alla coda virtuale della sottoscrizione.

<div class="dev-callout">
<strong>Nota</strong>
<p>le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono associate, non vengono eliminate. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è innanzitutto necessario verificare se la sottoscrizione esiste già usando il metodo <strong>getSubscription</strong>.</p>
</div>

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene usato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata 'AllMessages' e viene utilizzato il filtro predefinito **MatchAll**. 
    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Creare sottoscrizioni con i filtri

È inoltre possibile configurare filtri che consentono di specificare i messaggi inviati a un argomento da visualizzare in una sottoscrizione all'argomento specifica.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

È possibile aggiungere i filtri a una sottoscrizione tramite il metodo **createRule**dell'oggetto **ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

> [WACOM.NOTE]

> Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima di tutto rimuovere il filtro predefinito, altrimenti <strong>MatchAll</strong> sovrascriverà qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo <strong>deleteRule</strong> dell'oggetto<strong>ServiceBusService</strong>.

Nell'esempio seguente viene creata una sottoscrizione denominata 'HighMessages' con un filtro 
**SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata
**messagenumber** è maggiore di 3:

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

Analogamente, nell'esempio seguente viene creata una sottoscrizione denominata
'LowMessages' con un filtro **SqlFilter** che seleziona solo i messaggi in cui
il valore della proprietà **messagenumber** è minore o uguale a 3:

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

Un messaggio inviato a 'MyTopic' verrà sempre recapitato ai ricevitori con sottoscrizione all'argomento 'AllMessages' e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti 'HighMessages' e 'LowMessages', a seconda del contenuto del messaggio.

##<a name="send-messages"></a> Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo **sendTopicMessage** dell'oggetto **ServiceBusService**. I messaggi inviati ad argomenti del bus di servizio sono oggetti **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un insieme di proprietà standard, ad esempio **Label** e **TimeToLive**, un dizionario utilizzato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa a **sendTopicMessage** in modo da popolare le proprietà standard necessarie con valori predefiniti.

L'esempio seguente illustra come inviare cinque messaggi di test a 'MyTopic'. Si noti che il valore della proprietà **messagenumber** di ogni messaggio varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno:

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
 Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 MB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

##<a name="receive-messages"></a> Come ricevere messaggi da una sottoscrizione

I messaggi vengono ricevuti da una sottoscrizione tramite il metodo **receiveSubscriptionMessage** sull'oggetto **ServiceBusService**. Per impostazione predefinita, i messaggi vengono eliminati dalla sottoscrizione non appena vengono letti. È tuttavia possibile leggere (peek) e bloccare il messaggio senza eliminarlo dalla sottoscrizione, impostando il parametro facoltativo **isPeekLock** su **true**.

Il comportamento predefinito di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo risulterà perso.

Se il parametro **isPeekLock** è impostato su **true**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **deleteMessage** e fornendo il messaggio da eliminare come parametro. Il metodo **deleteMessage** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando la modalità **receiveSubscriptionMessage**. Nell'esempio viene prima ricevuto ed eliminato un messaggio dalla sottoscrizione 'LowMessages' e quindi viene ricevuto un messaggio dalla sottoscrizione 'HighMessages' con **isPeekLock** impostato su true. Il messaggio viene quindi eliminato usando il metodo **deleteMessage**:

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

##<a name="handle-crashes"></a> Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage** sull'oggetto **ServiceBusService**. In questo modo, il bus di servizio sbloccherà il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile
per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

##<a name="delete"></a> Come eliminare argomenti e sottoscrizioni

Gli argomenti e le sottoscrizioni sono persistenti e devono pertanto essere eliminati in modo esplicito tramite il portale di gestione di Azure o a livello di codice. Nell'esempio seguente viene illustrato come eliminare l'argomento denominato 'MyTopic':

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice seguente illustra come eliminare una sottoscrizione denominata 'HighMessages' dall'argomento 'MyTopic':

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

##<a name="next-steps"></a> Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Vedere il riferimento in MSDN: [Code, argomenti e sottoscrizioni][].
-   Riferimento sulle API per [SqlFilter][].
-   Repository [Azure SDK per Node] su GitHub.

  [Azure SDK per Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Passaggi successivi]: #nextsteps
  [Informazioni su argomenti e sottoscrizioni del bus di servizio]: #what-are-service-bus-topics
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Creazione di un'applicazione Node.js]: #Create_a_Nodejs_Application
  [Configurare l'applicazione per l'uso del bus di servizio]: #Configure_Your_Application_to_Use_Service_Bus
  [Procedura: Creare un argomento]: #How_to_Create_a_Topic
  [Procedura: Creare sottoscrizioni]: #How_to_Create_Subscriptions
  [Procedura: Inviare messaggi a un argomento]: #How_to_Send_Messages_to_a_Topic
  [Procedura: Ricevere messaggi da una sottoscrizione]: #How_to_Receive_Messages_from_a_Subscription
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Procedura: Eliminare argomenti e sottoscrizioni]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Sito Web con WebMatrix]: /it-it/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Servizio cloud Node.js]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Creazione e distribuzione di un'applicazione Node.js in un Sito Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servizio cloud Node.js con archiviazione]: /it-it/develop/nodejs/tutorials/web-app-with-storage/
  [Applicazione Web Node.js con archiviazione]: /it-it/develop/nodejs/tutorials/web-site-with-storage/
  [SqlFilter]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Web Site with WebMatrix]: /it-it/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Create and deploy a Node.js application to an Azure Web Site]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /it-it/develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /it-it/develop/nodejs/tutorials/web-site-with-storage/
