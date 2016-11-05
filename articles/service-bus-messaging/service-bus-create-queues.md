---
title: Scrivere applicazioni che utilizzano le code del Bus di servizio | Microsoft Docs
description: Come scrivere una semplice applicazione basata sulle code che utilizza il Bus di servizio.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: sethm

---
# <a name="create-applications-that-use-service-bus-queues"></a>Creare applicazioni che utilizzano le code del Bus di servizio
Questo argomento descrive le code del bus di servizio e illustra come scrivere una semplice applicazione basata sulle code che usa il bus di servizio.

Si consideri uno scenario dal mondo della vendita al dettaglio in cui i dati di vendita da singoli terminali di punto di vendita (POS) devono essere indirizzati a un sistema di gestione dell’inventario che utilizza i dati per determinare quando è necessario un nuovo approvvigionamento. Questa soluzione utilizza il Bus di servizio di messaggistica per la comunicazione tra i terminali e il sistema di gestione dell’inventario, come illustrato nella figura riportata di seguito:

![Figura 1 Code del bus di servizio](./media/service-bus-create-queues/IC657161.gif)

Ogni terminale POS segnala i dati di vendita tramite l'invio di messaggi alla coda **DataCollectionQueue**. I messaggi rimangono nella coda finché vengono recuperati dal sistema di gestione dell’inventario. Questo schema viene spesso definito *messaggistica asincrona*, perché il terminale non deve attendere una risposta dal sistema di gestione inventario per continuare l'elaborazione.

## <a name="why-queuing?"></a>Quali sono i vantaggi offerti dalle code?
Prima di esaminare il codice necessario per configurare questa applicazione, considerare i vantaggi dell'utilizzo di una coda in questo scenario anziché i terminali POS per parlare direttamente (in modo sincrono) con il sistema di gestione del magazzino.

### <a name="temporal-decoupling"></a>Disaccoppiamento temporaneo.
Grazie al modello di messaggistica asincrono, non è necessario che produttori e gli utenti siano online nello stesso momento. L'infrastruttura di messaggistica archivia in modo affidabile i messaggi fino a quando l’utente è pronto a riceverli. Questo consente la disconnessione volontaria (ad esempio, per attività di manutenzione) o involontaria (a seguito di un guasto) dei componenti dell'applicazione distribuita senza ripercussioni sull'intero sistema. È inoltre possibile che l'applicazione dell’utente possa dover essere online solo in determinati orari. Ad esempio, in questo scenario di vendita al dettaglio, il sistema di gestione dell’inventario potrebbe essere online solo dopo la fine della giornata lavorativa.

### <a name="load-leveling"></a>Livellamento del carico
In molte applicazioni il carico del sistema varia in base al momento, mentre il tempo di elaborazione richiesto per ciascuna unità di lavoro è in genere costante. L'interposizione di una coda tra produttori e utenti di messaggi implica che è necessario solo eseguire il provisioning dell'applicazione utente (il ruolo di lavoro) per servire a quest'ultima un carico medio anziché il carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto alle risorse infrastrutturali richieste per gestire il carico dell'applicazione.

