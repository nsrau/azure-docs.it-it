---
title: Eccezioni del servizio di inoltro di Azure e relativa risoluzione | Microsoft Docs
description: Elenco di eccezioni del servizio di inoltro di Azure e azioni consigliate per la risoluzione.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a644dfe80255c64980400866a5e3d197f75375bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87532969"
---
# <a name="azure-relay-exceptions"></a>Eccezioni del servizio di inoltro di Azure

Questo articolo elenca alcune eccezioni generate dalle API di inoltro di Azure. Il documento è soggetto a modifica ed è quindi opportuno controllare periodicamente la presenza di aggiornamenti.

## <a name="exception-categories"></a>Categorie di eccezioni

Le eccezioni generate dalle API di inoltro rientrano nelle categorie seguenti. Vengono consigliate anche le azioni per la risoluzione delle eccezioni.

*   **Errore nella codifica dell'utente**: [System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1), [System.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1), [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1), [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1). 

    **Azione generale**: provare a correggere il codice prima di continuare.
*   **Errore di installazione/configurazione**: [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1). 

    **Azione generale**: controllare la configurazione. Se necessario, modificare la configurazione.
*   **Eccezioni temporanee**: [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Azione generale**: ripetere l'operazione o inviare una notifica agli utenti.
*   **Altre eccezioni**: [System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1), [System.TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1). 

    **Azione generale**: specifica del tipo di eccezione. Vedere la tabella nella sezione seguente. 

## <a name="exception-types"></a>Tipi di eccezioni

La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause e consiglia l'azione da eseguire.

| **Tipo di eccezione** | **Descrizione** | **Azione suggerita** | **Nota sulla ripetizione automatica o immediata** |
| --- | --- | --- | --- |
| [Timeout](/dotnet/api/system.timeoutexception?view=netcore-3.1) |Il server non ha risposto all'operazione richiesta entro l'intervallo di tempo specificato, controllato dalla proprietà [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Il server può aver completato l'operazione richiesta. L'eccezione può verificarsi a causa di ritardi a livello di rete o di infrastruttura. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione. Vedere [TimeoutException](#timeoutexception). |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [Operazione non valida](/dotnet/api/system.invalidoperationexception?view=netcore-3.1) |L'operazione richiesta dall'utente non è consentita nell'ambito del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. |Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. |Ripetere l'operazione non serve. |
| [Operazione annullata](/dotnet/api/system.operationcanceledexception?view=netcore-3.1) |È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. |Controllare il codice e assicurarsi che non richiami operazioni su un oggetto eliminato. |Ripetere l'operazione non serve. |
| [Accesso non autorizzato](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1) |L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token oppure il token non è valido o non contiene le attestazioni necessarie per eseguire l'operazione. |Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [Eccezione di argomento](/dotnet/api/system.argumentexception?view=netcore-3.1)<br /> [Argomento Null](/dotnet/api/system.argumentnullexception?view=netcore-3.1)<br />[Argomento non compreso nell'intervallo](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1) |Si è verificato uno o più dei seguenti casi:<br />Uno o più argomenti forniti al metodo non sono validi.<br /> L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o a [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contiene uno o più segmenti di percorso.<br />Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) non è valido. <br />Il valore della proprietà è maggiore di 32 KB. |Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. |Ripetere l'operazione non serve. |
| [Server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |In questo momento il servizio non è in grado di elaborare la richiesta. |Il client può attendere per un certo periodo di tempo, quindi è opportuno ripetere l'operazione. |Il client può eseguire nuovi tentativi dopo un intervallo specifico. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [Quota superata](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |L'entità di messaggistica ha raggiunto le dimensioni massime consentite. |Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. Vedere [QuotaExceededException](#quotaexceededexception). |Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [Dimensione del messaggio superata](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Un payload del messaggio supera il limite di 256 KB. Si noti che il limite di 256 KB è la dimensione totale del messaggio. La dimensione totale dei messaggi può includere proprietà di sistema ed eventuali overhead .NET. |Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

Per l'inoltro, questa eccezione esegue il wrapping di [System.ServiceModel.QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception?view=dotnet-plat-ext-3.1), il quale indica che è stato superato il numero massimo di listener per questo endpoint. Questo numero è indicato nel valore **MaximumListenersPerEndpoint** del messaggio di eccezione.

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Controllare il valore della proprietà [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=netcore-3.1#System_Net_ServicePointManager_DefaultConnectionLimit). Il raggiungimento di questo limite può causare anche un [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1).

Per l'inoltro è possibile ricevere eccezioni di timeout alla prima apertura di una connessione di inoltro del mittente. Questa eccezione può essere dovuta a due cause comuni:

*   Il valore [OpenTimeout](/previous-versions/) è troppo piccolo (anche di una frazione di secondo).
* Uno o più listener di inoltro locali non sono reattivi o si verificano problemi di regole firewall che non consentono ai listener di accettare nuove connessioni client e il valore [OpenTimeout](/previous-versions/) è minore di circa 20 secondi.

Esempio:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Cause comuni
Questo errore può essere dovuto a due cause comuni:

*   **Configurazione non corretta**
    
    Il valore di timeout dell'operazione potrebbe essere troppo ridotto per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il valore nel codice è impostato su un valore troppo piccolo. Si noti che l'uso della CPU e la condizione della rete possono incidere sul tempo necessario per completare un'operazione. È consigliabile non impostare il timeout dell'operazione su un valore molto basso.
*   **Errore temporaneo del servizio**

    In alcuni casi per il servizio di inoltro potrebbero verificarsi ritardi nell'elaborazione delle richieste. Questo può accadere, ad esempio, durante i periodi di traffico elevato. In questi casi, ritentare l'operazione dopo un ritardo fino a quando l'operazione non ha esito positivo. Se la stessa operazione ha ancora esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti sul servizio di inoltro di Azure](relay-faq.md)
* [Creare uno spazio dei nomi di inoltro](relay-create-namespace-portal.md)
* [Introduzione al servizio di inoltro di Azure e .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione al servizio di inoltro di Azure e al nodo](relay-hybrid-connections-node-get-started.md)
