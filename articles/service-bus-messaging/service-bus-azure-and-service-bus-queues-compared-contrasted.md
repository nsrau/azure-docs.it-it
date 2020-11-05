---
title: Confronto tra code di Archiviazione di Azure e code di Bus di servizio
description: Analizza i punti in comune e le differenze tra i due tipi di code offerti da Azure.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 5c65cf5ef2d572417ea70d0e0259cf2c03ab590e
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379571"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Analogie e differenze tra le code di archiviazione e le code del bus di servizio
Questo articolo analizza le differenze e le analogie tra i due tipi di code offerte da Microsoft Azure: code di archiviazione e code del bus di servizio. Utilizzando queste informazioni, è possibile prendere una decisione più consapevole sulla soluzione più adatta alle proprie esigenze.

## <a name="introduction"></a>Introduzione
Azure supporta due tipi di meccanismi di code: **code di archiviazione** e **code del bus di servizio**.

Le **code di archiviazione** fanno parte dell'infrastruttura di archiviazione di [Azure](https://azure.microsoft.com/services/storage/) . Consentono di archiviare un numero elevato di messaggi. È possibile accedere ai messaggi ovunque ci si trovi con chiamate autenticate tramite HTTP o HTTPS. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono. Per altre informazioni, vedere [che cosa sono le code di archiviazione di Azure](../storage/queues/storage-queues-introduction.md).

Le **code del bus di servizio** fanno parte di un'infrastruttura di messaggistica di [Azure](https://azure.microsoft.com/services/service-bus/) più ampia che supporta l'accodamento, la pubblicazione/sottoscrizione e modelli di integrazione più avanzati. Sono progettati per integrare applicazioni o componenti dell'applicazione che possono estendersi su più protocolli di comunicazione, contratti dati, domini di trust o ambienti di rete. Per altre informazioni su code, argomenti e sottoscrizioni del bus di servizio, vedere [code, argomenti e](service-bus-queues-topics-subscriptions.md)sottoscrizioni del bus di servizio.


## <a name="technology-selection-considerations"></a>Considerazioni sulla selezione della tecnologia
Le code di archiviazione e le code del bus di servizio hanno un set di funzionalità leggermente diverso. È possibile scegliere uno o entrambi, a seconda delle esigenze della soluzione specifica.

Quando si determina quale tecnologia di Accodamento si riferisce allo scopo di una determinata soluzione, architetti e sviluppatori di soluzioni devono prendere in considerazione questi consigli. 

### <a name="consider-using-storage-queues"></a>Provare a usare le code di archiviazione
Gli architetti e gli sviluppatori di soluzioni **dovrebbero considerare l'uso delle code di Azure** quando:

* L'applicazione deve archiviare oltre 80 gigabyte di messaggi in una coda.
* L'applicazione desidera tenere traccia dello stato di avanzamento dell'elaborazione di un messaggio nella coda. È utile se il thread di lavoro che elabora un messaggio si arresta in modo anomalo. Un altro ruolo di lavoro può quindi utilizzare tali informazioni per continuare dal punto in cui il lavoro precedente è stato interrotto.
* Sono necessari i log sul lato server di tutte le transazioni eseguite nelle code.

### <a name="consider-using-service-bus-queues"></a>Provare a usare le code del bus di servizio
Gli architetti e gli sviluppatori di soluzioni **dovrebbero considerare l'uso delle code del bus di servizio** quando:

