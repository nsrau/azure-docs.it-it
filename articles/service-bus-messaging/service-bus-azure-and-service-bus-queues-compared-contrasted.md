---
title: Code di Azure e del bus di servizio - Analogie e differenze | Microsoft Docs
description: Analizza i punti in comune e le differenze tra i due tipi di code offerti da Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: tysonn
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/23/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0740427b1cd990fb94e82f1f045cc9e7f11468cd


---
# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Analogie e differenze tra le code di Azure e le code del bus di servizio
Questo articolo analizza le differenze e le analogie presenti tra i due tipi di code offerte attualmente da Microsoft Azure: code di Azure e code del bus di servizio. Grazie a queste informazioni, è possibile confrontare e contrapporre le rispettive tecnologie ed essere quindi in grado di fare una scelta più oculata riguardo alla soluzione che soddisfa meglio le proprie esigenze.

## <a name="introduction"></a>Introduzione
Microsoft Azure supporta due tipi di meccanismi di code: **code di Azure** e **code del bus di servizio**.

Le **code di Azure**, che fanno parte dell'infrastruttura di [archiviazione di Azure](https://azure.microsoft.com/services/storage/), offrono un'interfaccia per operazioni di ricezione/inserimento/visualizzazione basata su REST, che determina una messaggistica affidabile e persistente all'interno di un servizio e tra i servizi.

Le **code del bus di servizio** fanno parte di un'infrastruttura di [messaggistica di Azure](https://azure.microsoft.com/services/service-bus/) più ampia che supporta l'accodamento, oltre a modelli di pubblicazione, sottoscrizione, comunicazione remota del servizio Web e integrazione. Per altre informazioni su code, argomenti, sottoscrizioni e inoltri del bus di servizio, vedere [Panoramica dei modelli di messaggistica del bus di servizio](service-bus-messaging-overview.md).

Anche se entrambe le tecnologie di accodamento sono disponibili contemporaneamente, le code di Azure sono state introdotte prima, come meccanismo dedicato di archiviazione delle code creato nei servizi di archiviazione di Azure. Le code del bus di servizio vengono create nell'infrastruttura di "messaggistica negoziata" più ampia progettata per integrare applicazioni e componenti delle applicazioni in grado di estendere più protocolli di comunicazione, contratti dati, domini trusted e/o ambienti di rete.

Questo articolo confronta le due tecnologie di accodamento offerte da Azure, illustrando le differenze di comportamento e implementazione delle funzionalità fornite da ognuna. L'articolo offre anche informazioni aggiuntive per la scelta delle funzionalità che possono soddisfare meglio le proprie esigenze di sviluppo di applicazioni.

## <a name="technology-selection-considerations"></a>Considerazioni sulla selezione della tecnologia
Sia le code di Azure sia quelle del bus di servizio sono implementazioni del servizio di accodamento messaggi attualmente disponibile in Microsoft Azure. Ogni tecnologia presenta un set di funzionalità leggermente diverso, pertanto è possibile scegliere l'una o l'altra, o entrambe, a seconda delle esigenze della particolare soluzione in uso o di un problema aziendale o tecnico da risolvere.

Quando si stabilisce la tecnologia di accodamento che soddisfa lo scopo di una determinata soluzione, è consigliabile che gli architetti e gli sviluppatori di soluzioni tengano in considerazione le indicazioni seguenti. Per altri dettagli, vedere la sezione successiva.

Gli architetti e gli sviluppatori di soluzioni **dovrebbero considerare l'uso delle code di Azure** quando:

* L'applicazione deve archiviare in una coda oltre 80 GB di messaggi la cui durata è inferiore a 7 giorni.
* L'applicazione deve tenere traccia dello stato dell'elaborazione di un messaggio all'interno della coda. Ciò si rivela utile se si verifica un arresto anomalo del processo di lavoro tramite cui si elabora il messaggio. Queste informazioni possono quindi essere usate in un processo di lavoro successivo per continuare dal punto in cui è stato interrotto il processo di lavoro precedente.
* Sono necessari i log sul lato server di tutte le transazioni eseguite nelle code.

