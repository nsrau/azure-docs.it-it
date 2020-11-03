---
title: Hub eventi di Azure-eccezioni (legacy)
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica di Hub eventi di Azure e le relative azioni consigliate.
ms.topic: article
ms.date: 11/02/2020
ms.openlocfilehash: adaf7242530727a1f77a9662110a43341e57e80a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289329"
---
# <a name="event-hubs-messaging-exceptions---net-legacy"></a>Eccezioni di messaggistica di hub eventi-.NET (legacy)
Questa sezione elenca le eccezioni .NET generate dalle API .NET Framework. 

> [!IMPORTANT]
> Alcune delle eccezioni elencate nell'articolo si applicano solo alla libreria .NET legacy di hub eventi. Ad esempio: Microsoft. ServiceBus. * Exceptions.
> 
> Per informazioni sui EventHubsException generati dalla nuova libreria .NET, vedere [EventHubsException-.NET](exceptions-dotnet.md)

## <a name="exception-categories"></a>Categorie di eccezioni

Le API .NET di hub eventi generano eccezioni che possono rientrare nelle categorie seguenti, insieme all'azione associata che è possibile eseguire per tentare di correggerle:

 - Errore di codifica utente: 
 
   - [System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)
   - [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true)
   - [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true)
   - [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)
   
   Azione generale: provare a correggere il codice prima di procedere.
 
 - Errore di installazione/configurazione: 
 
   - [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true)
   
   Azione generale: controllare la configurazione e modificare se necessario.
   
 - Eccezioni temporanee: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception)
   - [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception)
   - [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Azione generale: ripetere l'operazione o inviare una notifica agli utenti.
 
 - Altre eccezioni: 
 
   - [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true)
   - [System.TimeoutException](#timeoutexception)
   - [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Azione generale: specifica del tipo di eccezione; vedere la tabella nella sezione seguente. 

## <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| Tipo di eccezione | Descrizione/Causa/Esempi | Azione suggerita | Nota sulla ripetizione automatica/immediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |Il server non ha risposto all'operazione richiesta entro il tempo specificato, che è controllato da [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Il server può aver completato l'operazione richiesta. Questa eccezione può verificarsi a causa di ritardi di rete o di altre infrastrutture. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione.<br /> Vedere [TimeoutException](#timeoutexception). | In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |L'operazione richiesta dall'utente non è consentita all'interno del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) genera questa eccezione se il messaggio è stato ricevuto in modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. | Il nuovo tentativo non sarà più utile. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) | È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. | Controllare il codice e assicurarsi che non richiami le operazioni su un oggetto eliminato. | Il nuovo tentativo non sarà più utile. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) | L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token, il token non è valido oppure il token non contiene le attestazioni necessarie per eseguire l'operazione. | Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. | In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) | Uno o più argomenti forniti al metodo non sono validi. L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contiene segmenti di percorso. Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) non è valido. Il valore della proprietà è maggiore di 32 KB. | Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. | Ripetere l'operazione non serve. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | L'entità associata all'operazione non esiste o è stata eliminata. | Assicurarsi che l'entità esista. | Ripetere l'operazione non serve. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Il client non riesce a stabilire una connessione con Hub eventi. |Assicurarsi che il nome host fornito sia corretto e l'host sia raggiungibile. | Se sono presenti problemi di connettività intermittente, può essere utile ripetere l'operazione. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | In questo momento il servizio non è in grado di elaborare la richiesta. | Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. <br /> Vedere [ServerBusyException](#serverbusyexception). | Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Eccezione di messaggistica generica che può essere generata nei casi seguenti: È stato eseguito un tentativo di creare una classe [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando un nome o un percorso appartenente a un tipo di entità diverso, ad esempio un argomento. È stato eseguito un tentativo di inviare un messaggio di dimensioni superiori a 1 MB. Si è verificato un errore nel server o nel servizio durante l'elaborazione della richiesta. Per informazioni dettagliate, vedere il messaggio di eccezione. Si tratta in genere di un'eccezione temporanea. | Controllare il codice e verificare che per il corpo del messaggio siano stati usati solo oggetti serializzabili (oppure usare un serializzatore personalizzato). Consultare la documentazione per identificare i tipi di valori delle proprietà supportati e usare solo quelli. Controllare la proprietà [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se è **true** , è possibile ripetere l'operazione. | Il comportamento di ripetizione dei tentativi non è definito e ripetere l'operazione può non essere utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | È stato eseguito un tentativo di creare un'entità con un nome già usato da un'altra entità dello stesso spazio dei nomi del servizio. | Eliminare l'entità esistente o scegliere un nome diverso per l'entità da creare. | Ripetere l'operazione non serve. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | L'entità di messaggistica ha raggiunto le dimensioni massime consentite. Questa eccezione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori, ovvero 5. | Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. <br /> Vedere [QuotaExceededException](#quotaexceededexception) | Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | È stata inoltrata una richiesta per un'operazione di runtime su un'entità disattivata. |Attivare l'entità. | Se nel frattempo l'entità è stata attivata, può essere utile ripetere l'operazione. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Un payload del messaggio supera il limite di 1 MB. Questo limite di 1 MB è per il messaggio totale, che può includere le proprietà di sistema e qualsiasi overhead .NET. | Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

Questa eccezione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori (5).

### <a name="event-hubs"></a>Hub eventi
Hub eventi ha un limite di 20 gruppi di utenti per Hub eventi. Quando si tenta di creare più gruppi, si riceve un'eccezione di tipo [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Per Hub eventi, il timeout è specificato come parte della stringa di connessione o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

Si prevede che i timeout avvengano durante o tra le operazioni di manutenzione, ad esempio gli aggiornamenti del servizio Hub eventi o gli aggiornamenti del sistema operativo sulle risorse che eseguono il servizio. Durante gli aggiornamenti del sistema operativo, le entità vengono spostate e i nodi vengono aggiornati o riavviati, operazione che può causare timeout. Per informazioni dettagliate sui contratti di servizio per il servizio Hub eventi di Azure, vedere [contratto di servizio per hub eventi](https://azure.microsoft.com/support/legal/sla/event-hubs/). 


### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: una configurazione errata o un errore temporaneo del servizio.

- **Configurazione errata** : il valore di timeout dell'operazione è troppo piccolo per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il codice contiene un valore troppo piccolo. La condizione di utilizzo della rete e della CPU può influire sul tempo necessario per il completamento di una determinata operazione, quindi il timeout dell'operazione non deve essere impostato su un valore ridotto.
- **Errore temporaneo del servizio** : a volte il servizio di Hub eventi può subire ritardi nell'elaborazione delle richieste, ad esempio durante periodi di traffico elevato. In questi casi, è possibile ritentare l'operazione dopo un ritardo fino a quando l'operazione ha esito positivo. Se la stessa operazione continua ad avere esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) o [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indicano che il server è sovraccarico. Esistono due codici di errore relativi a questa eccezione.

### <a name="error-code-50002"></a>Codice di errore 50002
Questo errore può verificarsi per uno dei due motivi:

- Il carico non viene distribuito uniformemente tra tutte le partizioni nell'hub eventi e una partizione raggiunge il limite dell'unità di velocità effettiva locale.
    
    **Soluzione** : rivedere la strategia di distribuzione della partizione o provare [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) potrebbe essere utile.

- Lo spazio dei nomi di hub eventi non dispone di unità di velocità effettiva sufficienti (è possibile controllare la schermata **metrica** nella finestra dello spazio dei nomi di hub eventi nel [portale di Azure](https://portal.azure.com) per confermare). Il portale Mostra informazioni aggregate (1 minuto), ma la velocità effettiva viene misurata in tempo reale, quindi si tratta solo di una stima.

    **Soluzione** : l'aumento delle unità di velocità effettiva nello spazio dei nomi può essere utile. 

    È possibile configurare unità di velocità effettiva nella pagina **scala** o **Panoramica** della pagina **dello spazio dei nomi di hub eventi** nel portale di Azure. In alternativa, è possibile usare la scalabilità [automatica](event-hubs-auto-inflate.md), che aumenta automaticamente le prestazioni aumentando il numero di unità di velocità effettiva per soddisfare le esigenze di utilizzo.

    Le unità elaborate (TUs) si applicano a tutti gli hub eventi in uno spazio dei nomi di hub eventi. Significa che le unità elaborate vengono acquistate a livello di spazio dei nomi e condivise con l'hub eventi nello spazio. Ogni unità elaborata conferisce allo spazio dei nomi le capacità seguenti:

    - Fino a 1 MB al secondo di eventi in ingresso (eventi inviati a un hub eventi), ma non più di 1000 eventi in ingresso, operazioni di gestione o chiamate API di controllo al secondo.
    - Fino a 2 MB al secondo di eventi in uscita (gli eventi utilizzati da un hub eventi), ma non più di 4096 eventi in uscita.
    - Fino a 84 GB di archiviazione eventi (sufficienti per il periodo di conservazione predefinito di 24 ore).
    
    Nella sezione **Mostra metriche** della pagina **Panoramica** passare alla scheda **velocità effettiva** . Selezionare il grafico per aprirlo in una finestra più grande con intervalli di 1 minuto sull'asse x. Esaminare i valori di picco e suddividerli per 60 per ottenere i byte in ingresso al secondo o i byte in uscita al secondo. Usare un approccio simile per calcolare il numero di richieste al secondo in momenti di picco nella scheda **richieste** . 

    Se i valori sono superiori al numero di limiti di TUs * (1 MB al secondo per il traffico in ingresso o 1000 richieste per il traffico in ingresso al secondo, 2 MB al secondo per l'uscita), aumentare il numero di TUs usando la pagina **scala** (nel menu a sinistra) di uno spazio dei nomi di hub eventi per scalare manualmente più a lungo o usare la funzionalità di aumento [automatico](event-hubs-auto-inflate.md) degli Si noti che il gonfiaggio automatico può aumentare fino a 20 TUS. Per elevarlo esattamente 40 TUs, inviare una [richiesta di supporto](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="error-code-50001"></a>Codice errore 50001

Questo errore si verifica raramente. Si verifica quando il contenitore che esegue il codice per lo spazio dei nomi è insufficiente per la CPU, occorrono pochi secondi prima che il bilanciamento del carico dell'Hub eventi inizi.

**Soluzione** : limitare le chiamate al Metodo GetRuntimeInformation. Hub eventi di Azure supporta fino a 50 chiamate al secondo al GetRuntimeInfo al secondo. Una volta raggiunto il limite, è possibile che venga generata un'eccezione simile alla seguente:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](./event-hubs-about.md)
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
