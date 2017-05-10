---
title: Eccezioni di messaggistica di Hub eventi in Azure| Microsoft Docs
description: Elenco delle eccezioni di messaggistica di Hub eventi di Azure e relative azioni consigliate.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: ab0df01d637c15b05c2d83f1936a0154118034f7
ms.openlocfilehash: f88c4914478c3adf823fc22a0e049e73fb43e8db
ms.contentlocale: it-it
ms.lasthandoff: 02/08/2017


---
# <a name="event-hubs-messaging-exceptions"></a>Eccezioni della messaggistica di Hub eventi
Questo articolo elenca alcune eccezioni generate dall'API di messaggistica del bus di servizio di Azure, che include Hub eventi. Il documento è soggetto a modifica ed è quindi opportuno controllare periodicamente la presenza di aggiornamenti.

## <a name="exception-categories"></a>Categorie di eccezioni
Le API di Hub eventi generano eccezioni che possono essere raggruppate nelle categorie seguenti e a ognuna delle quali è associata un'azione che è possibile eseguire per tentare di risolverla.

1. Errore nella codifica dell'utente: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Azione generale: provare a correggere il codice prima di continuare.
2. Errore di configurazione/installazione: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Azione generale: controllare la configurazione e modificarla, se necessario.
3. Eccezioni temporanee: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Azione generale: ripetere l'operazione o inviare una notifica agli utenti.
4. Altre eccezioni: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Azione generale: specifica per il tipo di eccezione. Fare riferimento alla tabella nella sezione seguente. 