* La soluzione deve ricevere messaggi senza dover eseguire il polling della coda. Con il bus di servizio, è possibile ottenerlo usando un'operazione di ricezione con polling prolungato usando i protocolli basati su TCP supportati dal bus di servizio.
* La soluzione deve garantire il recapito ordinato FIFO (First In First Out) da parte della coda.
* La soluzione deve supportare il rilevamento automatico dei duplicati.
* L'applicazione deve elaborare i messaggi come flussi paralleli a esecuzione prolungata (i messaggi sono associati a un flusso tramite la proprietà [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) del messaggio). In questo modello ogni nodo dell'applicazione che utilizza il servizio entra in competizione con gli altri nodi per l'acquisizione dei flussi anziché dei messaggi. Quando un flusso viene assegnato a un nodo basato sul servizio, tale nodo può esaminare lo stato del flusso dell'applicazione mediante transazioni.
* Per la soluzione sono necessari atomicità e comportamento transazionale in caso di invio o ricezione di più messaggi da una coda.
* L'applicazione gestisce messaggi che possono superare 64 KB ma che probabilmente non si avvicineranno al limite di 256 KB.
* È necessario fornire un modello di accesso basato sui ruoli alle code e autorizzazioni/diritti diversi per mittenti e destinatari. Per altre informazioni, vedere gli articoli seguenti:
    - [Eseguire l'autenticazione con le identità gestite](service-bus-managed-service-identity.md)
    - [Eseguire l'autenticazione da un'applicazione](authenticate-application.md)
* Le dimensioni della coda non crescono più di 80 GB.
* Si vuole usare il protocollo di messaggistica basato sugli standard AMQP 1.0. Per altre informazioni su AMQP, vedere [Panoramica di AMQP per il bus di servizio](service-bus-amqp-overview.md).
* È possibile prevedere un'eventuale migrazione dalla comunicazione punto a punto basata sulla coda a un modello di messaggistica di pubblicazione-sottoscrizione. Questo modello consente l'integrazione di destinatari aggiuntivi (sottoscrittori). Ogni ricevitore riceve copie indipendenti di alcuni o tutti i messaggi inviati alla coda. 
* La soluzione di messaggistica deve supportare la garanzia di recapito "at-most-once" senza che sia necessario compilare i componenti aggiuntivi dell'infrastruttura.
* La soluzione deve pubblicare e utilizzare batch di messaggi.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Confrontare le code di archiviazione e le code del bus di servizio
Le tabelle nelle sezioni seguenti forniscono un raggruppamento logico delle funzionalità di Accodamento. Consentono di confrontare a colpo d'occhio le funzionalità disponibili sia nelle code di archiviazione di Azure che nelle code del bus di servizio.

## <a name="foundational-capabilities"></a>Funzionalità fondamentali
Questa sezione confronta alcune delle funzionalità di accodamento fondamentali fornite dalle code di archiviazione e dalle code del bus di servizio.

| Criteri di confronto | Code di archiviazione | Code del bus di servizio |
| --- | --- | --- |
| Garanzia di ordinamento |**No** <br/><br>Per ulteriori informazioni, vedere la prima nota nella sezione [informazioni aggiuntive](#additional-information) .</br> | **Sì - First-In-First-Out (FIFO)**<br/><br>(tramite l'utilizzo di [sessioni di messaggi](message-sessions.md)) |
| Garanzia di recapito |**At-Least-Once** |**At-least-once** (usando la modalità di ricezione PeekLock. È il valore predefinito) <br/><br/>**At-most-once** (usando la modalità di ricezione ReceiveAndDelete) <br/> <br/> Altre informazioni sulle diverse [modalità di ricezione](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Supporto per l'operazione atomica |**No** |**Sì**<br/><br/> |
| Comportamento di ricezione |**Non bloccante**<br/><br/>(viene completata immediatamente se non vengono trovati altri messaggi) |**Blocco con o senza timeout**<br/><br/>(offre disponibilità di polling prolungato o la ["Tecnica Comet"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Non bloccante**<br/><br/>(solo tramite l'uso di interfaccia API gestita di .NET) |
| API di tipo push |**No** |**Sì**<br/><br/>API .NET per sessioni [QueueClient. OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) e [MessageSessionHandler. OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) . |
| Modalità di ricezione |**Visualizzazione e lease** |**Visualizzazione e blocco**<br/><br/>**Ricezione ed eliminazione** |
| Modalità di accesso esclusivo |**Basato sul lease** |**Basato sul blocco** |
| Durata lease/blocco |**30 secondi (impostazione predefinita)**<br/><br/>**7 giorni (durata massima)** (È possibile rinnovare o rilasciare un lease di messaggio tramite l'interfaccia API di [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage)). |**60 secondi (impostazione predefinita)**<br/><br/>È possibile rinnovare un blocco di messaggio tramite l'interfaccia API di [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock). |
| Precisione lease/blocco |**Livello di messaggio**<br/><br/>Ogni messaggio può avere un valore di timeout diverso, che è quindi possibile aggiornare in base alle esigenze durante l'elaborazione del messaggio, usando l'API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) . |**Livello di coda**<br/><br/>(ogni coda ha una precisione di blocco applicata a tutti i relativi messaggi, tuttavia è possibile rinnovare il blocco tramite l'interfaccia API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)). |
| Ricezione in batch |**Sì**<br/><br/>(specifica esplicitamente il numero di messaggi durante il recupero dei messaggi, fino a un massimo di 32 messaggi). |**Sì**<br/><br/>(abilita implicitamente una proprietà di prelettura o esplicitamente tramite l'uso di transazioni). |
| Invio in batch |**No** |**Sì**<br/><br/>(tramite l'uso di transazioni o invio in batch sul lato client). |

### <a name="additional-information"></a>Informazioni aggiuntive
* I messaggi nelle code di archiviazione sono in genere First-in-First-out, ma a volte possono non essere ordinati. Ad esempio, quando la durata del timeout di visibilità di un messaggio scade a causa di un arresto anomalo di un'applicazione client durante l'elaborazione di un messaggio. Quando il timeout di visibilità scade, il messaggio risulta nuovamente visibile nella coda in modo che un altro utente possa rimuoverlo. A questo punto, il nuovo messaggio visibile potrebbe essere inserito nella coda per essere rimosso nuovamente dalla coda.
* Il modello FIFO garantito nelle code del bus di servizio richiede l'uso di sessioni di messaggistica. Se si verifica un arresto anomalo dell'applicazione durante l'elaborazione di un messaggio ricevuto nella modalità **visualizza & blocco** , alla successiva accettazione di una sessione di messaggistica da parte di un ricevitore della coda, verrà avviato con il messaggio non superato dopo la scadenza del periodo di durata (TTL) del messaggio.
* Le code di archiviazione sono progettate per supportare scenari di Accodamento standard, ad esempio quelli riportati di seguito:
    - Separazione dei componenti dell'applicazione per aumentare la scalabilità e la tolleranza degli errori
    - Livellamento del carico
    - Compilazione dei flussi di lavoro dei processi.
* Le incoerenze relative alla gestione dei messaggi nel contesto delle sessioni del bus di servizio possono essere evitate utilizzando lo stato della sessione per archiviare lo stato dell'applicazione in relazione allo stato di avanzamento della gestione della sequenza di messaggi della sessione e utilizzando transazioni per la risoluzione dei messaggi ricevuti e l'aggiornamento dello stato della sessione. Questo tipo di funzionalità di coerenza viene talvolta etichettato *esattamente una volta nell'elaborazione* dei prodotti di altri fornitori. Eventuali errori di transazione provocheranno ovviamente il recapito dei messaggi e questo è il motivo per cui il termine non è esattamente appropriato.
* Le code di archiviazione offrono un modello di programmazione uniforme e coerente tra code, tabelle e BLOB, sia per i team di sviluppo che per i team operativi.
* Le code del bus di servizio offrono supporto per le transazioni locali nel contesto di una singola coda.
* La modalità **Ricezione ed eliminazione** supportata dal bus di servizio offre la possibilità di ridurre il numero di operazioni di messaggistica (e relativo costo) in cambio di una garanzia di recapito più bassa.
* Le code di archiviazione offrono lease con possibilità di estensione per i messaggi. Questa funzionalità consente ai processi di lavoro di mantenere brevi lease sui messaggi. Quindi, se un thread di lavoro si arresta in modo anomalo, il messaggio può essere elaborato nuovamente da un altro thread di lavoro. Un thread di lavoro può inoltre estendere il lease in un messaggio se è necessario elaborarlo più a lungo rispetto al tempo di lease corrente.
* Le code di archiviazione offrono un timeout di visibilità che può essere impostato al momento dell'inserimento o della rimozione dalla coda di un messaggio. Inoltre, è possibile aggiornare un messaggio con valori di lease diversi in fase di esecuzione e aggiornare valori diversi tra i messaggi nella stessa coda. I timeout di blocco del bus di servizio sono definiti nei metadati della coda. È tuttavia possibile rinnovare il blocco chiamando il metodo [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* Il timeout massimo per un'operazione di ricezione del blocco nelle code del bus di servizio è di 24 giorni. I timeout basati su REST, tuttavia, hanno un valore massimo di 55 secondi.
* Il batch sul lato client fornito dal bus di servizio consente a un client di coda di inviare in batch più messaggi in una singola operazione di invio. L'invio in batch è disponibile solo per le operazioni di invio asincrone.
* Funzionalità come il limite di 200 TB di code di archiviazione (più quando si virtualizzano gli account) e le code illimitate lo rendono una piattaforma ideale per i provider SaaS.
* Le code di archiviazione forniscono un meccanismo di controllo di accesso delegato flessibile ed efficiente.

## <a name="advanced-capabilities"></a>Funzionalità avanzate
Questa sezione confronta le funzionalità avanzate fornite dalle code di archiviazione e dalle code del bus di servizio.

| Criteri di confronto | Code di archiviazione | Code del bus di servizio |
| --- | --- | --- |
| Recapito pianificato |**Sì** |**Sì** |
| Mancato recapito automatico dei messaggi |**No** |**Sì** |
| Valore TTL di accodamento aumentato |**Sì**<br/><br/>(tramite aggiornamento sul posto del timeout di visibilità) |**Sì**<br/><br/>(specificato tramite una funzione API dedicata) |
| Supporto messaggi non elaborabili |**Sì** |**Sì** |
| Aggiornamento sul posto |**Sì** |**Sì** |
| Log delle transazioni sul lato server |**Sì** |**No** |
| Metriche di archiviazione |**Sì**<br/><br/>**Metrica al minuto** fornisce metriche in tempo reale per disponibilità, TPS, conteggi delle chiamate API, conteggi degli errori e altro ancora. Sono tutte in tempo reale, aggregate al minuto e segnalate entro pochi minuti da ciò che si è appena verificato in produzione. Per altre informazioni, vedere [About Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics) (Informazioni sulle metriche di analisi dell'archiviazione). |**Sì**<br/><br/>(query in blocco tramite chiamate a [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Gestione dello stato |**No** |**Sì**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Autoinoltring messaggi |**No** |**Sì** |
| Funzione di eliminazione della coda |**Sì** |**No** |
| Gruppi di messaggi |**No** |**Sì**<br/><br/>(tramite l'uso di sessioni di messaggistica) |
| Stato dell'applicazione per gruppo di messaggi |**No** |**Sì** |
| Rilevamento duplicati |**No** |**Sì**<br/><br/>(configurabile sul lato del mittente) |
| Esplorazione di gruppi di messaggi |**No** |**Sì** |
| Recupero delle sessioni di messaggistica per ID |**No** |**Sì** |

### <a name="additional-information"></a>Informazioni aggiuntive
* Entrambe le tecnologie di accodamento consentono di pianificare il recapito di un messaggio in un momento successivo.
* L'invio in coda consente a migliaia di code di eseguire l'autoinoltring dei messaggi a una singola coda, da cui l'applicazione ricevente usa il messaggio. Questo meccanismo consente di ottenere un valore elevato di sicurezza, di controllare il flusso e di isolare le aree di archiviazione tra i server di pubblicazione dei messaggi.
* Le code di archiviazione offrono supporto per l'aggiornamento del contenuto del messaggio. Questa funzionalità consente di rendere permanenti le informazioni sullo stato e gli aggiornamenti sull'avanzamento incrementale all'interno del messaggio, in modo che sia possibile elaborare quest'ultimo dall'ultimo checkpoint noto anziché dall'inizio. Con le code del bus di servizio è possibile abilitare lo stesso scenario mediante sessioni di messaggistica Le sessioni consentono di salvare e recuperare lo stato di elaborazione dell'applicazione tramite [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState).
* Le code del bus di servizio supportano il [messaggio non recapitabile](service-bus-dead-letter-queues.md). Può essere utile per isolare i messaggi che soddisfano i criteri seguenti:
    - I messaggi non possono essere elaborati correttamente dall'applicazione ricevente 
    - I messaggi non possono raggiungere la destinazione a causa di una proprietà time-to-Live (TTL) scaduta. Tramite il valore TTL viene specificata la durata del messaggio nella coda. Con il bus di servizio, il messaggio verrà spostato in una coda speciale denominata $DeadLetterQueue allo scadere del periodo TTL.
* Per trovare messaggi "non elaborabili" nelle code di archiviazione, durante la rimozione di un messaggio dalla coda l'applicazione esamina la proprietà [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) del messaggio. Se il valore della proprietà **DequeueCount** supera la soglia specificata, l'applicazione sposta il messaggio in una coda di "messaggio non recapitabile" definita dall'applicazione.
* Le code di archiviazione consentono di ottenere un log dettagliato di tutte le transazioni eseguite nella coda e le metriche aggregate. Queste opzioni sono entrambe utili per il debug e per comprendere in che modo l'applicazione usa le code di archiviazione. Sono inoltre utili per ottimizzare le prestazioni dell'applicazione e ridurre i costi di utilizzo delle code.
* Le sessioni di messaggi supportate dal bus di servizio consentono di associare a un destinatario i messaggi appartenenti a un gruppo logico. Viene creata un'affinità di tipo sessione tra i messaggi e i rispettivi ricevitori. È possibile abilitare questa funzionalità avanzata nel bus di servizio impostando la proprietà [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) in un messaggio. I destinatari possono quindi restare in ascolto su un ID di sessione specifico e ricevere messaggi in cui viene condiviso l'identificatore di sessione specificato.
* La funzionalità di rilevamento della duplicazione delle code del bus di servizio rimuove automaticamente i messaggi duplicati inviati a una coda o a un argomento, in base al valore della proprietà [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Capacità e quote
Questa sezione confronta le code di Azure e le code del bus di servizio in termini di [capacità e quote](service-bus-quotas.md).

| Criteri di confronto | Code di archiviazione | Code del bus di servizio |
| --- | --- | --- |
| Dimensioni massime della coda |**500 TB**<br/><br/>(limitate alla capacità di un [singolo account di archiviazione](../storage/common/storage-introduction.md#queue-storage)) |**Da 1 GB a 80 GB**<br/><br/>(valori definiti al momento della creazione della coda e dell'[abilitazione del partizionamento](service-bus-partitioning.md). Vedere la sezione "Informazioni aggiuntive"). |
| Dimensioni massime del messaggio |**64 KB**<br/><br/>(48 KB quando si usa una codifica **Base64** )<br/><br/>Azure supporta messaggi di grandi dimensioni combinando code e BLOB. È quindi possibile accodare fino a 200 GB per un unico elemento. |**256 KB** o **1 MB**<br/><br/>(inclusi l'intestazione e il corpo, dimensioni massime dell'intestazione: 64 KB).<br/><br/>Dipende dal [livello di servizio](service-bus-premium-messaging.md). |
| Durata TTL massima del messaggio |**Infinito** (API-versione 2017-07-27 o successiva) |**TimeSpan.Max** |
| Numero massimo di code |**Illimitato** |**10,000**<br/><br/>(per spazio dei nomi del servizio) |
| Numero massimo di client concorrenti |**Illimitato** |**Illimitato**<br/><br/>(limite di 100 connessioni simultanee applicato solo alla comunicazione basata su protocollo TCP) |

### <a name="additional-information"></a>Informazioni aggiuntive
* Bus di servizio impone l'applicazione dei limiti di dimensione della coda. Quando si crea una coda, viene specificata la dimensione massima della coda. Può avere una lunghezza compresa tra 1 GB e 80 GB. Se le dimensioni della coda raggiungono questo limite, i messaggi in arrivo aggiuntivi verranno rifiutati e il chiamante riceverà un'eccezione. Per altre informazioni sulle quote nel bus di servizio, vedere [Quote del bus di servizio](service-bus-quotas.md).
* Il partizionamento non è supportato nel [livello Premium](service-bus-premium-messaging.md). Nel livello di messaggistica standard è possibile creare code e argomenti del bus di servizio in dimensioni di 1 (impostazione predefinita), 2, 3, 4 o 5 GB. Con il partizionamento abilitato, il bus di servizio crea 16 copie (16 partizioni) dell'entità, ognuna delle stesse dimensioni specificate. Di conseguenza, se si crea una coda di 5 GB, con 16 partizioni le dimensioni massime della coda diventano (5 * 16) = 80 GB.  È possibile visualizzare le dimensioni massime della coda o dell'argomento partizionato nell' [portale di Azure][Azure portal].
* Con le code di archiviazione, se il contenuto del messaggio non è protetto da XML, deve essere codificato in base **64** . Se per il messaggio non è stata usata la codifica **Base64** , il payload dell'utente può essere fino a 48 KB, anziché 64.
* Con le code del bus di servizio, ogni messaggio archiviato in una coda è costituito da due parti: un'intestazione e un corpo. La dimensione totale del messaggio non può superare la dimensione massima del messaggio supportata dal livello di servizio.
* Se le comunicazioni tra client e code del bus di servizio vengono stabilite tramite il protocollo TCP, il numero massimo di connessioni simultanee a una singola coda del bus di servizio è limitato a 100. Questo numero è condiviso tra mittenti e destinatari. Se viene raggiunta questa quota, le richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Questo limite non viene imposto ai client che si connettono alle code usando l'API basata su REST.
* Se si richiedono più di 10.000 code in un unico spazio dei nomi del bus di servizio, è possibile contattare il team di supporto tecnico di Azure e richiedere un aumento. Per ridimensionare più di 10.000 code con il bus di servizio, è anche possibile creare spazi dei nomi aggiuntivi tramite il [portale di Azure][Azure portal].

## <a name="management-and-operations"></a>Gestione e operazioni
Questa sezione confronta le funzionalità di gestione fornite dalle code di archiviazione e dalle code del bus di servizio.

| Criteri di confronto | Code di archiviazione | Code del bus di servizio |
| --- | --- | --- |
| Protocollo di gestione |**REST su HTTP/HTTPS** |**REST su HTTPS** |
| Protocollo runtime |**REST su HTTP/HTTPS** |**REST su HTTPS**<br/><br/>**Standard AMQP 1.0 (TCP con TLS)** |
| API .NET |**Sì**<br/><br/>(API client di archiviazione .NET) |**Sì**<br/><br/>(API del bus di servizio .NET) |
| C++ nativo |**Sì** |**Sì** |
| API Java |**Sì** |**Sì** |
| API PHP |**Sì** |**Sì** |
| API Node.js |**Sì** |**Sì** |
| Supporto metadati arbitrario |**Sì** |**No** |
| Regole di denominazione delle code |**Può contenere fino a 63 caratteri**<br/><br/>(le lettere del nome di una coda devono essere minuscole). |**Può contenere fino a 260**<br/><br/>(i percorsi e i nomi di code non fanno distinzione tra maiuscole e minuscole). |
| Funzione di recupero della lunghezza della coda |**Sì**<br/><br/>(valore indicativo se i messaggi scadono dopo il TTL senza essere eliminati). |**Sì**<br/><br/>(Valore esatto e temporizzato). |
| Funzione di visualizzazione |**Sì** |**Sì** |

### <a name="additional-information"></a>Informazioni aggiuntive
* Le code di archiviazione forniscono supporto per gli attributi arbitrari applicabili alla descrizione della coda, sotto forma di coppie nome-valore.
* Entrambe le tecnologie di coda consentono di visualizzare un messaggio senza doverlo bloccare. Questa funzionalità può essere utile quando si implementa uno strumento di esplorazione delle code.
* Le API di messaggistica negoziata .NET del bus di servizio usano le connessioni TCP full-duplex per ottenere prestazioni migliori rispetto a REST su HTTP e supportano il protocollo standard AMQP 1,0.
* I nomi delle code di archiviazione possono avere una lunghezza compresa tra 3 e 63 caratteri e contenere lettere minuscole, numeri e trattini. Per altre informazioni, vedere [Naming Queues and Metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata) (Denominazione di code e metadati).
* I nomi delle code del bus di servizio possono avere una lunghezza massima di 260 caratteri e presentano regole di denominazione meno restrittive. I nomi delle code del bus di servizio possono contenere lettere, numeri, punti, trattini e caratteri di sottolineatura.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione
Questa sezione illustra le funzionalità di autenticazione e autorizzazione supportate dalle code di archiviazione e dalle code del bus di servizio.

| Criteri di confronto | Code di archiviazione | Code del bus di servizio |
| --- | --- | --- |
| Authentication |**Chiave simmetrica** |**Chiave simmetrica** |
| Modello di protezione |Accesso delegato tramite token di firma di accesso condiviso. |SAS |
| Federazione del provider di identità |**No** |**Sì** |

### <a name="additional-information"></a>Informazioni aggiuntive
* Ogni richiesta a entrambe le tecnologie di accodamento deve essere autenticata. Le code pubbliche con accesso anonimo non sono supportate. L'uso di [SAS](service-bus-sas.md) consente di ovviare a questo inconveniente tramite la pubblicazione di una firma SAS di sola scrittura, di sola lettura o anche di accesso completo.
* Lo schema di autenticazione fornito dalle code di archiviazione prevede l'utilizzo di una chiave simmetrica. Questa chiave è un Message Authentication Code basato su hash (HMAC), calcolata con l'algoritmo SHA-256 e codificata come stringa **base64** . Per altre informazioni sul relativo protocollo, vedere [Authentication for the Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services) (Autenticazione per i servizi di archiviazione di Azure). Le code del bus di servizio supportano un modello simile mediante l'uso di chiavi simmetriche. Per altre informazioni, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](service-bus-sas.md).

## <a name="conclusion"></a>Conclusione
Ottenendo una conoscenza più approfondita delle due tecnologie, è possibile prendere una decisione più consapevole sulla tecnologia di Accodamento da usare e quando. La scelta di usare le code di archiviazione o le code del bus di servizio si basa chiaramente su diversi fattori, che possono dipendere soprattutto dalle singole esigenze dell'applicazione in uso e dalla relativa architettura. 

Potrebbe essere preferibile scegliere le code di archiviazione per i motivi seguenti:

- Se l'applicazione usa già le funzionalità principali di Microsoft Azure 
- Se sono necessarie comunicazioni e messaggistica di base tra i servizi 
- Sono necessarie le code che possono avere dimensioni maggiori di 80 GB

Le code del bus di servizio forniscono una serie di funzionalità avanzate, ad esempio le seguenti. Quindi, possono essere la scelta migliore se si sta compilando un'applicazione ibrida o se l'applicazione in uso richiede queste funzionalità.

- [Sessioni](message-sessions.md)
- [Transazioni](service-bus-transactions.md)
- [Rilevamento duplicati](duplicate-detection.md)
- [Messaggi non recapitabili automatici](service-bus-dead-letter-queues.md)
- [Funzionalità di pubblicazione e sottoscrizione durevoli](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti offrono ulteriori indicazioni e informazioni sull'uso delle code di archiviazione e delle code del bus di servizio.

* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare il servizio di archiviazione code](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio](service-bus-performance-improvements.md)
* [Introduzione alle code e agli argomenti del bus di servizio di Azure (post di blog)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Guida per gli sviluppatori del bus di servizio](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Uso del servizio di accodamento in Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