![Figura 2 Code del bus di servizio](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Bilanciamento del carico.
Con l'aumento del carico, è possibile aggiungere altri processi di lavoro per la lettura della coda di lavoro. Ciascun messaggio viene elaborato da un solo processo di lavoro. Inoltre, il bilanciamento del carico di tipo pull permette un uso ottimale dei computer di lavoro anche quando questi presentano una potenza di elaborazione diversa.Ogni computer effettuerà infatti il pull dei messaggi in base alla propria velocità massima. Questo modello viene spesso definito modello del consumer concorrente.

![Figura 3 Code del bus di servizio](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Accoppiamento di tipo loose
L’utilizzo di un messaggio di cosa da interporre tra producer e consumer fornisce un accoppiamento intrinseco di tipo loose tra i componenti. Poiché producer e consumer sono indipendenti gli uni dagli altri, è possibile aggiornare un consumer senza causare alcun effetto sul producer. Inoltre, la topologia di messaggistica può evolversi senza influire sugli endpoint esistenti. Tratteremo questo in modo più approfondito quando si parlerà di pubblicazione/sottoscrizione.

## <a name="show-me-the-code"></a>Mostra il codice
Nella sezione seguente viene illustrato come utilizzare il Bus di servizio per costruire l'applicazione.

### <a name="sign-up-for-an-azure-account"></a>Iscriversi a un account Azure
È necessario un account Azure per iniziare a lavorare con il Bus di servizio. Se non si ha già una sottoscrizione, è possibile registrarsi per un account gratuito [qui](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Creare uno spazio dei nomi
Dopo aver creato una sottoscrizione, è possibile [creare un nuovo spazio dei nomi](service-bus-create-namespace-portal.md). Ogni spazio dei nomi funge da contenitore di ambito per un set di entità del Bus di servizio. Assegnare al nuovo spazio dei nomi un nome univoco tra tutti gli account del Bus di servizio. 

### <a name="install-the-nuget-package"></a>Installare il pacchetto NuGet
Per utilizzare lo spazio dei nomi del Bus di servizio, un'applicazione deve fare riferimento all'assembly del Bus di servizio, in particolare a Microsoft.ServiceBus.dll. È possibile trovare questa assembly come parte di Microsoft Azure SDK, e il download è disponibile nella [pagina di download di Azure SDK](https://azure.microsoft.com/downloads/). Il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è tuttavia il modo più semplice per recuperare l'interfaccia API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

### <a name="create-the-queue"></a>Creare la coda
Le operazioni di gestione per entità di messaggistica (code e argomenti di pubblicazione/sottoscrizione) del bus di servizio vengono eseguite tramite la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Il bus di servizio usa un modello di sicurezza basato su [SAS (Shared Access Signature, Firma di accesso condiviso)](service-bus-sas-overview.md). La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. Per memorizzare le credenziali SAS, verrà usato un metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx). L'istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene costruita con l'indirizzo di base dello spazio dei nomi del bus di servizio e con il provider di token.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) offre i metodi per creare, enumerare ed eliminare entità di messaggistica. Il codice riportato di seguito illustra come l'istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene creata e usata per creare la coda **DataCollectionQueue**.

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

### <a name="send-messages-to-the-queue"></a>Inviare messaggi alla coda
Per le operazioni di runtime su entità del bus di servizio, ad esempio l'invio e la ricezione di messaggi, un'applicazione deve prima creare un oggetto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Simile alla classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), l'istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) viene creata a partire dall'indirizzo di base dello spazio dei nomi del servizio e dal provider di token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Questa classe consiste di un insieme di proprietà standard, (ad esempio, [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), di un dizionario usato per mantenere le proprietà dell'applicazione e di un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo passando qualsiasi oggetto serializzabile (nell'esempio seguente passa un oggetto **SalesData** che rappresenta i dati di vendita dal terminale POS), che userà [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) per serializzare l'oggetto. In alternativa, può essere specificato un oggetto [Flusso](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

Il modo più semplice per inviare messaggi a una determinata coda, in questo caso la coda **DataCollectionQueue**, consiste nell'usare [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) per creare un oggetto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) direttamente dall'istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Ricezione di messaggi dalla coda
Per ricevere messaggi dalla coda è possibile usare un oggetto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) che può essere creato direttamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) tramite [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). I ricevitori dei messaggi possono lavorare in due modalità diverse: **ReceiveAndDelete** e **PeekLock**. La modalità [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) viene impostata quando si crea il ricevitore del messaggio, come parametro per la chiamata [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase, ovvero quando il bus di servizio riceve la richiesta, contrassegna il messaggio come letto e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** rappresenta il modello più semplice ed è adatta a scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come consumato, quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, il messaggio consumato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve la richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per successive elaborazioni, l'applicazione esegue la seconda fase del processo di ricezione chiamando [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. Quando il bus di servizio vede la chiamata [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), contrassegna il messaggio come letto.

Sono possibili altri due risultati. In primo luogo, se per qualche motivo l'applicazione non riesce a elaborare il messaggio, può chiamare il metodo [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) sul messaggio ricevuto, (invece del metodo [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). In questo modo, il bus di servizio sbloccherà il messaggio che sarà disponibile per essere nuovamente ricevuto dallo stesso consumer o da un altro consumer concorrente. In secondo luogo, al blocco è associato un timeout e se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout (ad esempio a causa di un arresto anomalo), il bus di servizio sbloccherà il messaggio rendendolo nuovamente disponibile per la ricezione (eseguendo essenzialmente un'operazione [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) per impostazione predefinita).

Si noti che in caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), il messaggio verrà recapitato di nuovo all'applicazione al prossimo riavvio dell'applicazione. In questo caso si parla di elaborazione *Almeno una volta *. Indica che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, allora è necessaria una logica aggiuntiva nell'applicazione per rilevare i duplicati. Ciò può essere realizzato con la proprietà [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio. Il valore di questa proprietà rimane costante in tutti i tentativi di recapito. In questo caso si parla di elaborazione *Una sola volta*.

Il codice riportato di seguito riceve ed elabora un messaggio usando la modalità **PeekLock**, ovvero l'impostazione predefinita se non viene esplicitamente definito un valore per [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

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

### <a name="use-the-queue-client"></a>Utilizzare il client di coda
Negli esempi precedenti in questa sezione sono stati creati gli oggetti [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) e [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) direttamente dal [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) per inviare e ricevere rispettivamente messaggi dalla coda. Un approccio alternativo consiste nell'usare la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), che supporta operazioni di invio e ricezione, oltre a funzionalità più avanzate, ad esempio le sessioni.

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

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso le nozioni di base sulle code, vedere [Creare applicazioni che usano argomenti e sottoscrizioni del bus di servizio](service-bus-create-topics-subscriptions.md) per continuare questa discussione sulle funzionalità di pubblicazione/sottoscrizione degli argomenti e delle sottoscrizioni del bus di servizio.

<!--HONumber=Oct16_HO2-->