## <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| **Tipo di eccezione** | **Descrizione/Causa/Esempi** | **Azione suggerita** | **Nota sulla ripetizione automatica/immediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Il server non ha risposto all'operazione richiesta entro l'intervallo di tempo specificato, controllato dalla proprietà [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Il server può aver completato l'operazione richiesta. L'eccezione può verificarsi a causa di ritardi a livello di rete o di infrastruttura. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione.<br /> Vedere [TimeoutException](#timeoutexception). |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'operazione richiesta dall'utente non è consentita nell'ambito del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) genera questa eccezione se il messaggio è stato ricevuto in modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . |Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. |Ripetere l'operazione non serve. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. |Controllare il codice e assicurarsi che non richiami operazioni su un oggetto eliminato. |Ripetere l'operazione non serve. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token oppure il token non è valido o non contiene le attestazioni necessarie per eseguire l'operazione. |Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o più argomenti forniti al metodo non sono validi. L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contiene segmenti di percorso. Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) non è valido. Il valore della proprietà è maggiore di 32 KB. |Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. |Ripetere l'operazione non serve. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |L'entità associata all'operazione non esiste o è stata eliminata. |Assicurarsi che l'entità esista. |Ripetere l'operazione non serve. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativo di ricevere un messaggio con un particolare numero di sequenza. Non è stato trovato questo messaggio. |Assicurarsi che il messaggio non sia già stato ricevuto. Controllare la coda dei messaggi non recapitabili per verificare che il messaggio non si trovi al suo interno. |Ripetere l'operazione non serve. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Il client non riesce a stabilire una connessione con Hub eventi. |Assicurarsi che il nome host fornito sia corretto e l'host sia raggiungibile. |Se sono presenti problemi di connettività intermittente, può essere utile ripetere l'operazione. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |In questo momento il servizio non è in grado di elaborare la richiesta. |Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. <br /> Vedere [ServerBusyException](#serverbusyexception). |Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception) |Il token di blocco associato al messaggio è scaduto o non è disponibile. |Eliminare il messaggio. |Ripetere l'operazione non serve. |
| [SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception) |È stato perso il blocco associato a questa sessione. | Interrompere l'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Ripetere l'operazione non serve. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Eccezione di messaggistica generica che può essere generata nei casi seguenti: È stato eseguito un tentativo di creare una classe [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando un nome o un percorso appartenente a un tipo di entità diverso, ad esempio un argomento. È stato eseguito un tentativo di inviare un messaggio di dimensioni superiori a 256 KB. Si è verificato un errore nel server o nel servizio durante l'elaborazione della richiesta. Per informazioni dettagliate, vedere il messaggio di eccezione. Si tratta in genere di un'eccezione temporanea. |Controllare il codice e verificare che per il corpo del messaggio siano stati usati solo oggetti serializzabili (oppure usare un serializzatore personalizzato). Consultare la documentazione per identificare i tipi di valori delle proprietà supportati e usare solo quelli. Controllare la proprietà [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Se è **true**, è possibile ripetere l'operazione. |Il comportamento di ripetizione dei tentativi non è definito e ripetere l'operazione può non essere utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |È stato eseguito un tentativo di creare un'entità con un nome già usato da un'altra entità dello stesso spazio dei nomi del servizio. |Eliminare l'entità esistente o scegliere un nome diverso per l'entità da creare. |Ripetere l'operazione non serve. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |L'entità di messaggistica ha raggiunto le dimensioni massime consentite. Questa situazione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori, ovvero 5. |Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. <br /> Vedere [QuotaExceededException](#quotaexceededexception) |Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
|  | | | |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |È stato eseguito un tentativo di accettare una sessione con un ID di sessione specifico, ma la sessione è attualmente bloccata da un altro client. |Assicurarsi che la sessione venga sbloccata dagli altri client. |Se nel frattempo la sessione è stata rilasciata, può essere utile ripetere l'operazione. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Nella transazione sono presenti troppe operazioni. |Ridurre il numero di operazioni presenti nella transazione. |Ripetere l'operazione non serve. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |È stata inoltrata una richiesta per un'operazione di runtime su un'entità disattivata. |Attivare l'entità. |Se nel frattempo l'entità è stata attivata, può essere utile ripetere l'operazione. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Un payload del messaggio supera il limite di 256 KB. Il limite di 256 KB è la dimensione totale del messaggio, che può includere proprietà di sistema ed eventuali overhead .NET. |Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |La transazione di ambiente (*Transaction.Current*) non è valida. È possibile che nel frattempo sia stata interrotta o completata. L'eccezione interna può fornire informazioni aggiuntive. | |Ripetere l'operazione non serve. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |È stata tentata un'operazione su una transazione in dubbio oppure è stato tentato di eseguire il commit della transazione e la transazione è diventata in dubbio. |L'applicazione deve gestire questa eccezione (come caso speciale), poiché è possibile che sia già stato eseguito il commit della transazione. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

Questa situazione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di&5; ricevitori.

### <a name="event-hubs"></a>Hub eventi
Hub eventi ha un limite di 20 gruppi di utenti per Hub eventi. Quando si tenta di creare più gruppi, si riceve un'eccezione di tipo [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Per Hub eventi, il timeout è specificato come parte della stringa di connessione o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: una configurazione errata o un errore temporaneo del servizio.

1. **Configurazione errata**
   : il valore di timeout dell'operazione potrebbe essere troppo ridotto per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il codice contiene un valore troppo piccolo. Si noti che la condizione relativa all'utilizzo della rete e della CPU può influire sul tempo necessario per il completamento di una determinata operazione, pertanto il timeout dell'operazione non deve essere impostato su un valore troppo piccolo.
2. **Errore temporaneo del servizio**
   : a volte il servizio di Hub eventi può subire ritardi nell'elaborazione delle richieste, ad esempio durante periodi di traffico elevato. In questi casi, è possibile ritentare l'operazione dopo un ritardo fino a quando l'operazione ha esito positivo. Se la stessa operazione ha ancora esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) o [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indicano che il server è sovraccarico. Esistono due codici di errore relativi a questa eccezione.

### <a name="error-code-50002"></a>Codice di errore 50002

Questo errore può verificarsi per uno dei due motivi:

1. Il carico non viene distribuito uniformemente su tutte le partizioni nell'Hub eventi e una partizione raggiunge il limite dell'unità di velocità effettiva locale.
    
    Risoluzione: rivedere la strategia di distribuzione della partizione o provare con [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_).

2. Lo spazio dei nomi di Hub eventi non dispone di unità di velocità effettiva sufficienti (per conferma, è possibile controllare il pannello **Metrica** nel pannello dello spazio dei nomi di Hub eventi nel [Portale di Azure](https://portal.azure.com)). Si noti che il portale mostra le informazioni aggregate (1 minuto), ma viene misurata la velocità effettiva in tempo reale, quindi si tratta solo di una stima.

    Risoluzione: aumentare le unità di velocità effettiva nello spazio dei nomi. È possibile farlo nel portale, nel pannello **Scalabilità** del pannello di spazio dei nomi di Hub eventi.

### <a name="error-code-50001"></a>Codice errore 50001

Questo errore si verifica raramente. Si verifica quando il contenitore che esegue il codice per lo spazio dei nomi è insufficiente per la CPU, occorrono pochi secondi prima che il bilanciamento del carico dell'Hub eventi inizi.


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

