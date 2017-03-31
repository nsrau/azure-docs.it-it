---
title: Eccezioni di messaggistica del bus di servizio di Azure | Microsoft Docs
description: Elenco delle eccezioni di messaggistica del bus di servizio e relative azioni consigliate.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 3b543b1c94122a037cdd3b16e25d60957add1cb7
ms.lasthandoff: 03/04/2017


---
# <a name="service-bus-messaging-exceptions"></a>Eccezioni di messaggistica del bus di servizio
Questo articolo elenca alcune delle eccezioni generate dall'API di messaggistica del bus di servizio di Microsoft Azure. Il documento è soggetto a modifica ed è quindi opportuno controllare periodicamente la presenza di aggiornamenti.

## <a name="exception-categories"></a>Categorie di eccezioni
Le API di messaggistica generano eccezioni che possono essere raggruppate nelle categorie seguenti e a ognuna delle quali è associata un'azione che è possibile eseguire per tentare di risolverla. Tenere presente che il significato e le cause di un'eccezione possono variare a seconda del tipo di entità di messaggistica, ovvero code/argomenti o Hub eventi:

1. Errore nella codifica dell'utente ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Azione generale: provare a correggere il codice prima di continuare.
2. Errore di configurazione/installazione ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Azione generale: controllare la configurazione e modificarla, se necessario.
3. Eccezioni temporanee ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Azione generale: ripetere l'operazione o inviare una notifica agli utenti.
4. Altre eccezioni ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)). Azione generale: specifica per il tipo di eccezione. Fare riferimento alla tabella nella sezione seguente. 