Gli architetti e gli sviluppatori di soluzioni **dovrebbero considerare l'uso delle code del bus di servizio** quando:

* La soluzione deve essere in grado di ricevere messaggi senza dover eseguire il polling della coda. Con il bus di servizio, è possibile ottenere questo risultato mediante l'operazione di ricezione con polling prolungato che usa i protocolli basati su TCP supportati dal bus di servizio.
* La soluzione deve garantire il recapito ordinato FIFO (First In First Out) da parte della coda.
* Si vuole una configurazione simmetrica in Azure e su Windows Server (cloud privato). Per altre informazioni, vedere [Bus di servizio per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).
* La soluzione deve essere in grado di eseguire il rilevamento duplicato automatico.
* L'applicazione deve elaborare i messaggi come flussi paralleli a esecuzione prolungata (i messaggi sono associati a un flusso tramite la proprietà [SessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) del messaggio). In questo modello ogni nodo dell'applicazione che utilizza il servizio entra in competizione con gli altri nodi per l'acquisizione dei flussi anziché dei messaggi. Quando un flusso viene assegnato a un nodo basato sul servizio, tale nodo può esaminare lo stato del flusso dell'applicazione mediante transazioni.
* Per la soluzione sono necessari atomicità e comportamento transazionale in caso di invio o ricezione di più messaggi da una coda.
* La caratteristica relativa alla durata (TTL) del carico di lavoro specifico dell'applicazione può superare il periodo di 7 giorni.
* L'applicazione gestisce messaggi che possono superare i 64 KB ma che probabilmente non si avvicineranno al limite dei 256 KB
* È necessario fornire un modello di accesso basato sui ruoli alle code e autorizzazioni/diritti diversi per mittenti e destinatari.
* Le dimensioni della coda non supereranno gli 80 GB.
* Si vuole usare il protocollo di messaggistica basato sugli standard AMQP 1.0. Per altre informazioni su AMQP, vedere [Panoramica di AMQP per il bus di servizio](service-bus-amqp-overview.md).
* È possibile prevedere un'eventuale migrazione dalla comunicazione punto a punto basata sulla coda a un modello di scambio dei messaggi mediante il quale viene garantita un'integrazione continua di destinatari aggiuntivi (sottoscrittori), mediante ognuno dei quali vengono ricevute copie separate di alcuni o di tutti i messaggi inviati alla coda. Quest'ultima viene definita funzionalità di pubblicazione/sottoscrizione a livello nativo fornita dal bus di servizio.
* La soluzione di messaggistica deve garantire il recapito "At-Most-Once" senza che sia necessario sviluppare i componenti aggiuntivi dell'infrastruttura.
* Si desidera essere in grado di pubblicare e usare batch di messaggi.
* È necessaria l'integrazione completa con lo stack di comunicazione Windows Communication Foundation (WCF) in .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Confronto tra le code di Azure e le code del bus di servizio
Le tabelle nelle sezioni seguenti forniscono un raggruppamento logico delle funzionalità relative alle code e offrono la possibilità di un confronto immediato delle funzionalità disponibili sia nelle code di Azure sia in quelle del bus di servizio.

## <a name="foundational-capabilities"></a>Funzionalità fondamentali
Questa sezione confronta alcune delle funzionalità di accodamento fondamentali fornite dalle code di Azure e da quelle del bus di servizio.

