<properties 
   pageTitle="Spazi dei nomi associati del bus di servizio | Microsoft Azure"
   description="Dettagli di implementazione e costi relativi allo spazio dei nomi associato"
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
   ms.date="12/28/2015"
   ms.author="sethm" />

# Dettagli di implementazione e implicazioni in termini di costi relativi allo spazio dei nomi associato

Il metodo [PairNamespaceAsync][], mediante un'istanza di [SendAvailabilityPairedNamespaceOptions][], esegue attività visibili per conto dell'utente. Poiché l'uso di questa funzionalità comporta considerazioni in termini di costi, è utile comprendere le attività eseguite in modo da prevederne il comportamento. L'API attiva automaticamente il comportamento seguente per conto dell'utente:

-   Creazione di code di backlog.
-   Creazione di un oggetto [MessageSender][] che comunica con code o argomenti.
-   Invio di messaggi ping a un'entità di messaggistica che diventa non disponibile, nel tentativo di rilevare il momento in cui questa diventa nuovamente disponibile.
-   Creazione facoltativa di un set di message pump che spostano i messaggi dalle code di backlog a quelle primarie.
-   Coordinamento di eventi di chiusura o errore delle istanze di [MessagingFactory][] primarie e secondarie.

A livello generale, la funzionalità opera in questo modo: quando l'entità primaria è integra, non si verificano modifiche del comportamento. Quando la durata di [FailoverInterval][] scade e l'entità primaria non rileva invii riusciti dopo un'eccezione di tipo [MessagingException][] o [TimeoutException][] non temporanea, si verifica quanto segue:

1.  Le operazioni di invio all'entità primaria vengono disabilitate e il sistema effettua il ping dell'entità primaria finché i ping non vengono recapitati correttamente.

2.  Viene selezionata una coda di backlog casuale.

3.  Gli oggetti [BrokeredMessage][] vengono instradati alla coda di backlog scelta.

1.  Se un'operazione di invio alla coda di backlog scelta non riesce, la coda viene estratta dalla rotazione e viene selezionata una nuova coda. L'errore viene comunicato a tutti i mittenti nell'istanza di [MessagingFactory][].

Le figure seguenti illustrano la sequenza. Prima di tutto il mittente invia i messaggi.

![Spazi dei nomi associati][0]

Dal momento in cui si verifica l'errore di invio alla coda primaria, il mittente inizia a inviare i messaggi a una coda di backlog scelta casualmente. Contemporaneamente avvia un'attività di ping.

![Spazi dei nomi associati][1]

A questo punto i messaggi si trovano ancora nella coda secondaria e non sono stati recapitati a quella primaria. Quando la coda primaria sarà di nuovo integra, è necessario che il sifone venga eseguito da almeno un processo. Il sifone recapita i messaggi dalle varie code di backlog alle entità di destinazione appropriate, ad esempio code e argomenti.

![Spazi dei nomi associati][2]

Nelle sezioni successive di questo argomento verranno illustrati i dettagli specifici del funzionamento di questi componenti.

## Creazione di code di backlog

L'oggetto [SendAvailabilityPairedNamespaceOptions][] passato al metodo [PairNamespaceAsync][] indica il numero di code di backlog da usare. Ogni coda di backlog viene quindi creata con le proprietà seguenti impostate in modo esplicito. Tutti gli altri valori vengono impostati in base all'oggetto [QueueDescription][] predefinito:

| Path | [spazio dei nomi primario]/x-servicebus-transfer/[indice] dove [indice] è un valore espresso in [0, BacklogQueueCount] |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes | 5120 |
| MaxDeliveryCount | int.MaxValue |
| DefaultMessageTimeToLive | TimeSpan.MaxValue |
| AutoDeleteOnIdle | TimeSpan.MaxValue |
| LockDuration | 1 minuto |
| EnableDeadLetteringOnMessageExpiration | true |
| EnableBatchedOperations | true |

Ad esempio, la prima coda di backlog creata per lo spazio dei nomi **contoso** è denominata `contoso/x-servicebus-transfer/0`.

Quando si creano le code, il codice verifica prima di tutto se una coda esiste. In caso contrario, la coda viene creata. Il codice non esegue la pulizia di code di backlog "aggiuntive". In particolare, se per l'applicazione con lo spazio dei nomi primario **contoso** sono necessarie cinque code di backlog, ma esiste già una coda con percorso `contoso/x-servicebus-transfer/7`, la coda aggiuntiva risulta presente, ma non viene usata. Il sistema consente in modo esplicito la presenza di code di backlog aggiuntive che tuttavia non vengono usate. Come proprietario dello spazio dei nomi, l'utente è responsabile della pulizia di eventuali code di backlog inutilizzate o indesiderate. Questa decisione è dovuta al fatto che il bus di servizio non è in grado di riconoscere lo scopo dell'esistenza di tutte le code nello spazio dei nomi. Se inoltre una coda con il nome specificato esiste già, ma NON soddisfa quanto definito in precedenza per l'oggetto [QueueDescription][], la modifica del comportamento predefinito dipende esclusivamente dai motivi definiti dall'utente. Non vengono fornite garanzie riguardo alle modifiche apportate alle code di backlog dal codice dell'utente. Verificare quindi con attenzione le modifiche apportate.