## <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| **Tipo di eccezione** | **Descrizione/Causa/Esempi** | **Azione suggerita** | **Nota sulla ripetizione automatica/immediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Il server non ha risposto all'operazione richiesta entro l'intervallo di tempo specificato, controllato dalla proprietà [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Il server può aver completato l'operazione richiesta. L'eccezione può verificarsi a causa di ritardi a livello di rete o di infrastruttura. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione. Vedere [Eccezioni di timeout](#timeoutexception). |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'operazione richiesta dall'utente non è consentita nell'ambito del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, il metodo [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) genera questa eccezione se il messaggio è stato ricevuto in modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . |Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. |Ripetere l'operazione non serve. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. |Controllare il codice e assicurarsi che non richiami operazioni su un oggetto eliminato. |Ripetere l'operazione non serve. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token oppure il token non è valido o non contiene le attestazioni necessarie per eseguire l'operazione. |Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o più argomenti forniti al metodo non sono validi.<br /> L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contiene segmenti di percorso.<br /> Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) non è valido. <br />Il valore della proprietà è maggiore di 32 KB. |Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. |Ripetere l'operazione non serve. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) |L'entità associata all'operazione non esiste o è stata eliminata. |Assicurarsi che l'entità esista. |Ripetere l'operazione non serve. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativo di ricevere un messaggio con un particolare numero di sequenza. Non è stato trovato questo messaggio. |Assicurarsi che il messaggio non sia già stato ricevuto. Controllare la coda dei messaggi non recapitabili per verificare che il messaggio non si trovi al suo interno. |Ripetere l'operazione non serve. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Il client non è in grado di stabilire una connessione con il bus di servizio. |Assicurarsi che il nome host fornito sia corretto e l'host sia raggiungibile. |Se sono presenti problemi di connettività intermittente, può essere utile ripetere l'operazione. |
| [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |In questo momento il servizio non è in grado di elaborare la richiesta. |Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. |Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception) |Il token di blocco associato al messaggio è scaduto o non è disponibile. |Eliminare il messaggio. |Ripetere l'operazione non serve. |
| [SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception) |È stato perso il blocco associato a questa sessione. |Interrompere l'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Ripetere l'operazione non serve. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Eccezione di messaggistica generica che può essere generata nei casi seguenti:<br /> È stato eseguito un tentativo di creare una classe [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando un nome o un percorso appartenente a un tipo di entità diverso, ad esempio un argomento.<br />  È stato eseguito un tentativo di inviare un messaggio di dimensioni superiori a 256 KB. Si è verificato un errore nel server o nel servizio durante l'elaborazione della richiesta. Per informazioni dettagliate, vedere il messaggio di eccezione. Si tratta in genere di un'eccezione temporanea. |Controllare il codice e verificare che per il corpo del messaggio siano stati usati solo oggetti serializzabili (oppure usare un serializzatore personalizzato). Consultare la documentazione per identificare i tipi di valori delle proprietà supportati e usare solo quelli. Controllare la proprietà [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Se è **true**, è possibile ripetere l'operazione. |Il comportamento di ripetizione dei tentativi non è definito e ripetere l'operazione può non essere utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |È stato eseguito un tentativo di creare un'entità con un nome già usato da un'altra entità dello stesso spazio dei nomi del servizio. |Eliminare l'entità esistente o scegliere un nome diverso per l'entità da creare. |Ripetere l'operazione non serve. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |L'entità di messaggistica ha raggiunto le dimensioni massime consentite oppure è stato superato il numero massimo di connessioni a uno spazio dei nomi. |Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. Vedere [QuotaExceededException](#quotaexceededexception). |Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Il bus di servizio restituisce questa eccezione se si tenta di creare un'azione di regola non valida. Il bus di servizio assegna questa eccezione a un messaggio non recapitabile se si verifica un errore durante l'elaborazione dell'azione di regola per il messaggio. |Verificare la correttezza dell'azione di regola. |Ripetere l'operazione non serve. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Il bus di servizio restituisce questa eccezione se si tenta di creare un filtro non valido. Il bus di servizio assegna questa eccezione a un messaggio non recapitabile se si verifica un errore durante l'elaborazione del filtro per il messaggio. |Verificare la correttezza del filtro. |Ripetere l'operazione non serve. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |È stato eseguito un tentativo di accettare una sessione con un ID di sessione specifico, ma la sessione è attualmente bloccata da un altro client. |Assicurarsi che la sessione venga sbloccata dagli altri client. |Se nel frattempo la sessione è stata rilasciata, può essere utile ripetere l'operazione. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Nella transazione sono presenti troppe operazioni. |Ridurre il numero di operazioni presenti nella transazione. |Ripetere l'operazione non serve. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |È stata inoltrata una richiesta per un'operazione di runtime su un'entità disattivata. |Attivare l'entità. |Se nel frattempo l'entità è stata attivata, può essere utile ripetere l'operazione. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Il bus di servizio restituisce questa eccezione se si invia un messaggio a un argomento in cui è abilitato il filtro preliminare e nessuno dei filtri corrisponde. |Assicurarsi che almeno un filtro corrisponda. |Ripetere l'operazione non serve. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Un payload del messaggio supera il limite di 256 KB. Il limite di 256 KB rappresenta le dimensioni totali del messaggio, che possono includere proprietà di sistema ed eventuali overhead .NET. |Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |La transazione di ambiente (*Transaction.Current*) non è valida. È possibile che nel frattempo sia stata interrotta o completata. L'eccezione interna può fornire informazioni aggiuntive. | |Ripetere l'operazione non serve. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |È stata tentata un'operazione su una transazione in dubbio oppure è stato tentato di eseguire il commit della transazione e la transazione è diventata in dubbio. |L'applicazione deve gestire questa eccezione (come caso speciale), poiché è possibile che sia già stato eseguito il commit della transazione. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

### <a name="queues-and-topics"></a>Code e argomenti
Per le code e gli argomenti, tale quota è spesso rappresentata dalle dimensioni della coda. La proprietà del messaggio di errore conterrà altri dettagli, come nell'esempio seguente:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Il messaggio indica che l'argomento ha superato il limite di dimensioni, in questo caso 1 GB (limite di dimensioni predefinito). 

### <a name="namespaces"></a>Spazi dei nomi

Per gli spazi dei nomi, [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) può indicare che un'applicazione ha superato il numero massimo di connessioni a uno spazio dei nomi. Ad esempio:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: la coda dei messaggi non recapitabili e l'interruzione da parte dei destinatari dei messaggi.

1. **Coda dei messaggi non recapitabili**
   : un lettore non riesce a completare i messaggi e questi vengono restituiti alla coda o all'argomento alla scadenza del blocco. Questa situazione può verificarsi se il lettore rileva un'eccezione che impedisce la chiamata a [BrokeredMessage.Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx). Dopo che un messaggio è stato letto 10 volte, si sposta alla coda dei messaggi non recapitabili per impostazione predefinita. Questo comportamento è controllato dalla proprietà [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) e ha un valore predefinito di 10. Man mano che i messaggi si accumulano nella coda dei messaggi non recapitabili, occupano spazio.
   
    Per risolvere il problema, leggere e completare i messaggi nella coda dei messaggi non recapitabili, come si farebbe per qualsiasi altra coda. La classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) contiene anche un metodo [FormatDeadLetterPath](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.formatdeadletterpath.aspx) per la formattazione del percorso della coda dei messaggi non recapitabili.
2. **Interruzione da parte del destinatario**
   : un destinatario ha interrotto la ricezione di messaggi da una coda o da una sottoscrizione. Per identificare questo problema, è necessario osservare la proprietà [QueueDescription.MessageCountDetails](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecountdetails.aspx) , che mostra la suddivisione completa dei messaggi. Se il valore della proprietà [ActiveMessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagecountdetails.activemessagecount.aspx) è elevato o in crescita, i messaggi non vengono più letti alla stessa velocità in cui vengono scritti.

### <a name="event-hubs"></a>Hub eventi
Hub eventi ha un limite di 20 gruppi di utenti per Hub eventi. Quando si tenta di creare più gruppi, si riceve un'eccezione di tipo [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Controllare il valore della proprietà [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), in quanto il raggiungimento di questo limite può anche causare un evento di [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Code e argomenti
Per le code e gli argomenti, il timeout è specificato nella proprietà [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout), come parte della stringa di connessione, o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

### <a name="event-hubs"></a>Hub eventi
Per Hub eventi, il timeout è specificato come parte della stringa di connessione o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: una configurazione errata o un errore temporaneo del servizio.

1. **Configurazione errata**
   : il valore di timeout dell'operazione potrebbe essere troppo ridotto per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il codice contiene un valore troppo piccolo. Si noti che la condizione relativa all'utilizzo della rete e della CPU può influire sul tempo necessario per il completamento di una determinata operazione, pertanto il timeout dell'operazione non deve essere impostato su un valore troppo piccolo.
2. **Errore temporaneo del servizio**
   : a volte il servizio del bus di servizio può subire ritardi nell'elaborazione delle richieste, ad esempio durante periodi di traffico elevato. In questi casi, è possibile ritentare l'operazione dopo un ritardo fino a quando l'operazione ha esito positivo. Se la stessa operazione ha ancora esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni complete sull'API del bus di servizio e di Hub eventi per .NET, vedere [Azure .NET API reference](/dotnet/api/) (Informazioni di riferimento sulle API di Azure per .NET).

Per altre informazioni sul [bus di servizio](https://azure.microsoft.com/services/service-bus/), vedere gli argomenti seguenti.

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Architettura del bus di servizio](service-bus-architecture.md)


