---
title: Bus di servizio di Azure-eccezioni di messaggistica | Microsoft Docs
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica del bus di servizio di Azure e le azioni consigliate da intraprendere quando si verifica l'eccezione.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4813ad7386af3d9dd730b74e6b815ff173cfe809
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885729"
---
# <a name="service-bus-messaging-exceptions"></a>Eccezioni di messaggistica del bus di servizio
Questo articolo elenca le eccezioni .NET generate dalle API .NET Framework. 

## <a name="exception-categories"></a>Categorie di eccezioni
Le API di messaggistica generano eccezioni che possono essere raggruppate nelle categorie seguenti e a ognuna delle quali è associata un'azione che è possibile eseguire per tentare di risolverla. Il significato e le cause di un'eccezione possono variare a seconda del tipo di entità di messaggistica:

1. Errore di codifica utente ([System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true), [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)). Azione generale: provare a correggere il codice prima di continuare.
2. Errore di installazione/configurazione ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true). Azione generale: controllare la configurazione e modificarla, se necessario.
3. Eccezioni temporanee ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Azione generale: ripetere l'operazione o inviare una notifica agli utenti. La `RetryPolicy` classe nell'SDK client può essere configurata in modo da gestire i tentativi automaticamente. Per altre informazioni, vedere [linee guida](/azure/architecture/best-practices/retry-service-specific#service-bus)per i tentativi.
4. Altre eccezioni ([System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true), [System.TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Azione generale: specifica del tipo di eccezione; vedere la tabella nella sezione seguente: 

## <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| **Tipo di eccezione** | **Descrizione/Causa/Esempi** | **Azione suggerita** | **Nota sulla ripetizione automatica/immediata** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |Il server non ha risposto all'operazione richiesta entro il tempo specificato, che è controllato da [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Il server può aver completato l'operazione richiesta. Ciò può verificarsi a causa di ritardi di rete o di altri ritardi nell'infrastruttura. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione. Vedere [Eccezioni di timeout](#timeoutexception). |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |L'operazione richiesta dall'utente non è consentita all'interno del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, [complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) genera questa eccezione se il messaggio è stato ricevuto in modalità [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. |Il tentativo non è più utile. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) |È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. |Controllare il codice e assicurarsi che non richiami le operazioni su un oggetto eliminato. |Il tentativo non è più utile. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) |L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token, il token non è valido oppure il token non contiene le attestazioni necessarie per eseguire l'operazione. |Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. |In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) |Uno o più argomenti forniti al metodo non sono validi.<br /> L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contiene segmenti di percorso.<br /> Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) non è valido. <br />Il valore della proprietà è maggiore di 32 KB. |Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. |Il tentativo non è più utile. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |L'entità associata all'operazione non esiste o è stata eliminata. |Assicurarsi che l'entità esista. |Il tentativo non è più utile. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativo di ricevere un messaggio con un particolare numero di sequenza. Questo messaggio non è stato trovato. |Verificare che il messaggio non sia già stato ricevuto. Controllare la coda dei messaggi non recapitabili per verificare che il messaggio non si trovi al suo interno. |Il tentativo non è più utile. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Il client non è in grado di stabilire una connessione al bus di servizio. |Assicurarsi che il nome host fornito sia corretto e l'host sia raggiungibile. |Se sono presenti problemi di connettività intermittente, può essere utile ripetere l'operazione. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Il servizio non è in grado di elaborare la richiesta in questo momento. |Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. |Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Eccezione di messaggistica generica che può essere generata nei casi seguenti:<p>È stato eseguito un tentativo di creare una classe [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando un nome o un percorso appartenente a un tipo di entità diverso, ad esempio un argomento.</p><p>È stato eseguito un tentativo di inviare un messaggio di dimensioni superiori a 256 kB. </p>Si è verificato un errore nel server o nel servizio durante l'elaborazione della richiesta. Per informazioni dettagliate, vedere il messaggio di eccezione. Si tratta in genere di un'eccezione temporanea.</p><p>La richiesta è stata terminata perché l'entità è stata limitata. Codice di errore: 50001, 50002, 50008. </p> | Controllare il codice e verificare che per il corpo del messaggio siano stati usati solo oggetti serializzabili (oppure usare un serializzatore personalizzato). <p>Consultare la documentazione per identificare i tipi di valori delle proprietà supportati e usare solo quelli.</p><p> Controllare la proprietà [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se è **true**, è possibile ripetere l'operazione. </p>| Se l'eccezione è dovuta alla limitazione, attendere alcuni secondi, quindi ripetere l'operazione. Il comportamento di ripetizione dei tentativi non è definito e potrebbe non essere utile in altri scenari.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |È stato eseguito un tentativo di creare un'entità con un nome già usato da un'altra entità dello stesso spazio dei nomi del servizio. |Eliminare l'entità esistente o scegliere un nome diverso per l'entità da creare. |Il tentativo non è più utile. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |L'entità di messaggistica ha raggiunto le dimensioni massime consentite oppure è stato superato il numero massimo di connessioni a uno spazio dei nomi. |Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. Vedere [QuotaExceededException](#quotaexceededexception). |Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Il bus di servizio restituisce questa eccezione se si tenta di creare un'azione di regola non valida. Il bus di servizio assegna questa eccezione a un messaggio non recapitabile se si verifica un errore durante l'elaborazione dell'azione di regola per il messaggio. |Verificare la correttezza dell'azione di regola. |Il tentativo non è più utile. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Il bus di servizio restituisce questa eccezione se si tenta di creare un filtro non valido. Il bus di servizio assegna questa eccezione a un messaggio non recapitabile se si verifica un errore durante l'elaborazione del filtro per il messaggio. |Verificare la correttezza del filtro. |Il tentativo non è più utile. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |È stato eseguito un tentativo di accettare una sessione con un ID di sessione specifico, ma la sessione è attualmente bloccata da un altro client. |Assicurarsi che la sessione venga sbloccata dagli altri client. |Se nel frattempo la sessione è stata rilasciata, può essere utile ripetere l'operazione. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Nella transazione sono presenti troppe operazioni. |Ridurre il numero di operazioni presenti nella transazione. |Il tentativo non è più utile. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |È stata inoltrata una richiesta per un'operazione di runtime su un'entità disattivata. |Attivare l'entità. |Se nel frattempo l'entità è stata attivata, può essere utile ripetere l'operazione. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Il bus di servizio restituisce questa eccezione se si invia un messaggio a un argomento in cui è abilitato il filtro preliminare e nessuno dei filtri corrisponde. |Assicurarsi che almeno un filtro corrisponda. |Il tentativo non è più utile. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Un payload del messaggio supera il limite di 256 KB. Il limite di 256 KB rappresenta la dimensione totale del messaggio, che può includere proprietà di sistema ed eventuali overhead .NET. |Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Il tentativo non è più utile. |
| [TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true) |La transazione di ambiente (*Transaction.Current*) non è valida. È possibile che nel frattempo sia stata interrotta o completata. L'eccezione interna può fornire informazioni aggiuntive. | |Il tentativo non è più utile. |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception?view=netcore-3.1&preserve-view=true) |È stata tentata un'operazione su una transazione in dubbio oppure è stato tentato di eseguire il commit della transazione e la transazione è diventata in dubbio. |L'applicazione deve gestire questa eccezione (come caso speciale), poiché è possibile che sia già stato eseguito il commit della transazione. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

### <a name="queues-and-topics"></a>Code e argomenti
Per le code e gli argomenti, spesso si tratta della dimensione della coda. La proprietà del messaggio di errore contiene altri dettagli, come nell'esempio seguente:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Il messaggio indica che l'argomento ha superato il limite di dimensioni, in questo caso 1 GB (limite di dimensioni predefinito). 

### <a name="namespaces"></a>Spazi dei nomi

Per gli spazi dei nomi, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) può indicare che un'applicazione ha superato il numero massimo di connessioni a uno spazio dei nomi. Ad esempio:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: la coda dei messaggi non recapitabili e l'interruzione da parte dei destinatari dei messaggi.

1. Coda dei messaggi non **[recapitabili](service-bus-dead-letter-queues.md)** Un lettore non riesce a completare i messaggi e i messaggi vengono restituiti alla coda o all'argomento alla scadenza del blocco. Può verificarsi se il lettore rileva un'eccezione che impedisce la chiamata a [BrokeredMessage. complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Dopo che un messaggio è stato letto 10 volte, si sposta alla coda dei messaggi non recapitabili per impostazione predefinita. Questo comportamento è controllato dalla proprietà [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e ha un valore predefinito di 10. Man mano che i messaggi si accumulano nella coda dei messaggi non recapitabili, occupano spazio.
   
    Per risolvere il problema, leggere e completare i messaggi nella coda dei messaggi non recapitabili, come si farebbe per qualsiasi altra coda. È possibile usare il metodo [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) per la formattazione del percorso della coda dei messaggi non recapitabili.
2. **Interruzione da parte del destinatario**. Un destinatario ha interrotto la ricezione di messaggi da una coda o da una sottoscrizione. Per identificare questo problema, è necessario osservare la proprietà [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , che mostra la suddivisione completa dei messaggi. Se la proprietà [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) è alta o in crescita, i messaggi non vengono letti con la stessa velocità con cui vengono scritti.

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Controllare il valore della proprietà [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=netcore-3.1&preserve-view=true), in quanto il raggiungimento di questo limite può anche causare un evento di [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true).

Si prevede che i timeout avvengano durante o tra le operazioni di manutenzione, ad esempio gli aggiornamenti del servizio del bus di servizio o gli aggiornamenti del sistema operativo sulle risorse che eseguono il servizio. Durante gli aggiornamenti del sistema operativo, le entità vengono spostate e i nodi vengono aggiornati o riavviati, operazione che può causare timeout. Per informazioni dettagliate sui contratti di servizio per il servizio bus di servizio di Azure, vedere [contratto di servizio per il bus di servizio](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Code e argomenti
Per le code e gli argomenti, il timeout è specificato nella proprietà [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings), come parte della stringa di connessione, o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Causa

**MessageLockLostException** viene generata quando un messaggio viene ricevuto tramite la modalità di ricezione [PeekLock](message-transfers-locks-settlement.md#peeklock) e il blocco mantenuto dal client scade sul lato del servizio.

Il blocco di un messaggio può scadere a causa di vari motivi: 

  * Il timer di blocco è scaduto prima che sia stato rinnovato dall'applicazione client.
  * L'applicazione client ha acquisito il blocco, salvato in un archivio permanente e quindi riavviato. Una volta riavviato, l'applicazione client ha esaminato i messaggi in fase di esecuzione e ha tentato di completarli.

### <a name="resolution"></a>Soluzione

Nel caso di un **MessageLockLostException**, l'applicazione client non è più in grado di elaborare il messaggio. L'applicazione client può facoltativamente prendere in considerazione la registrazione dell'eccezione per l'analisi, ma il client *deve* eliminare il messaggio.

Poiché il blocco sul messaggio è scaduto, torna alla coda (o alla sottoscrizione) e può essere elaborato dall'applicazione client successiva che chiama Receive.

Se il **MaxDeliveryCount** ha superato il limite, il messaggio può essere spostato in **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Causa

**SessionLockLostException** viene generata quando una sessione viene accettata e il blocco utilizzato dal client scade sul lato del servizio.

Il blocco di una sessione può scadere per vari motivi: 

  * Il timer di blocco è scaduto prima che sia stato rinnovato dall'applicazione client.
  * L'applicazione client ha acquisito il blocco, salvato in un archivio permanente e quindi riavviato. Una volta riavviato, l'applicazione client esamina le sessioni in corso e tenta di elaborare i messaggi in tali sessioni.

### <a name="resolution"></a>Soluzione

In caso di **SessionLockLostException**, l'applicazione client non è più in grado di elaborare i messaggi della sessione. L'applicazione client può prendere in considerazione la registrazione dell'eccezione per l'analisi, ma il client *deve* eliminare il messaggio.

Poiché il blocco sulla sessione è scaduto, torna alla coda (o alla sottoscrizione) e può essere bloccato dalla successiva applicazione client che accetta la sessione. Poiché il blocco della sessione è gestito da una singola applicazione client in un determinato momento, l'elaborazione in ordine è garantita.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Causa

Viene generata un'eccezione **SocketException** nei casi seguenti:
   * Quando un tentativo di connessione non riesce perché l'host non risponde correttamente dopo un'ora specificata (codice di errore TCP 10060).
   * Una connessione stabilita non è riuscita perché l'host connesso non è stato in grado di rispondere.
   * Si è verificato un errore durante l'elaborazione del messaggio oppure il timeout è stato superato dall'host remoto.
   * Problema di risorse di rete sottostante.

### <a name="resolution"></a>Soluzione

Gli errori di **SocketException** indicano che la macchina virtuale che ospita le applicazioni non è in grado di convertire il nome nell' `<mynamespace>.servicebus.windows.net` indirizzo IP corrispondente. 

Controllare se il comando seguente ha esito positivo nel mapping a un indirizzo IP.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

che deve fornire un output come riportato di seguito

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Se il nome precedente **non viene risolto** in un indirizzo IP e nell'alias dello spazio dei nomi, controllare l'amministratore di rete per eseguire ulteriori indagini. La risoluzione dei nomi viene eseguita tramite un server DNS in genere una risorsa nella rete del cliente. Se la risoluzione DNS viene eseguita da DNS di Azure, contattare il supporto tecnico di Azure.

Se la risoluzione dei nomi **funziona come previsto**, verificare se le connessioni al bus di servizio di Azure sono consentite [qui](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>Causa

**MessagingException** è un'eccezione generica che può essere generata per vari motivi. Di seguito sono riportati alcuni dei motivi.

   * Viene effettuato un tentativo di creare un **QueueClient** per un **argomento** o una **sottoscrizione**.
   * Le dimensioni del messaggio inviato sono maggiori del limite per il livello specificato. Scopri di più sulle [quote e sui limiti](service-bus-quotas.md)del bus di servizio.
   * La richiesta del piano dati specifico (invio, ricezione, completamento, abbandono) è stata terminata a causa della limitazione delle richieste.
   * Problemi temporanei causati da aggiornamenti e riavvii dei servizi.

> [!NOTE]
> L'elenco di eccezioni precedente non è esaustivo.

### <a name="resolution"></a>Soluzione

La procedura di risoluzione dipende da ciò che ha causato la generazione della **messaggisticaexception** .

   * Per i **problemi temporanei** (in cui è impostato su ***true***) o per i **problemi di limitazione**, è possibile che l'operazione venga risolta con un nuovo tentativo. ***isTransient*** Per questo è possibile sfruttare i criteri di ripetizione dei tentativi predefiniti nell'SDK.
   * Per altri problemi, i dettagli nell'eccezione indicano che è possibile dedurre i passaggi del problema e della risoluzione dallo stesso.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni complete sull'API .NET del bus di servizio, vedere [Azure .NET API reference](/dotnet/api/overview/azure/service-bus) (Informazioni di riferimento sulle API di Azure per .NET).
Per suggerimenti sulla risoluzione dei problemi, vedere la [Guida alla risoluzione dei problemi](service-bus-troubleshooting-guide.md)