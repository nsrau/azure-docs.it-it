---
title: Elenco di controllo di prestazioni e scalabilità di Archiviazione tabelle - Archiviazione di Azure
description: Un elenco di controllo delle procedure consolidate per l'uso con Archiviazione tabelle nello sviluppo di applicazioni ad elevate prestazioni.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: b36ed2cac7e5009a0581091252b36dcd5af81bd7
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389984"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Elenco di controllo di prestazioni e scalabilità di Archiviazione tabelle

Microsoft ha creato una serie di procedure consolidate per lo sviluppo di applicazioni ad elevate prestazioni con Archiviazione tabelle. Questo elenco di controllo identifica le procedure chiave che gli sviluppatori possono seguire per ottimizzare le prestazioni. Tenere presenti queste procedure durante la progettazione dell'applicazione e dell'intero processo.

Archiviazione di Azure ha degli obiettivi di scalabilità per la capacità, la frequenza di transazioni e la larghezza di banda. Per altre informazioni sugli obiettivi di scalabilità per Archiviazione di Azure, vedere [Obiettivi di scalabilità e prestazioni per gli account di archiviazione](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="checklist"></a>Elenco di controllo

Questo articolo organizza procedure consolidate per le prestazioni in un elenco di controllo che è possibile seguire durante lo sviluppo dell'applicazione di Archiviazione tabelle.

| Operazione completata | Categoria | Considerazioni sulla progettazione |
| --- | --- | --- |
| &nbsp; |Obiettivi di scalabilità |[È possibile progettare l'applicazione in modo da non eccedere il numero massimo di account di archiviazione?](#maximum-number-of-storage-accounts) |
| &nbsp; |Obiettivi di scalabilità |[Si sta evitando il raggiungimento dei limiti di capacità e transazioni?](#capacity-and-transaction-targets) |
| &nbsp; |Rete |[I dispositivi sul lato client hanno una larghezza di banda sufficientemente alta e una latenza sufficientemente bassa per raggiungere le prestazioni richieste?](#throughput) |
| &nbsp; |Rete |[I dispositivi sul lato client hanno un collegamento di qualità elevata?](#link-quality) |
| &nbsp; |Rete |[L'applicazione client si trova nella stessa area dell'account di archiviazione?](#location) |
| &nbsp; |Accesso client diretto |[Si usano le firme di accesso condiviso e la condivisione di risorse tra le origini per abilitare l'accesso diretto ad Archiviazione di Azure?](#sas-and-cors) |
| &nbsp; |Creazione di batch |[L'applicazione sta creando batch di aggiornamenti usando transazioni di gruppi di entità?](#batch-transactions) |
| &nbsp; |Configurazione .NET |[Si usa .NET Core 2.1 o versione successiva per ottenere prestazioni ottimali?](#use-net-core) |
| &nbsp; |Configurazione .NET |[Il client è stato configurato per usare un numero sufficiente di connessioni simultanee?](#increase-default-connection-limit) |
| &nbsp; |Configurazione .NET |[Per le applicazioni .NET, è stato configurato l'uso di un numero sufficiente di thread?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelismo |[È stata verificata la corretta associazione del parallelismo in modo da non sovraccaricare le capacità del client o raggiungere gli obiettivi di scalabilità?](#unbounded-parallelism) |
| &nbsp; |Strumenti |[Si sta usando l'ultima versione delle librerie e degli strumenti client forniti da Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativi |[Si sta usando un criterio per l'esecuzione di nuovi tentativi per il backoff esponenziale per gli errori di limitazione e i timeout?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativi |[L'applicazione sta evitando nuovi tentativi in caso di errori irreversibili?](#non-retryable-errors) |
| &nbsp; |Obiettivi di scalabilità |[Si stanno raggiungendo gli obiettivi di scalabilità per le entità al secondo?](#table-specific-scalability-targets) |
| &nbsp; |Configurazione |[Si sta usando JSON per le richieste della tabella?](#use-json) |
| &nbsp; |Configurazione |[L'algoritmo Nagle è stato disattivato per migliorare le prestazioni per le piccole richieste?](#disable-nagle) |
| &nbsp; |Tabelle e partizioni |[I dati sono stati partizionati correttamente?](#schema) |
| &nbsp; |Partizioni critiche |[Si stanno evitando i modelli Solo accodamenti e Solo anteposizioni?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Partizioni critiche |[Gli inserimenti/aggiornamenti sono distribuiti in più partizioni?](#high-traffic-data) |
| &nbsp; |Ambito delle query |[Lo schema è stato progettato per consentire l'uso delle query di tipo punto nella maggior parte dei casi e delle query sulle tabelle solo in casi limitati?](#query-scope) |
| &nbsp; |Densità delle query |[Le query in genere analizzano e restituiscono solo le righe usate dall'applicazione?](#query-density) |
| &nbsp; |Limitazione dei dati restituiti |[Vengono usati dei filtri per evitare che vengano restituite entità non necessarie?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Limitazione dei dati restituiti |[Viene usata la proiezione per evitare che vengano restituite proprietà non necessarie?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizzazione |[I dati sono stati denormalizzati in modo da evitare query non efficaci o più richieste di lettura quando si tenta di ottenere i dati?](#denormalization) |
| &nbsp; |Inserire, aggiornare ed eliminare |[Si stanno creando dei batch per le richieste che devono essere transazionali o che possono essere eseguite simultaneamente per ridurre i round-trip?](#batching) |
| &nbsp; |Inserire, aggiornare ed eliminare |[Si sta evitando di recuperare un'entità solo per determinare se chiamare un inserimento o un aggiornamento?](#upsert) |
| &nbsp; |Inserire, aggiornare ed eliminare |[È stata considerata l'archiviazione di serie di dati che vengono spesso recuperate insieme in una singola entità sotto forma di proprietà invece che in più entità?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Inserire, aggiornare ed eliminare |[Per le entità recuperate sempre insieme e che possono essere scritte in batch (ad esempio, dati di serie temporali) è stato valutato l'uso di BLOB invece che di tabelle?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Obiettivi di scalabilità

Se l'applicazione raggiunge o supera uno o più obiettivi di scalabilità, può verificarsi un aumento delle latenze o delle limitazioni della transazione. Quando Archiviazione di Azure limita l'applicazione, il servizio inizia a restituire i codici di errore "503 Server occupato" o "500 Timeout operazione". Evitare questi errori rispettando i limiti degli obiettivi di scalabilità è una parte importante del miglioramento delle prestazioni dell'applicazione.

Per altre informazioni sugli obiettivi di scalabilità per il servizio tabelle, vedere [Obiettivi di scalabilità e prestazioni di Archiviazione di Azure per gli account di archiviazione](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2ftables%2ftoc.json#azure-table-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>Numero massimo di account di archiviazione

Se si sta per raggiungere il numero massimo di account di archiviazione consentiti per una determinata combinazione di sottoscrizione/area, si usano più account di archiviazione da partizionare per aumentare il traffico in ingresso, in uscita, le operazioni di I/O al secondo o la capacità? In questo scenario, se possibile Microsoft consiglia di sfruttare l'innalzamento dei limiti degli account di archiviazione per ridurre il numero di account di archiviazione necessari per il carico di lavoro. Contattare il [supporto di Azure](https://azure.microsoft.com/support/options/) per richiedere l'espansione dei limiti dell'account di archiviazione. Per altre informazioni, vedere [Annuncio di account di archiviazione di dimensioni maggiori a scalabilità più elevata](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Obiettivi di capacità e transazioni

Se l'applicazione sta raggiungendo gli obiettivi di scalabilità per un singolo account di archiviazione, valutare uno dei seguenti approcci:  

- Esaminare di nuovo il carico di lavoro che causa il raggiungimento o il superamento dell'obiettivo di scalabilità da parte dell'applicazione. È possibile progettarlo in modo diverso in modo che usi una quantità minore di larghezza di banda o capacità o un minor numero di transazioni?
- Se l'applicazione deve superare uno degli obiettivi di scalabilità, creare più account di archiviazione e partizionare i dati dell'applicazione tra questi account di archiviazione. Se si usa questo modello, assicurarsi di progettare l'applicazione in modo da aggiungere altri account di archiviazione in futuro per il bilanciamento del carico. Gli stessi account di archiviazione non hanno costi aggiuntivi rispetto a quelli per l'uso, ossia associati ai dati archiviati, alle transazioni effettuate o ai dati trasferiti.
- Se l'applicazione sta raggiungendo gli obiettivi di larghezza di banda, valutare la compressione dei dati sul lato client per ridurre la larghezza di banda necessaria a inviare i dati ad Archiviazione di Azure.
    La compressione dei dati, pur consentendo di risparmiare larghezza di banda e migliorare le prestazioni di rete, può avere anche degli effettivi negativi sulle prestazioni. Valutare gli effetti sulle prestazioni dei requisiti di elaborazione aggiuntivi per la compressione e la decompressione dei dati sul lato client. Tenere presente che l'archiviazione dei dati compressi può rendere più difficile la risoluzione dei problemi perché ostacola la visualizzazione dei dati usando gli strumenti standard.
- Se l'applicazione sta raggiungendo gli obiettivi di scalabilità, assicurarsi di usare un backoff esponenziale per i nuovi tentativi. È consigliabile provare a evitare di raggiungere gli obiettivi di scalabilità implementando i consigli descritti in questo articolo. Tuttavia, l'uso di un backoff esponenziale per i tentativi impedisce all'applicazione di ritentare rapidamente, il che potrebbe peggiorare la limitazione delle richieste. Per altre informazioni, vedere la sezione intitolata [Errori di timeout e server occupato](#timeout-and-server-busy-errors).

## <a name="table-specific-scalability-targets"></a>Obiettivi di scalabilità specifici per tabelle

Oltre ai limiti della larghezza di banda dell'intero account di archiviazione, le tabelle hanno il seguente limite di scalabilità. Il sistema bilancia il carico man mano che il traffico aumenta, ma se si verificano incrementi improvvisi del traffico, il volume della velocità effettiva potrebbe non essere raggiunto immediatamente. Se il modello presenta degli incrementi improvvisi è probabile che vengano visualizzai messaggi di limitazione e/o timeout durante l'incremento perché il servizio di archiviazione bilancia automaticamente il carico della tabella. Un incremento lento in genere produce risultati migliori perché lascia al sistema il tempo di bilanciare il carico in modo corretto.

## <a name="networking"></a>Rete

I vincoli fisici della rete dell'applicazione possono avere effetti significativi sulle prestazioni. Le sezioni seguenti descrivono alcune limitazioni che gli utenti possono incontrare.  

### <a name="client-network-capability"></a>Capacità della rete client

La larghezza di banda e la qualità del collegamento di rete svolgono ruoli importanti nelle prestazioni dell'applicazione, come descritto nelle sezioni seguenti.

#### <a name="throughput"></a>Velocità effettiva

Per la larghezza di banda il problema dipende spesso dalle capacità del client. Le istanze di Azure più grandi possono avere schede di interfaccia di rete con capacità più elevate ed è quindi opportuno prendere in considerazione l'uso di un'istanza più grande o di più macchine virtuali se è necessario che un singolo computer abbia limiti di rete più elevati. Se si accede ad Archiviazione di Azure da un'applicazione locale, si applica la stessa regola: comprendere le capacità di rete del dispositivo client e la connettività di rete della posizione di archiviazione di Azure, quindi aumentarle in base alle esigenze o progettare l'applicazione in modo che funzioni con i limiti di capacità disponibili.

#### <a name="link-quality"></a>Qualità del collegamento

Come accade in ogni rete, tenere presente che le condizioni di rete che generano errori e perdita di pacchetti riducono la velocità effettiva.  L'uso di WireShark o NetMon può contribuire a diagnosticare il problema.  

### <a name="location"></a>Location

In qualsiasi ambiente distribuito, il posizionamento del client accanto al server offre le prestazioni migliori. Per accedere all'archiviazione di Azure con la minor latenza possibile, è opportuno posizionare il client nella stessa area di Azure. Ad esempio, se si ha un'app Web di Azure che usa Archiviazione di Azure, posizionare entrambi in un'unica area, ad esempio Stati Uniti occidentali o Asia sudorientale. Il posizionamento delle risorse nella stessa area riduce latenza e costi, in quanto l'utilizzo della larghezza di banda in un'unica area è gratuito.  

Anche se le applicazioni client hanno accesso ad Archiviazione di Azure ma non sono ospitate in Azure, ad esempio app di dispositivi mobili o servizi aziendali locali, la latenza viene ridotta se si posiziona l'account di archiviazione in un'area vicina a tali client. Se i client sono distribuiti in un'area ampia (ad esempio, alcuni in America del Nord e altri in Europa), è opportuno usare un account di archiviazione per ogni area. Questo approccio è più semplice da implementare se i dati archiviati dall'applicazione sono specifici di singoli utenti e non richiedono la replica tra gli account di archiviazione.

## <a name="sas-and-cors"></a>SAS e CORS

Si supponga che sia necessario autorizzare il codice, ad esempio JavaScript, che viene eseguito nel Web browser dell'utente o in un'app per telefoni cellulari per accedere ai dati in Archiviazione di Azure. Un approccio consiste nel creare un'applicazione di servizio che funga da proxy. Il dispositivo dell'utente esegue l'autenticazione con il servizio, che a sua volta autorizza l'accesso alle risorse di Archiviazione di Azure. In questo modo si evita di esporre le chiavi dell'account di archiviazione in dispositivi non sicuri. Questo approccio causa tuttavia un sovraccarico significativo dell'applicazione di servizio, perché tutti i dati trasferiti tra il dispositivo dell'utente e Archiviazione di Azure devono passare attraverso l'applicazione di servizio.

È possibile evitare di usare un'applicazione di servizio come proxy per Archiviazione di Azure usando firme di accesso condiviso. Con le firme di accesso condiviso è possibile consentire al dispositivo dell'utente di indirizzare le richieste direttamente ad Archiviazione di Azure usando un token con accesso limitato. Ad esempio, se un utente vuole caricare una foto nell'applicazione, l'applicazione di servizio può generare una firma di accesso condiviso e inviarla al dispositivo dell'utente. Il token di firma di accesso condiviso può concedere l'autorizzazione per scrivere in una risorsa di Archiviazione di Azure per un intervallo di tempo specificato, dopo la scadenza del token di firma di accesso condiviso. Per altre informazioni sulla firma di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).  

Un Web browser non consente in genere codice JavaScript in una pagina ospitata da un sito Web in un dominio per l'esecuzione di determinate operazioni, come operazioni di scrittura, in un altro dominio. Noto come criterio di corrispondenza dell'origine, questo criterio impedisce a uno script dannoso in una pagina di ottenere l'accesso ai dati in un'altra pagina Web. Tuttavia, i criteri di corrispondenza dell'origine possono costituire una limitazione quando si compila una soluzione nel cloud. La condivisione di risorse tra le origini è una funzionalità del browser che consente al dominio di destinazione di comunicare con il browser di cui reputa attendibili le richieste originate nel dominio di origine.

Si supponga, ad esempio, che un'applicazione Web in esecuzione in Azure faccia una richiesta di una risorsa a un account di Archiviazione di Azure. L'applicazione Web è il dominio di origine e l'account di archiviazione è il dominio di destinazione. È possibile configurare la condivisione di risorse tra le origini per qualsiasi servizio di Archiviazione di Azure in modo che comunichi con il Web browser che le richieste provenienti dal dominio di origine siano ritenuti attendibili da Archiviazione di Azure. Per altre informazioni sulla condivisione di risorse tra le origini, vedere [Supporto della condivisione delle risorse tra le origini (CORS) per Archiviazione di Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Entrambe le tecnologie SAS e CORS possono aiutare a evitare carichi non necessari nell'applicazione Web.  

## <a name="batch-transactions"></a>Transazioni batch

Il servizio tabelle supporta le transazioni batch sulle entità che si trovano nella stessa tabella e appartengono allo stesso gruppo di partizioni. Per altre informazioni, vedere la sezione [Esecuzione di transazioni di gruppi di entità](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Configurazione .NET

Se si usa .NET Framework, in questa sezione vengono elencate diverse impostazioni di configurazione rapide che è possibile usare per migliorare significativamente le prestazioni.  Se si usano altri linguaggi, controllare se esistono procedure simili per il linguaggio prescelto.  

### <a name="use-net-core"></a>Usare .NET Core

Sviluppare applicazioni di Archiviazione di Azure con .NET Core 2.1 o versione successiva per sfruttare i miglioramenti delle prestazioni. Quando possibile, è consigliabile usare .NET Core 3.x.

Per altre informazioni sui miglioramenti delle prestazioni in .NET Core, vedere i post di blog seguenti:

- [Miglioramenti delle prestazioni in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Miglioramenti delle prestazioni in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumento del limite di connessione predefinito

In .NET, il seguente codice aumenta il limite di connessione predefinito (in genere pari a 2 in un ambiente client o 10 in un ambiente server) a 100. Solitamente il valore deve essere impostato basandosi approssimativamente sul numero di thread usato dall'applicazione.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Impostare il limite di connessione prima di aprire le connessioni.  

Per gli altri linguaggi di programmazione, vedere la documentazione specifica per determinare come impostare il limite di connessione.  

Per altre informazioni, vedere il post del blog [Servizi Web: connessioni simultanee](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Aumentare il numero minimo di thread

Se si usano chiamate sincrone insieme ad attività asincrone, può essere opportuno aumentare il numero di thread nel pool di thread:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Per altre informazioni, vedere il metodo [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads).  

## <a name="unbounded-parallelism"></a>Parallelismo non associato

Sebbene il parallelismo possa essere ideale per le prestazioni, prestare attenzione quando si usa il parallelismo non associato, perché non esiste alcun limite applicato al numero di thread o di richieste parallele. Assicurarsi di limitare le richieste parallele per caricare o scaricare dati, per accedere a più partizioni nello stesso account di archiviazione o per accedere a più elementi nella stessa partizione. Se il parallelismo non è associato, l'applicazione può superare le capacità del dispositivo client o gli obiettivi di scalabilità dell'account di archiviazione producendo latenze più lunghe e limitazioni.  

## <a name="client-libraries-and-tools"></a>Librerie e strumenti client dell'archiviazione

Per ottenere le migliori prestazioni, usare sempre l'ultima versione delle librerie e degli strumenti client forniti da Microsoft. Le librerie client di Archiviazione di Azure sono disponibili per diversi linguaggi. Archiviazione di Azure supporta anche PowerShell e l'interfaccia della riga di comando di Azure. Microsoft sviluppa attivamente questi strumenti e librerie client concentrandosi sulle prestazioni, li mantiene aggiornati con le ultime versioni del servizio e verifica che siano in grado di gestire internamente gran parte delle procedure comprovate relative alle prestazioni. Per altre informazioni, vedere la [documentazione di riferimento di Archiviazione di Azure](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Gestire gli errori del servizio

Archiviazione di Azure restituisce un errore quando il servizio non riesce a elaborare una richiesta. La comprensione degli errori che possono essere restituiti da Archiviazione di Azure in un determinato scenario è utile per ottimizzare le prestazioni.

### <a name="timeout-and-server-busy-errors"></a>Errori di timeout e server occupato

Archiviazione di Azure può limitare l'applicazione se raggiunge i limiti di scalabilità. In alcuni casi, Archiviazione di Azure potrebbe non riuscire a gestire una richiesta a causa di una condizione temporanea. In entrambi i casi, il servizio può restituire un errore 503 (Server occupato) o 500 (Timeout). Questi errori possono verificarsi anche se il servizio esegue il ribilanciamento delle partizioni di dati per consentire una maggiore velocità effettiva. L'applicazione client in genere deve ripetere l'operazione che ha causato uno di questi errori. Tuttavia, se Archiviazione di Azure sta limitando l'applicazione perché supera gli obiettivi di scalabilità o non riesce a completare la richiesta per altri motivi, la ripetizione dei tentativi non farebbe che peggiorare il problema. È consigliabile l'uso di criteri di ripetizione del backoff esponenziale, ovvero il comportamento predefinito nelle librerie client. Ad esempio, l'applicazione può riprovare l'operazione dopo 2 secondi, 4 secondi, 10 secondi, 30 secondi, dopo di che non effettua altri tentativi. Piuttosto che peggiorare il problema, in questo modo si riduce notevolmente il carico dell'applicazione sul servizio che potrebbe condurre alla limitazione della larghezza di banda della rete.  

I nuovi tentativi relativi a errori di connettività possono essere eseguiti immediatamente perché non dipendono dalle limitazioni e sono considerati temporanei.  

### <a name="non-retryable-errors"></a>Errori irreversibili

Le librerie client gestiscono i tentativi con la consapevolezza degli errori che possono essere ritentati e quelli che non possono. Tuttavia, se si chiama direttamente l'API REST di Archiviazione di Azure, ci sono alcuni errori che non è consigliabile riprovare. Ad esempio, un errore 400 (Richiesta non valida) indica che l'applicazione client ha inviato una richiesta che non è stata elaborata perché non è nel formato previsto. Un nuovo invio di questa richiesta genera sempre la stessa risposta, quindi è inutile riprovare. Tuttavia, se si chiama direttamente l'API REST di Archiviazione di Azure, ci sono alcuni errori che non è consigliabile riprovare.

Per altre informazioni sui codici di errore di Archiviazione di Azure, vedere [Stato e codici errore](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Configurazione

In questa sezione vengono elencate diverse impostazioni di configurazione rapide che è possibile usare per migliorare significativamente le prestazioni nel servizio tabelle:

### <a name="use-json"></a>Usare JSON

A partire dalla versione del servizio di archiviazione 2013-08-15, il servizio tabelle supporta l'uso di JSON al posto del formato AtomPub basato su XML per il trasferimento dei dati della tabella. L'uso di JSON consente di ridurre le dimensioni del payload di una percentuale massima del 75% e può migliorare notevolmente le prestazioni dell'applicazione.

Per altre informazioni, vedere il post [Tabelle di Microsoft Azure: introduzione a JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) e al [formato Payload per operazioni del servizio tabelle](https://msdn.microsoft.com/library/azure/dn535600.aspx).

### <a name="disable-nagle"></a>Disabilitare Nagle

L'algoritmo Nagle viene spesso implementato nelle reti TCP/IP come strumento per migliorare le prestazioni di rete. Tuttavia, non è la soluzione ottimale in tutti gli scenari (ad esempio, gli ambienti ad alta interazione). L'algoritmo Nagle ha un impatto negativo sulle prestazioni delle richieste al servizio tabelle di Azure e, se possibile, dovrebbe essere disabilitato.

## <a name="schema"></a>SCHEMA

La modalità con cui vengono rappresentati i dati e vengono eseguite query su di essi è il fattore singolo più importante che influisce sulle prestazioni del servizio tabelle. Ogni applicazione è diversa, ma in questa sezione vengono descritte alcune procedure comprovate generali relative a:

- Progettazione della tabella
- Query efficaci
- Aggiornamenti dei dati efficaci

### <a name="tables-and-partitions"></a>Tabelle e partizioni

Le tabella sono divise in partizioni. Ogni entità archiviata in una partizione condivide la stessa chiave di partizione e dispone di una chiave di riga univoca che la identifica all'interno della partizione. Le partizioni offrono dei vantaggi ma introducono anche dei limiti di scalabilità.

- Vantaggi: è possibile aggiornare le entità nella stessa partizione in un'unica transazione batch atomica che contiene fino a 100 operazioni di archiviazione distinte (limite di 4 MB di dimensioni totali). Presupponendo lo stesso numero di entità da recuperare, è anche possibile eseguire query sui dati all'interno di una singola partizione in modo più efficiente rispetto ai dati distribuiti in più partizioni (tuttavia, leggere più avanti per ulteriori consigli sulle query sui dati nelle tabelle).
- Limite di scalabilità: l'accesso alle entità archiviate in una singola partizione non può essere sottoposto a bilanciamento del carico perché le partizioni supportano le transazioni batch atomiche. Per questo motivo, l'obiettivo di scalabilità per una singola partizione della tabella è inferiore rispetto a quello dell'intero servizio tabelle.

Tenendo conto delle caratteristiche descritte di tabelle e partizioni, adottare i seguenti principi di progettazione:

- Posizionare nella stessa partizione i dati aggiornati o su cui vengono eseguite query più di frequente da parte dell'applicazione client nella stessa unità logica di lavoro. Posizionare, ad esempio, i dati nella stessa partizione se l'applicazione sta aggregando le scritture o se si eseguono operazioni batch atomiche. Inoltre, le query sui dati in una singola partizione possono essere eseguite in modo più efficace con un'unica query rispetto a quelle sui dati in più partizioni.
- Posizionare in partizioni separate i dati che l'applicazione client non inserisce/aggiorna o di cui non esegue query nella stessa unità logica di lavoro (query singola o aggiornamento batch). Tenere presente che non esiste un limite al numero di chiavi di partizione in una singola tabella, quindi l'esistenza di milioni di chiavi di partizione non rappresenta un problema e non influisce sulle prestazioni. Se, ad esempio, l'applicazione è un sito Web molto frequentato con accesso utente, l'uso dell'ID utente come chiave di partizione potrebbe rappresentare una buona scelta.

#### <a name="hot-partitions"></a>Partizioni critiche

Una partizione critica riceve una percentuale sproporzionata del traffico di un account e non è possibile sottoporla a bilanciamento del carico perché è una partizione singola. In generale, le partizioni critiche vengono create in uno di questi due modi:

#### <a name="append-only-and-prepend-only-patterns"></a>Modelli Solo accodamenti e Solo anteposizioni

In un modello "Solo accodamenti" tutto (o quasi tutto) il traffico verso una determinata chiave di partizione aumenta o diminuisce in base all'ora corrente. Un esempio è quello dell'applicazione che usa la data corrente come chiave di partizione per i dati di log. Ciò comporta che tutti gli inserimenti vanno nell'ultima partizione della tabella e il sistema non riesce a bilanciare il carico correttamente. Se il volume di traffico verso la partizione supera l'obiettivo di scalabilità a livello della partizione viene generata una limitazione. È opportuno assicurarsi che il traffico sia inviato a più partizioni per abilitare il bilanciamento del carico delle richieste nella tabella.

#### <a name="high-traffic-data"></a>Dati con traffico elevato

Se lo schema di partizionamento produce una singola partizione con dati molto più usati rispetto alle altre partizioni, può essere generata una limitazione man mano che la partizione raggiunge l'obiettivo di scalabilità per una singola partizione. È opportuno verificare che lo schema di partizione non generi partizioni singole che raggiungono gli obiettivi di scalabilità.

### <a name="querying"></a>Query

Questa sezione descrive le procedure comprovate per le eseguire query nel servizio tabelle.

#### <a name="query-scope"></a>Ambito delle query

Esistono diversi metodi per specificare l'intervallo di entità su cui eseguire query. L'elenco seguente descrive ciascuna opzione per ambito di query.

- **Query di tipo punto:** una query di tipo punto recupera una sola entità specificando sia la chiave di partizione che la chiave di riga dell'entità da recuperare. Queste query sono efficienti ed è consigliabile usarle quando possibile.
- **Query sulle partizioni:** Una query sulle partizioni recupera un set di dati che condivide una chiave di partizione comune. In genere la query specifica un intervallo di valori della chiave di riga o un intervallo di valori relativi a una proprietà dell'entità, oltre alla chiave di partizione. Queste query sono meno efficaci delle query di tipo punto e devono essere usate in casi limitati.
- **Query sulle tabelle:** Una query sulle tabelle recupera un set di entità che non condivide una chiave di partizione comune. Queste query non sono efficaci e, se possibile, vanno evitate.

Come regola generale, si consiglia di evitare le analisi (query più grandi di una singola entità), ma, se sono necessarie, provare a organizzare i dati in modo che le analisi recuperino i dati necessari senza analizzare o restituire grandi quantità di entità non necessarie.

#### <a name="query-density"></a>Densità delle query

Un altro fattore importante in termini di efficacia delle query è il numero di entità restituite rispetto al numero di entità analizzate per trovare il set restituito. Se l'applicazione esegue una query sulle tabelle con un filtro per un valore della proprietà condiviso solo dall'1% dei dati, la query analizza 100 entità per ciascuna entità restituita. Gli obiettivi di scalabilità della tabella descritti in precedenza fanno tutti riferimento al numero di entità analizzate e non al numero di entità restituite: è probabile che una densità delle query bassa produca una limitazione dell'applicazione nel servizio tabelle perché devono essere analizzate troppe entità per recuperare quella cercata. Per altre informazioni su come evitare la limitazione, vedere la sezione intitolata [Denormalizzazione](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Limitazione della quantità di dati restituiti

Quando si prevede che una query restituisca entità non necessarie nell'applicazione client, valutare l'uso di un filtro per ridurre le dimensioni del set restituito. Le entità non restituite al client vengono comunque considerate per il calcolo dei limiti di scalabilità, ma le prestazioni dell'applicazione migliorano grazie alle minori dimensioni del payload di rete e al numero ridotto di entità che l'applicazione client deve elaborare. Tenere presente che gli obiettivi di scalabilità fanno riferimento al numero di entità analizzate quindi una query che esclude molte entità può comunque produrre una limitazione, anche se vengono restituite poche entità. Per altre informazioni su come rendere efficienti le query, vedere la sezione intitolata [Densità delle query](#query-density).

Se l'applicazione client necessita solo di un set di proprietà limitato dall'entità nella tabella, è possibile usare la proiezione per limitare le dimensioni del set di dati restituito. Come nel caso dei filtri, la proiezione consente di ridurre il carico di rete e l'elaborazione del client.

#### <a name="denormalization"></a>Denormalizzazione

A differenza dell'uso dei database relazionali, le procedure comprovate per l'esecuzione di query efficaci sui dati della tabella portano alla denormalizzazione dei dati. In altre parole, invece di analizzare numeri elevati di entità per individuare i dati richiesti dall'applicazione, vengono duplicati gli stessi dati in più entità (una per ogni chiave usata per trovare i dati) per ridurre il numero di entità che una query deve analizzare per trovare i dati richiesti dal client. In un sito Web di e-commerce è possibile, ad esempio, cercare un ordine in base all'ID cliente (ricerca degli ordini dello specifico cliente) e alla data (ricerca degli ordini con una specifica data). Nell'archiviazione tabelle si consiglia di archiviare l'entità (o un riferimento all'entità) due volte: una con nome tabella, PK e RK per semplificare la ricerca in base all'ID cliente e una per semplificare la ricerca in base alla data.  

### <a name="insert-update-and-delete"></a>Inserire, aggiornare ed eliminare

Questa sezione descrive le procedure comprovate per modificare le entità archiviate nel servizio tabelle.  

#### <a name="batching"></a>Creazione di batch

Le transazioni batch sono note come transazioni dei gruppi di entità in Archiviazione di Azure. Tutte le operazioni all'interno di una transazione dei gruppi di entità devono essere su una singola partizione in una tabella singola. Dove possibile, usare le transazioni dei gruppi di entità per eseguire inserimenti, aggiornamenti ed eliminazioni in batch. L'uso delle transazioni dei gruppi di entità riduce il numero di round trip dall'applicazione client al server, si limita il numero di transazioni fatturabili (una transazione dei gruppi di entità conta come una singola transazione ai fini della fatturazione e può contenere fino a 100 operazioni di archiviazione) e si abilitano gli aggiornamenti atomici (l'esito positivo o negativo di un processo si applica a tutte le operazioni all'interno della transazione dei gruppi di entità). Gli ambienti con latenze elevate come i dispositivi mobili possono trarre grandi vantaggi dall'uso delle transazioni dei gruppi di entità.  

#### <a name="upsert"></a>Upsert

Usare le operazioni della tabella **Upsert** quando possibile. Esistono due tipi di **Upsert**, entrambi più efficaci di una tradizionale operazione di **inserimento** e **aggiornamento**:  

- **InsertOrMerge**: Usare questa operazione quando si vuole caricare un subset di proprietà dell'entità, ma non si è certi del fatto che l'entità esista già. Se l'entità esiste, questa chiamata aggiorna le proprietà incluse nell'operazione **Upsert** e lascia inalterate tutte le proprietà esistenti; se l'entità non esiste, ne inserisce una nuova. La procedura è analoga all'uso della proiezione in una query perché è necessario caricare solo le proprietà modificate.
- **InsertOrReplace**: Usare questa operazione quando si vuole caricare un'entità completamente nuova, ma non si è certi del fatto che l'entità esista già. Usare questa operazione se si è certi che l'entità appena caricata è corretta perché questa sovrascrive completamente l'entità esistente. Si vuole ad esempio aggiornare l'entità in cui è archiviata la posizione corrente di un utente indipendentemente dal fatto che l'applicazione abbia o meno archiviato in precedenza dati sulla posizione dell'utente. La nuova entità di posizione è completa e non occorrono altre informazioni da entità precedenti.

#### <a name="storing-data-series-in-a-single-entity"></a>Archiviazione di serie di dati in una singola entità

A volte un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente: ad esempio, un'applicazione può tenere traccia dell'utilizzo della CPU nel tempo per tracciare un grafico in sequenza dei dati relativo alle ultime 24 ore. Un approccio prevede un'entità di tabella all'ora, in cui ogni entità rappresenta un'ora specifica e archivia l'utilizzo della CPU per quell'ora. Per tracciare questi dati, l'applicazione deve recuperare le entità che comprendono i dati delle ultime 24 ore.  

In alternativa, l'applicazione può archiviare l'utilizzo della CPU per ciascuna ora sotto forma di proprietà separata di una singola entità: per aggiornare le singole ore, l'applicazione può usare una singola chiamata **InsertOrMerge Upsert** per aggiornare il valore per l'ora più recente. Per tracciare i dati, l'applicazione deve recuperare solo una singola entità invece di 24, aumentando l'efficacia della query. Per altre informazioni sull'efficienza delle query, vedere la sezione intitolata [Ambito delle query](#query-scope).

#### <a name="storing-structured-data-in-blobs"></a>Archiviazione di dati strutturati in BLOB

Se si eseguono inserimenti batch e si recuperano contemporaneamente intervalli di entità, è consigliabile usare i BLOB anziché le tabelle. Per spiegarlo efficacemente, si prenda l'esempio di un file di log. È possibile creare in batch e inserire diversi minuti di log, e quindi recuperare diversi minuti di log alla volta. Per motivi di prestazioni, si consiglia in questo caso di usare i BLOB invece delle tabelle perché consentono di ridurre significativamente il numero di oggetti scritti o letti e potenzialmente anche il numero di richieste che occorre effettuare.  

## <a name="next-steps"></a>Passaggi successivi

- [Obiettivi di scalabilità e prestazioni di Archiviazione di Azure per gli account di archiviazione](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Stato e codici errore](/rest/api/storageservices/Status-and-Error-Codes2)
