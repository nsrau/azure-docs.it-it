<properties 
   pageTitle="Scrivere applicazioni che utilizzano le code del Bus di servizio | Microsoft Azure"
   description="Come scrivere una semplice applicazione basata sulle code che utilizza il Bus di servizio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2015"
   ms.author="sethm" />

# Creare applicazioni che utilizzano le code del Bus di servizio

In questo argomento vengono descritte le code del Bus di servizio e viene illustrato come scrivere una semplice applicazione basata sulle code che utilizza il Bus di servizio.

Si consideri uno scenario dal mondo della vendita al dettaglio in cui i dati di vendita da singoli terminali di punto di vendita (POS) devono essere indirizzati a un sistema di gestione dell’inventario che utilizza i dati per determinare quando è necessario un nuovo approvvigionamento. Questa soluzione utilizza il Bus di servizio di messaggistica per la comunicazione tra i terminali e il sistema di gestione dell’inventario, come illustrato nella figura riportata di seguito:

![Code del Bus di servizio - Img1](./media/service-bus-create-queues/IC657161.gif)

Ogni terminale POS segnala i dati di vendita tramite l'invio di messaggi per la **DataCollectionQueue**. I messaggi rimangono nella coda finché vengono recuperati dal sistema di gestione dell’inventario. Questo modello viene spesso definito *messaggistica asincrona*, poiché il terminale non deve attendere una risposta dal sistema di gestione dell’inventario per continuare l'elaborazione.

## Quali sono i vantaggi offerti dalle code?

Prima di esaminare il codice necessario per configurare questa applicazione, considerare i vantaggi dell'utilizzo di una coda in questo scenario anziché i terminali POS per parlare direttamente (in modo sincrono) con il sistema di gestione del magazzino.

### Disaccoppiamento temporaneo.

Grazie al modello di messaggistica asincrono, non è necessario che produttori e gli utenti siano online nello stesso momento. L'infrastruttura di messaggistica archivia in modo affidabile i messaggi fino a quando l’utente è pronto a riceverli. Questo consente la disconnessione volontaria (ad esempio, per attività di manutenzione) o involontaria (a seguito di un guasto) dei componenti dell'applicazione distribuita senza ripercussioni sull'intero sistema. È inoltre possibile che l'applicazione dell’utente possa dover essere online solo in determinati orari. Ad esempio, in questo scenario di vendita al dettaglio, il sistema di gestione dell’inventario potrebbe essere online solo dopo la fine della giornata lavorativa.

### Livellamento del carico

In molte applicazioni il carico del sistema varia in base al momento, mentre il tempo di elaborazione richiesto per ciascuna unità di lavoro è in genere costante. L'interposizione di una coda tra produttori e utenti di messaggi implica che è necessario solo eseguire il provisioning dell'applicazione utente (il ruolo di lavoro) per servire a quest'ultima un carico medio anziché il carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto alle risorse infrastrutturali richieste per gestire il carico dell'applicazione.

![Code del Bus di servizio - Img2](./media/service-bus-create-queues/IC657162.gif)

### Bilanciamento del carico.

Con l'aumento del carico, è possibile aggiungere altri processi di lavoro per la lettura della coda di lavoro. Ciascun messaggio viene elaborato da un solo processo di lavoro. Inoltre, il bilanciamento del carico di tipo pull permette un uso ottimale dei computer di lavoro anche quando questi presentano una potenza di elaborazione diversa.Ogni computer effettuerà infatti il pull dei messaggi in base alla propria velocità massima. Questo modello viene spesso definito modello del consumer concorrente.

![Code del Bus di servizio - Img3](./media/service-bus-create-queues/IC657163.gif)

### Accoppiamento di tipo loose

L’utilizzo di un messaggio di cosa da interporre tra producer e consumer fornisce un accoppiamento intrinseco di tipo loose tra i componenti. Poiché producer e consumer sono indipendenti gli uni dagli altri, è possibile aggiornare un consumer senza causare alcun effetto sul producer. Inoltre, la topologia di messaggistica può evolversi senza influire sugli endpoint esistenti. Tratteremo questo in modo più approfondito quando si parlerà di pubblicazione/sottoscrizione.

## Mostra il codice

Nella sezione seguente viene illustrato come utilizzare il Bus di servizio per costruire l'applicazione.

### Iscriversi a un account del Bus di servizio e sottoscrizione