| Criteri di confronto | Code di Azure | Code del bus di servizio |
| --- | --- | --- |
| Garanzia di ordinamento |**No** <br/><br>Per altre informazioni, vedere la prima nota della sezione "Informazioni aggiuntive".</br> |**Sì - First-In-First-Out (FIFO)**<br/><br>(tramite l'uso di sessioni di messaggistica) |
| Garanzia di recapito |**At-Least-Once** |**At-Least-Once**<br/><br/>**At-Most-Once** |
| Supporto per l'operazione atomica |**No** |**Sì**<br/><br/> |
| Comportamento di ricezione |**Non-blocking**<br/><br/>(viene completata immediatamente se non vengono trovati altri messaggi) |**Blocking with/without timeout**<br/><br/>(offre disponibilità di polling prolungato o la ["Tecnica Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Non-blocking**<br/><br/>(solo tramite l'uso di interfaccia API gestita di .NET) |
| API di tipo push |**No** |**Sì**<br/><br/>Interfaccia API di .NET per sessioni [OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) e **OnMessage**. |
| Modalità di ricezione |**Visualizzazione e lease** |**Visualizzazione e blocco**<br/><br/>**Ricezione ed eliminazione** |
| Modalità di accesso esclusivo |**Basato sul lease** |**Basato sul blocco** |
| Durata lease/blocco |**30 secondi (impostazione predefinita)**<br/><br/>**7 giorni (durata massima)** (È possibile rinnovare o rilasciare un lease di messaggio tramite l'interfaccia API di [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx)). |**60 secondi (impostazione predefinita)**<br/><br/>È possibile rinnovare un blocco di messaggio tramite l'interfaccia API di [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx). |
| Precisione lease/blocco |**Livello di messaggio**<br/><br/>(ogni messaggio può avere un valore di timeout diverso che è possibile aggiornare come necessario durante l'elaborazione del messaggio tramite l'interfaccia API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx)). |**Livello di coda**<br/><br/>(ogni coda ha una precisione di blocco applicata a tutti i relativi messaggi, tuttavia è possibile rinnovare il blocco tramite l'interfaccia API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx)). |
| Ricezione in batch |**Sì**<br/><br/>(specifica esplicitamente il numero di messaggi durante il recupero dei messaggi, fino a un massimo di 32 messaggi). |**Sì**<br/><br/>(abilita implicitamente una proprietà di prelettura o esplicitamente tramite l'uso di transazioni). |
| Invio in batch |**No** |**Sì**<br/><br/>(tramite l'uso di transazioni o invio in batch sul lato client). |

### <a name="additional-information"></a>Informazioni aggiuntive
* In genere i messaggi nelle code di Azure sono ordinati in base al principio FIFO (First-In-First-Out), ma in alcuni casi possono avere un ordine diverso. Ad esempio, quando la durata del timeout di visibilità di un messaggio scade come risultato dell'arresto di un'applicazione client durante l'elaborazione. Quando il timeout di visibilità scade, il messaggio risulta nuovamente visibile nella coda in modo che un altro utente possa rimuoverlo. A questo punto, il messaggio reso nuovamente visibile può essere posizionato nella coda (per poter essere rimosso di nuovo) dopo un messaggio originariamente accodato dopo quest'ultimo.
* Se all'uso di BLOB e tabelle di Archiviazione di Microsoft Azure si affianca quello delle code, viene garantita una disponibilità pari al 99,9%. Se si usano tabelle o BLOB con code del bus di servizio, la disponibilità risulta ridotta.
* Il modello FIFO garantito nelle code del bus di servizio richiede l'uso di sessioni di messaggistica. Se l'applicazione termina di funzionare in maniera anomala durante l'elaborazione di un messaggio ricevuto nella modalità **Visualizzazione e blocco**, la prossima volta che un ricevitore di code accetta una sessione di messaggistica, l'applicazione verrà avviata con il messaggio non recapitato dopo la scadenza della relativa durata (TTL).
* Le code di Azure sono progettate per supportare scenari di accodamento standard, ad esempio componenti dell'applicazione per il disaccoppiamento per aumentare la scalabilità e la tolleranza di errore, il livellamento del carico e i flussi di lavoro per la definizione dei processi.
* Le code del bus di servizio supportano la garanzia di recapito *At-Least-Once*. La semantica *At-Most-Once* può anche essere supportata tramite lo stato della sessione per archiviare lo stato dell'applicazione e tramite le transazioni per ricevere i messaggi in modo atomico e aggiornare lo stato della sessione.
* Le code di Azure offrono un modello di programmazione uniforme e coerente tra code, tabelle e BLOB, che può essere usato sia dagli sviluppatori sia dai team addetti alle operazioni.
* Le code del bus di servizio offrono supporto per le transazioni locali nel contesto di una singola coda.
* La modalità **Ricezione ed eliminazione** supportata dal bus di servizio offre la possibilità di ridurre il numero di operazioni di messaggistica (e relativo costo) in cambio di una garanzia di recapito più bassa.
* Le code di Azure forniscono lease con possibilità di estensione per i messaggi. In questo modo i processi di lavoro possono gestire lease brevi nei messaggi. Pertanto, se un processo di lavoro viene arrestato in modo anomalo, il messaggio può essere elaborato di nuovo rapidamente da un altro processo di lavoro. Inoltre, tramite un processo di lavoro è possibile estendere il lease in un messaggio se l'elaborazione di quest'ultimo richiede più tempo del lease corrente.
* Le code di Azure offrono un timeout di visibilità che può essere impostato durante l'inserimento o la rimozione dalla coda di un messaggio. È anche possibile aggiornare un messaggio con valori di lease diversi in fase di esecuzione, nonché aggiornare valori diversi nei messaggi presenti nella stessa coda. I timeout dei blocchi del bus di servizio vengono definiti nei metadati della coda. È tuttavia possibile rinnovare il blocco chiamando il metodo [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).
* Il timeout massimo per un'operazione di ricezione del blocco nelle code del bus di servizio è di 24 giorni. I timeout basati su REST, tuttavia, hanno un valore massimo di 55 secondi.
* Il batch sul lato client fornito dal bus di servizio consente a un client di coda di inviare in batch più messaggi in una singola operazione di invio. L'invio in batch è disponibile solo per le operazioni di invio asincrone.
* Caratteristiche quali la capacità massima di 200 TB (capacità ancora superiore se gli account vengono virtualizzati) e la disponibilità in numero illimitato rendono le code di Azure una piattaforma ideale per i provider SaaS.
* Le code di Azure forniscono un meccanismo di controllo di accesso delegato flessibile ed efficiente.

## <a name="advanced-capabilities"></a>Funzionalità avanzate
Questa sezione confronta le funzionalità avanzate fornite dalle code di Azure e da quelle del bus di servizio.

| Criteri di confronto | Code di Azure | Code del bus di servizio |
| --- | --- | --- |
| Recapito pianificato |**Sì** |**Sì** |
| Mancato recapito automatico dei messaggi |**No** |**Sì** |
| Valore TTL di accodamento aumentato |**Sì**<br/><br/>(tramite aggiornamento sul posto del timeout di visibilità) |**Sì**<br/><br/>(specificato tramite una funzione API dedicata) |
| Supporto messaggi non elaborabili |**Sì** |**Sì** |
| Aggiornamento sul posto |**Sì** |**Sì** |
| Log delle transazioni sul lato server |**Sì** |**No** |
| Metriche di archiviazione |**Sì**<br/><br/>**Metrica al minuto**: specifica metriche in tempo reale relative a disponibilità, TPS, numero di chiamate API, numero di errori e molto altro, aggregate per minuto e segnalate entro pochi minuti rispetto agli eventi in fase di produzione. Per altre informazioni, vedere [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx) (Informazioni sulle metriche di analisi dell'archiviazione). |**Sì**<br/><br/>(query in blocco tramite chiamate a [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx)) |
| Gestione dello stato |**No** |**Sì**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx) |
| Inoltro automatico dei messaggi |**No** |**Sì** |
| Funzione di eliminazione della coda |**Sì** |**No** |
| Gruppi di messaggi |**No** |**Sì**<br/><br/>(tramite l'uso di sessioni di messaggistica) |
| Stato dell'applicazione per gruppo di messaggi |**No** |**Sì** |
| Rilevamento duplicati |**No** |**Sì**<br/><br/>(configurabile sul lato del mittente) |
| Integrazione WCF |**No** |**Sì**<br/><br/>(offre associazioni WCF predefinite) |
| Integrazione WF |**Impostazione personalizzata**<br/><br/>(richiede la compilazione di un'attività WF personalizzata) |**Impostazione nativa**<br/><br/>(offre attività WF predefinite) |
| Esplorazione di gruppi di messaggi |**No** |**Sì** |
| Recupero delle sessioni di messaggistica per ID |**No** |**Sì** |

### <a name="additional-information"></a>Informazioni aggiuntive
* Entrambe le tecnologie di accodamento consentono di pianificare il recapito di un messaggio in un momento successivo.
* La funzionalità di inoltro automatico consente a migliaia di code di inoltrare automaticamente i propri messaggi a una singola coda dalla quale l'applicazione ricevente preleva il messaggio. Questo meccanismo consente di ottenere un valore elevato di sicurezza, di controllare il flusso e di isolare le aree di archiviazione tra i server di pubblicazione dei messaggi.
* Le code di Azure offrono supporto per l'aggiornamento del contenuto del messaggio. Questa funzionalità consente di rendere permanenti le informazioni sullo stato e gli aggiornamenti sull'avanzamento incrementale all'interno del messaggio, in modo che sia possibile elaborare quest'ultimo dall'ultimo checkpoint noto anziché dall'inizio. Con le code del bus di servizio è possibile abilitare lo stesso scenario mediante sessioni di messaggistica Le sessioni consentono di salvare e recuperare lo stato di elaborazione dell'applicazione tramite [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx).
* La funzionalità [messaggi non recapitabili](service-bus-dead-letter-queues.md), supportata solo dalle code del bus di servizio, può essere utile per isolare i messaggi che non possono essere elaborati correttamente dall'applicazione ricevente o quando i messaggi non possono essere recapitati a causa di una proprietà di durata (TTL) scaduta. Tramite il valore TTL viene specificata la durata del messaggio nella coda. Con il bus di servizio, il messaggio verrà spostato in una coda speciale denominata $DeadLetterQueue allo scadere del periodo TTL.
* Per trovare messaggi "non elaborabili" nelle code di Azure, durante la rimozione di un messaggio dalla coda, l'applicazione esamina la proprietà **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** del messaggio. Se il valore della proprietà **DequeueCount** supera la soglia specificata, l'applicazione sposta il messaggio in una coda di "messaggio non recapitabile" definita dall'applicazione.
* Le code di Azure consentono di ottenere un log dettagliato di tutte le transazioni eseguite nella coda, nonché le metriche di aggregazione. Queste opzioni sono entrambe utili per eseguire il debug e comprendere la modalità d'uso delle code di Azure da parte dell'applicazione. Inoltre, sono utili per l'ottimizzazione delle prestazioni dell'applicazione e per la riduzione dei costi d'uso delle code.
* Il concetto di "sessione di messaggistica" supportato dal bus di servizio consente di associare a uno specifico destinatario i messaggi appartenenti a un determinato gruppo logico. Il destinatario crea a sua volta un'affinità di tipo sessione tra i messaggi e i relativi destinatari. È possibile abilitare questa funzionalità avanzata nel bus di servizio impostando la proprietà [SessionID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) in un messaggio. I destinatari possono quindi restare in ascolto su un ID di sessione specifico e ricevere messaggi in cui viene condiviso l'identificatore di sessione specificato.
* La funzionalità di rilevamento di duplicazione supportata dalle code del bus di servizio rimuove automaticamente i messaggi duplicati inviati a una coda o a un argomento, in base al valore della proprietà [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx).

## <a name="capacity-and-quotas"></a>Capacità e quote
Questa sezione confronta le code di Azure e quelle del bus di servizio dalla prospettiva di [capacità e quote](service-bus-quotas.md) da applicare.

| Criteri di confronto | Code di Azure | Code del bus di servizio |
| --- | --- | --- |
| Dimensioni massime della coda |**200 TB**<br/><br/>(limitate alla capacità di un singolo account di archiviazione) |**Da 1 GB a 80 GB**<br/><br/>(valori definiti al momento della creazione della coda e dell'[abilitazione del partizionamento](service-bus-partitioning.md). Vedere la sezione "Informazioni aggiuntive"). |
| Dimensioni massime del messaggio |**64 KB**<br/><br/>(48 KB quando si usa una codifica **Base64**)<br/><br/>Poiché Azure supporta messaggi di grandi dimensioni combinando code e BLOB, è possibile accodare fino a 200 GB per un singolo elemento. |**256 KB** o **1 MB**<br/><br/>(inclusi l'intestazione e il corpo, dimensioni massime dell'intestazione: 64 KB).<br/><br/>Dipende dal [livello di servizio](service-bus-premium-messaging.md). |
| Durata TTL massima del messaggio |**7 giorni** |**`TimeSpan.Max`** |
| Numero massimo di code |**Illimitato** |**10.000**<br/><br/>(per spazio dei nomi del servizio, può essere aumentato) |
| Numero massimo di client concorrenti |**Illimitato** |**Illimitato**<br/><br/>(limite di 100 connessioni simultanee applicato solo alla comunicazione basata su protocollo TCP) |

### <a name="additional-information"></a>Informazioni aggiuntive
* Bus di servizio impone l'applicazione dei limiti di dimensione della coda. Le dimensioni massime della coda vengono specificate al momento della creazione della coda stessa e possono avere un valore compreso tra 1 e 80 GB. Se viene raggiunto il valore delle dimensioni della coda impostato al momento della creazione, i successivi messaggi in arrivo verranno rifiutati e il codice chiamante riceverà un'eccezione. Per altre informazioni sulle quote nel bus di servizio, vedere [Quote del bus di servizio](service-bus-quotas.md).
* È possibile creare code del bus di servizio in dimensioni di 1, 2, 3, 4 o 5 GB (il valore predefinito è 1 GB). Con il partizionamento abilitato (ovvero l'impostazione predefinita), il bus di servizio crea 16 partizioni per ciascun GB specificato. Di conseguenza, se si crea una coda di 5 GB di dimensioni, con 16 partizioni la dimensione massima della coda diventa (5 * 16) = 80 GB. È possibile visualizzare le dimensioni massime della coda partizionata o dell'argomento esaminando relativa voce nel [portale di Azure][portale di Azure].
* Con le code di Azure, se il contenuto del messaggio non è XML-safe, deve avere la codifica **Base64**. Se per il messaggio non è stata usata la codifica **Base64**, il payload dell'utente può essere fino a 48 KB, anziché 64.
* Con le code del bus di servizio, ogni messaggio archiviato in una coda è costituito da due parti: un'intestazione e un corpo. Le dimensioni totali del messaggio non possono superare la dimensione massima del messaggio supportata dal livello di servizio.
* Se le comunicazioni tra client e code del bus di servizio vengono stabilite tramite il protocollo TCP, il numero massimo di connessioni simultanee a una singola coda del bus di servizio è limitato a 100. Questo numero è condiviso tra mittenti e destinatari. Se viene raggiunta questa quota, le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Questo limite non viene imposto ai client tramite cui viene effettuata la connessione alle code mediante l'API basata su REST.
* Se si richiedono più di 10.000 code in un unico spazio dei nomi del bus di servizio, è possibile contattare il team di supporto tecnico di Azure e richiedere un aumento. Per ridimensionare più di 10.000 code con il bus di servizio, è possibile anche creare altri spazi dei nomi usando il [portale di Azure][portale di Azure].

## <a name="management-and-operations"></a>Gestione e operazioni
Questa sezione confronta le funzionalità di gestione fornite dalle code di Azure e da quelle del bus di servizio.

| Criteri di confronto | Code di Azure | Code del bus di servizio |
| --- | --- | --- |
| Protocollo di gestione |**REST su HTTP/HTTPS** |**REST su HTTPS** |
| Protocollo runtime |**REST su HTTP/HTTPS** |**REST su HTTPS**<br/><br/>**Standard AMQP 1.0 (TCP con TLS)** |
| API gestita .NET |**Sì**<br/><br/>(API del client di archiviazione gestita .NET) |**Sì**<br/><br/>(API di messaggistica negoziata gestita .NET) |
| C++ nativo |**Sì** |**No** |
| API Java |**Sì** |**Sì** |
| API PHP |**Sì** |**Sì** |
| API Node.js |**Sì** |**Sì** |
| Supporto metadati arbitrario |**Sì** |**No** |
| Regole di denominazione delle code |**Può contenere fino a 63 caratteri**<br/><br/>(le lettere del nome di una coda devono essere minuscole). |**Può contenere fino a 260**<br/><br/>(i percorsi e i nomi di code non fanno distinzione tra maiuscole e minuscole). |
| Funzione di recupero della lunghezza della coda |**Sì**<br/><br/>(valore indicativo se i messaggi scadono dopo il TTL senza essere eliminati). |**Sì**<br/><br/>(Valore esatto e temporizzato). |
| Funzione di visualizzazione |**Sì** |**Sì** |

### <a name="additional-information"></a>Informazioni aggiuntive
* Le code di Azure forniscono supporto per gli attributi arbitrari applicabili alla descrizione della coda, sotto forma di coppie nome/valore.
* Entrambe le tecnologie di coda consentono di visualizzare un messaggio senza doverlo bloccare. Questa funzionalità può essere utile quando si implementa uno strumento di esplorazione delle code.
* Le API gestite .NET di messaggistica negoziata del bus di servizio usano le connessioni TCP full-duplex per ottenere migliori livelli di prestazioni rispetto a REST su HTTP e supportano il protocollo standard AMQP 1.0.
* I nomi delle code di Azure possono avere una lunghezza compresa tra 3 e 63 caratteri e contenere lettere minuscole, numeri e trattini. Per altre informazioni, vedere [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Denominazione di code e metadati).
* I nomi delle code del bus di servizio possono avere una lunghezza massima di 260 caratteri e presentano regole di denominazione meno restrittive. I nomi delle code del bus di servizio possono contenere lettere, numeri, punti, trattini e caratteri di sottolineatura.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione
Questa sezione illustra le funzionalità di autenticazione e autorizzazione supportate dalle code di Azure e da quelle del bus di servizio.

| Criteri di confronto | Code di Azure | Code del bus di servizio |
| --- | --- | --- |
| Autenticazione |**Chiave simmetrica** |**Chiave simmetrica** |
| Modello di protezione |Accesso delegato tramite token di firma di accesso condiviso. |SAS |
| Federazione del provider di identità |**No** |**Sì** |

### <a name="additional-information"></a>Informazioni aggiuntive
* Ogni richiesta a entrambe le tecnologie di accodamento deve essere autenticata. Le code pubbliche con accesso anonimo non sono supportate. L'uso di [SAS](service-bus-sas-overview.md) consente di ovviare a questo inconveniente tramite la pubblicazione di una firma SAS di sola scrittura, di sola lettura o anche di accesso completo.
* Lo schema di autenticazione offerto dalle code di Azure prevede l'uso di una chiave simmetrica, ovvero un codice HMAC (Hash Message Authentication Code) calcolato dall'algoritmo SHA-256 e codificato come stringa **Base64**. Per altre informazioni sul relativo protocollo, vedere [Authentication for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx) (Autenticazione per i servizi di archiviazione di Azure). Le code del bus di servizio supportano un modello simile mediante l'uso di chiavi simmetriche. Per altre informazioni, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Costi
Questa sezione confronta le code di Azure e quelle del bus di servizio relativamente ai costi.

| Criteri di confronto | Code di Azure | Code del bus di servizio |
| --- | --- | --- |
| Costo della transazione della coda |**$0.0036**<br/><br/>(per 100.000 transazioni) |**Livello basico**: **$0.05**<br/><br/>(per un milione di operazioni) |
| Operazioni fatturabili |**Tutti** |**Solo invio/ricezione**<br/><br/>(nessun addebito per altre operazioni) |
| Transazioni inattive |**Fatturabile**<br/><br/>(l'esecuzione di query su una coda vuota viene conteggiata come transazione fatturabile). |**Fatturabile**<br/><br/>(una ricezione in una coda vuota è considerata un messaggio fatturabile). |
| Costo di archiviazione |**$0.07**<br/><br/>(per GB/mese) |**$0.00** |
| Costi di trasferimento dei dati in uscita |**$0.12 - $0.19**<br/><br/>(il valore varia a seconda dell'area geografica). |**$0.12 - $0.19**<br/><br/>(il valore varia a seconda dell'area geografica). |

### <a name="additional-information"></a>Informazioni aggiuntive
* I trasferimenti dei dati vengono addebitati in base alla quantità totale di dati che vengono trasferiti dai data center di Azure tramite Internet in un determinato periodo di fatturazione.
* I trasferimenti dei dati tra servizi di Azure ubicati all'interno della stessa area non sono soggetti ad addebito.
* Al momento della stesura di questo documento, tutti i trasferimenti dei dati in ingresso non sono soggetti ad addebito.
* In base al supporto per polling prolungato, l'uso di code del bus di servizio può essere economicamente conveniente in situazioni in cui è richiesto il recapito a bassa latenza.

> [!NOTE]
> Tutti i costi sono soggetti a modifiche. Questa tabella presenta il prezzo corrente e non include i costi di eventuali offerte promozionali che possono essere attualmente disponibili. Per informazioni aggiornate sui prezzi di Azure, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/). Per altre informazioni sui prezzi del bus di servizio, vedere la pagina relativa ai [prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).
> 
> 

## <a name="conclusion"></a>Conclusioni
Acquisendo una comprensione più profonda delle due tecnologie, si sarà in grado di prendere una decisione più oculata circa la tecnologia di accodamento da usare e il momento in cui adottarla. La scelta di usare le code di Azure o quelle del bus di servizio si basa chiaramente su diversi fattori, che possono dipendere soprattutto dalle singole esigenze dell'applicazione in uso e dalla relativa architettura. Se l'applicazione usa già le funzionalità principali di Microsoft Azure, è possibile scegliere le code di Azure, specialmente se sono richieste la comunicazione e la messaggistica di base tra servizi o se sono necessarie code che possono avere dimensioni maggiori di 80 GB.

Poiché offrono numerose funzionalità avanzate quali sessioni, transazioni rilevamento duplicati, mancato recapito automatico dei messaggi e funzionalità di pubblicazione e sottoscrizione permanente, le code del bus di servizio possono rappresentare la soluzione migliore quando si sviluppa un'applicazione ibrida o se l'applicazione in uso richiede comunque tali funzionalità.

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti offrono indicazioni e informazioni sull'uso delle code di Azure e di quelle del bus di servizio.

* [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare il servizio di archiviazione code](../storage/storage-dotnet-how-to-use-queues.md)
* [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio](service-bus-performance-improvements.md)
* [Introduzione alle code e agli argomenti del bus di servizio di Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Guida per gli sviluppatori del bus di servizio](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Uso del servizio di accodamento in Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
* [Informazioni sulla fatturazione di archiviazione di Azure (larghezza di banda, transazioni e capacità](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[portale di Azure]: https://portal.azure.com




<!--HONumber=Nov16_HO3-->