## MessageSender personalizzato

Quando vengono inviati, tutti i messaggi passano attraverso un oggetto [MessageSender][] interno, che ha un comportamento normale quando tutto funziona correttamente, ma che esegue il reindirizzamento alle code di backlog in caso di errori. Se viene rilevato un errore non temporaneo, viene avviato un timer. Dopo un periodo definito in [TimeSpan][] corrispondente al valore della proprietà [FailoverInterval][], durante il quale non viene eseguito l'invio di messaggi, viene avviata la procedura di failover. A questo punto, per ogni entità si verifica quanto segue:

- Viene eseguita un'attività ping per ogni [PingPrimaryInterval][], per verificare se l'entità è disponibile. Se questa attività riesce, tutto il codice client che usa l'entità inizia immediatamente a inviare nuovi messaggi allo spazio dei nomi primario.

- Le richieste successive di invio alla stessa entità da qualsiasi altro mittente avranno come risultato l'invio di un oggetto [BrokeredMessage][] che dovrà essere modificato per entrare nella coda di backlog. La modifica comporta la rimozione di alcune proprietà dall'oggetto [BrokeredMessage][] e la relativa archiviazione in un'altra area. Le proprietà seguenti vengono eliminate e aggiunte con un nuovo alias, per consentire al bus di servizio e all'SDK di elaborare i messaggi in modo uniforme:

| Nome proprietà precedente | Nuovo nome proprietà |
|-------------------------|-------------------|
| SessionId | x-ms-sessionid |
| TimeToLive | x-ms-timetolive |
| ScheduledEnqueueTimeUtc | x-ms-path |

Il percorso di destinazione originale viene archiviato anche nel messaggio come proprietà x-ms-path. Questa progettazione consente la coesistenza in un'unica coda di backlog di messaggi appartenenti a più entità. Le proprietà vengono ritrasferite dal sifone.

Per l'oggetto [MessageSender][] possono verificarsi problemi quando i messaggi raggiungono il limite di 256 KB e viene avviato il failover. L'oggetto [MessageSender][] personalizzato archivia nelle code di backlog i messaggi per tutte le code e tutti gli argomenti. Questo oggetto unisce i messaggi provenienti da numerose code primarie nelle code di backlog. Per gestire il bilanciamento del carico tra più client che non hanno relazioni tra loro, l'SDK sceglie casualmente una coda di backlog per ogni oggetto [QueueClient][] o [TopicClient][] creato nella coda.

## Ping

Un messaggio ping è un oggetto [BrokeredMessage][] vuoto con la proprietà [ContentType][] impostata su application/vnd.ms-servicebus-ping e un valore [TimeToLive][] di 1 secondo. Questo ping presenta una caratteristica particolare nel bus di servizio: il server non recapita mai un ping quando un chiamante qualsiasi richiede un oggetto [BrokeredMessage][]. Di conseguenza, non sarà mai necessario comprendere come ricevere e ignorare questi messaggi. A ogni entità (argomento o coda univoca) per istanza di [MessagingFactory][] e per client che risulta non disponibile viene inviato un ping. Per impostazione predefinita, ciò accade una volta al minuto. I messaggi ping sono considerati come normali messaggi del bus di servizio e possono comportare l'addebito di costi in base alla larghezza di banda e al numero di messaggi. Non appena i client rilevano che il sistema è disponibile, l'invio di messaggi viene arrestato.

## Sifone

Almeno un programma eseguibile nell'applicazione deve eseguire attivamente il sifone, che avvia un'operazione di ricezione mediante polling prolungato della durata di 15 minuti. Quando tutte le entità sono disponibili e sono presenti dieci code di backlog, l'applicazione che ospita il sifone chiama l'operazione di ricezione 40 volte all'ora, 960 volte al giorno e 28800 volte in 30 giorni. Quando il sifone sposta attivamente i messaggi dalla coda di backlog a quella primaria, per ciascun messaggio vengono applicati i costi riportati di seguito (costi standard relativi alle dimensioni del messaggio e all'impiego delle larghezza di banda nelle varie fasi):

1.  Invio alla coda di backlog.

2.  Ricezione dalla coda di backlog.

3.  Invio alla coda primaria.

4.  Ricezione dalla coda primaria.

## Comportamento di chiusura o errore

In un'applicazione che ospita il sifone, non appena l'oggetto [MessagingFactory][] primario o secondario restituisce un errore o viene chiuso, senza che l'oggetto correlato restituisca un errore o venga chiuso, e non appena questo stato viene rilevato, il sifone si attiva automaticamente. Se l'oggetto [MessagingFactory][] correlato al primo non viene chiuso entro cinque secondi, il sifone restituisce un errore per l'oggetto [MessagingFactory][] ancora aperto.

## Passaggi successivi

Per informazioni dettagliate sulla messaggistica asincrona del bus di servizio, vedere [Modelli di messaggistica asincrona e disponibilità elevata].

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Modelli di messaggistica asincrona e disponibilità elevata]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png

<!---HONumber=AcomDC_0107_2016-->