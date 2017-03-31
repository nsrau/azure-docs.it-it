---
title: Eccezioni del servizio di inoltro di Microsoft Azure e relativa risoluzione | Microsoft Docs
description: Elenco delle eccezioni di inoltro di Azure e relative azioni consigliate.
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: bc71995deef3f9795ebd33d9642c7ffa1995021b
ms.openlocfilehash: 346851226dce8ca8e48d3ae291b97e6e43d1ae03
ms.lasthandoff: 02/02/2017


---
# <a name="relay-exceptions"></a>Eccezioni di inoltro

Questo articolo elenca alcune delle eccezioni generate dalle API di inoltro di Microsoft Azure. Il documento è soggetto a modifica ed è quindi opportuno controllare periodicamente la presenza di aggiornamenti.

## <a name="exception-categories"></a>Categorie di eccezioni

Le API di inoltro generano eccezioni raggruppabili nelle categorie seguenti, a ciascuna delle quali è associata un'azione eseguibile per tentare di risolverla.

1. Errore nella codifica dell'utente ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Azione generale: provare a correggere il codice prima di continuare.
2. Errore di configurazione/installazione ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Azione generale: controllare la configurazione e modificarla, se necessario.
3. Eccezioni temporanee ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Azione generale: ripetere l'operazione o inviare una notifica agli utenti.
4. Altre eccezioni ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)). Azione generale: specifica per il tipo di eccezione. Fare riferimento alla tabella nella sezione seguente. 

## <a name="exception-types"></a>Tipi di eccezioni

La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| **Tipo di eccezione** | **Descrizione** | **Azione suggerita** | **Nota sulla ripetizione automatica o immediata** |
| --- | --- | --- | --- |
| [Timeout](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Il server non ha risposto all'operazione richiesta entro l'intervallo di tempo specificato, controllato dalla proprietà [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Il server può aver completato l'operazione richiesta. L'eccezione può verificarsi a causa di ritardi a livello di rete o di infrastruttura. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione. Vedere [Eccezioni di timeout](#timeoutexception). |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [Operazione non valida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'operazione richiesta dall'utente non è consentita nell'ambito del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, il metodo [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) genera questa eccezione se il messaggio è stato ricevuto in modalità **ReceiveAndDelete** . |Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. |Ripetere l'operazione non serve. |
| [Operazione annullata](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. |Controllare il codice e assicurarsi che non richiami operazioni su un oggetto eliminato. |Ripetere l'operazione non serve. |
| [Accesso non autorizzato](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token oppure il token non è valido o non contiene le attestazioni necessarie per eseguire l'operazione. |Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [Eccezione di argomento](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argomento Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Argomento non compreso nell'intervallo](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o più argomenti forniti al metodo non sono validi.<br /> L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contiene segmenti di percorso.<br /> Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) non è valido. <br />Il valore della proprietà è maggiore di 32 KB. |Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. |Ripetere l'operazione non serve. |
| [Server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |In questo momento il servizio non è in grado di elaborare la richiesta. |Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. |Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [Quota superata](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |L'entità di messaggistica ha raggiunto le dimensioni massime consentite. |Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. Vedere [QuotaExceededException](#quotaexceededexception). |Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [Dimensione del messaggio superata](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Un payload del messaggio supera il limite di 256 KB. Il limite di 256 KB è la dimensione totale del messaggio, che può includere proprietà di sistema ed eventuali overhead .NET. |Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

Per l'inoltro, questa eccezione esegue il wrapping di [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), il quale indica che è stato superato il numero massimo di listener per questo endpoint. Questo numero è indicato nel valore **MaximumListenersPerEndpoint** del messaggio di eccezione.

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Controllare il valore della proprietà [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), in quanto il raggiungimento di questo limite può anche causare un evento di [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Per l'inoltro, è possibile ricevere eccezioni di timeout alla prima apertura di una connessione di inoltro del mittente. Questa eccezione può essere dovuta a due cause comuni:

1. Il valore [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) è troppo piccolo (anche di una frazione di secondo).
2. Uno o più listener di inoltro locali non sono reattivi (o incontrano problemi di regole firewall che non consentono ai listener di accettare nuove connessioni client) e il valore [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) è minore di circa 20 secondi.

Ad esempio:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: una configurazione errata o un errore temporaneo del servizio.

1. **Configurazione errata**
   : il valore di timeout dell'operazione potrebbe essere troppo ridotto per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il codice contiene un valore troppo piccolo. Si noti che la condizione relativa all'utilizzo della rete e della CPU può influire sul tempo necessario per il completamento di una determinata operazione, pertanto il timeout dell'operazione non deve essere impostato su un valore troppo piccolo.
2. **Errore temporaneo del servizio**
   : a volte il servizio di inoltro può subire ritardi nell'elaborazione delle richieste, ad esempio durante periodi di traffico elevato. In questi casi, è possibile ritentare l'operazione dopo un ritardo fino a quando l'operazione ha esito positivo. Se la stessa operazione ha ancora esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

## <a name="next-steps"></a>Passaggi successivi:
* [Domande frequenti sul servizio di inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)