È necessario un account Azure per iniziare a lavorare con il Bus di servizio. Se non si dispone di una sottoscrizione, è possibile iscriversi per una versione di valutazione gratuita [qui](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### Creare uno spazio dei nomi del servizio

Dopo aver creato una sottoscrizione, è possibile creare un nuovo spazio dei nomi. È necessario assegnare al nuovo spazio dei nomi un nome univoco tra tutti gli account del Bus di servizio. Ogni spazio dei nomi funge da contenitore per un set di entità del Bus di servizio. Per ulteriori informazioni, vedere [Come creare o modificare uno spazio dei nomi del servizio Bus di servizio](https://msdn.microsoft.com/library/azure/hh690931.aspx).

### Installare il pacchetto NuGet

Per utilizzare lo spazio dei nomi del servizio Bus di servizio, un'applicazione deve fare riferimento all'assembly del Bus di servizio, in particolare a Microsoft.ServiceBus.dll. È possibile trovare questa assembly come parte di Microsoft Azure SDK, e il download è disponibile all'indirizzo della [pagina di download di Azure SDK](http://azure.microsoft.com/downloads/). In ogni modo, il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. Per informazioni dettagliate su NuGet e sul pacchetto del bus di servizio, vedere la pagina relativa all'[utilizzo del pacchetto NuGet del bus di servizio](https://msdn.microsoft.com/library/dn741354.aspx).

### Creare la coda

Operazioni di gestione per entità di messaggistica (code e pubblicazione/sottoscrizione di argomenti) per il Bus di servizio vengono eseguite tramite la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Il Bus di servizio utilizza un modello basato sulla sicurezza [Firma di accesso condiviso (SAS)](service-bus-sas-overview.md). La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. Utilizzeremo un metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) per contenere le credenziali di firma di accesso condiviso. L’istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene quindi costruita con l'indirizzo di base dello spazio dei nomi del Bus di servizio e il provider di token.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornisce i metodi per creare, enumerare ed eliminare le entità di messaggistica. Il codice riportato di seguito mostra come l’istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene creata e utilizzata per creare la coda **DataCollectionQueue**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Si noti che esistono overload del metodo [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) che abilitano le proprietà della coda da ottimizzare. Ad esempio, è possibile impostare il valore di durata (TTL) predefinito per i messaggi inviati alla coda.

### Inviare messaggi alla coda

Per le operazioni di runtime su entità del Bus di servizio; ad esempio, l'invio e la ricezione di messaggi, un'applicazione deve prima creare un oggetto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Simile alla classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), l’istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) viene creata a partire dall'indirizzo di base dello spazio dei nomi del servizio e dal provider di token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Questa classe include un insieme di proprietà standard, (ad esempio [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) and [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario utilizzato per contenere le proprietà dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo passando qualsiasi oggetto serializzabile (nell'esempio seguente passa in un oggetto **SalesData** che rappresenta i dati di vendita dal terminale POS), che utilizzerà il [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) per serializzare l'oggetto. In alternativa, un oggetto [Flusso](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) può essere fornito.

Il modo più semplice per inviare messaggi a una determinata coda, in questo caso il **DataCollectionQueue**, consiste nell'utilizzare [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) per creare un oggetto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) direttamente dall'istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### Ricezione di messaggi dalla coda

Il modo più semplice per ricevere messaggi dalla coda consiste nell'utilizzare un oggetto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) che è possibile creare direttamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) utilizzando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Il destinatario dei messaggi può lavorare in due modalità diverse: **ReceiveAndDelete** e **PeekLock**. Il [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) è impostato quando viene creato il destinatario del messaggio, come parametro per la chiamata [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).


Quando si usa la modalità **ReceiveAndDelete** la ricezione è un'operazione a un'unica fase, ovvero quando il bus di servizio riceve la richiesta, contrassegna il messaggio come consumato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** rappresenta il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come consumato, quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, il messaggio consumato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve la richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. Quando il bus di servizio vede la chiamata [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), contrassegna il messaggio come consumato.

Sono possibili altri due risultati. Innanzitutto, se l'applicazione per qualche motivo non riesce a elaborare il messaggio, può chiamare il metodo [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) sul messaggio ricevuto, (invece del metodo [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). In questo modo, il bus di servizio sbloccherà il messaggio che sarà disponibile per essere nuovamente ricevuto dallo stesso consumer o da un altro consumer concorrente. Inoltre, al blocco è associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, (ad esempio a causa di un arresto anomalo), allora il bus di servizio sbloccherà il messaggio rendendolo nuovamente disponibile per la ricezione.

Da notare che in caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta [Completa](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), il messaggio verrà recapitato di nuovo all'applicazione al riavvio del sistema. Questo è spesso definito processo *Almeno una volta*. Indica che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, allora è necessaria una logica aggiuntiva nell'applicazione per rilevare i duplicati. Ciò può essere ottenuto in base alla proprietà [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio. Il valore di questa proprietà rimane costante in tutti i tentativi di recapito. Questa situazione è definita un’elaborazione *Exactly Once*.

Il codice riportato di seguito riceve ed elabora un messaggio utilizzando la modalità **PeekLock**, ovvero l'impostazione predefinita se non viene esplicitamente fornito alcun valore [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### Utilizzare il client di coda

Negli esempi precedenti in questa sezione sono stati creati gli oggetti [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) e [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) direttamente dal [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) per inviare e ricevere messaggi dalla coda, rispettivamente. Un approccio alternativo consiste nell'utilizzare la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), che supporta sia l’invio che la ricezione delle operazioni, oltre a funzionalità più avanzate, quali le sessioni.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## Passaggi successivi

Ora che sono state apprese le nozioni di base delle code, vedere [Creare applicazioni che utilizzano argomenti del Bus di servizio e sottoscrizioni](service-bus-create-topics-subscriptions.md) per continuare questa discussione mediante le funzionalità di pubblicazione/sottoscrizione del Bus di servizio di messaggistica negoziata.

<!---HONumber=Oct15_HO3-->