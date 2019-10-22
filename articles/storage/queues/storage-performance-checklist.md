---
title: Elenco di controllo di prestazioni e scalabilità di archiviazione code - Archiviazione di Azure
description: Un elenco di controllo delle procedure consolidate per l'uso con l'archiviazione code nello sviluppo di applicazioni ad elevate prestazioni.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 8ab4cb6b06f0f023a8f6368dac633a97afe29fd4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390014"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Elenco di controllo di prestazioni e scalabilità di archiviazione code

Un elenco di controllo delle procedure consolidate per l'uso con archiviazione code nello sviluppo di applicazioni ad elevate prestazioni. Questo elenco di controllo identifica le procedure chiave che gli sviluppatori possono seguire per ottimizzare le prestazioni. Tenere presenti queste procedure durante la progettazione dell'applicazione e dell'intero processo.

Archiviazione di Azure ha degli obiettivi di scalabilità per la capacità, la frequenza di transazioni e la larghezza di banda. Per altre informazioni sugli obiettivi di scalabilità per Archiviazione di Azure, vedere [Obiettivi di scalabilità e prestazioni per gli account di archiviazione](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

## <a name="checklist"></a>Elenco di controllo

Questo articolo organizza procedure consolidate per le prestazioni in un elenco di controllo che è possibile seguire durante lo sviluppo dell'applicazione di archiviazione code.

| Operazione completata | Categoria | Considerazioni sulla progettazione |
| --- | --- | --- |
| &nbsp; |Obiettivi di scalabilità |[È possibile progettare l'applicazione in modo da non eccedere il numero massimo di account di archiviazione?](#maximum-number-of-storage-accounts) |
| &nbsp; |Obiettivi di scalabilità |[Si sta evitando il raggiungimento dei limiti di capacità e transazioni?](#capacity-and-transaction-targets) |
| &nbsp; |Rete |[I dispositivi sul lato client hanno una larghezza di banda sufficientemente alta e una latenza sufficientemente bassa per raggiungere le prestazioni richieste?](#throughput) |
| &nbsp; |Rete |[I dispositivi sul lato client hanno un collegamento di qualità elevata?](#link-quality) |
| &nbsp; |Rete |[L'applicazione client si trova nella stessa area dell'account di archiviazione?](#location) |
| &nbsp; |Accesso client diretto |[Si usano le firme di accesso condiviso e la condivisione di risorse tra le origini per abilitare l'accesso diretto ad Archiviazione di Azure?](#sas-and-cors) |
| &nbsp; |Configurazione .NET |[Si usa .NET Core 2.1 o versione successiva per ottenere prestazioni ottimali?](#use-net-core) |
| &nbsp; |Configurazione .NET |[Il client è stato configurato per usare un numero sufficiente di connessioni simultanee?](#increase-default-connection-limit) |
| &nbsp; |Configurazione .NET |[Per le applicazioni .NET, è stato configurato l'uso di un numero sufficiente di thread?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelismo |[È stata verificata la corretta associazione del parallelismo in modo da non sovraccaricare le capacità del client o raggiungere gli obiettivi di scalabilità?](#unbounded-parallelism) |
| &nbsp; |Strumenti |[Si sta usando l'ultima versione delle librerie e degli strumenti client forniti da Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativi |[Si sta usando un criterio per l'esecuzione di nuovi tentativi per il backoff esponenziale per gli errori di limitazione e i timeout?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativi |[L'applicazione sta evitando nuovi tentativi in caso di errori irreversibili?](#non-retryable-errors) |
| &nbsp; |Configurazione |[L'algoritmo Nagle è stato disattivato per migliorare le prestazioni per le piccole richieste?](#disable-nagle) |
| &nbsp; |Dimensioni dei messaggi |[I messaggi vengono compattati per migliorare le prestazioni della coda?](#message-size) |
| &nbsp; |Recupero bulk |[Si stanno recuperando più messaggi con un'unica operazione GET?](#batch-retrieval) |
| &nbsp; |Frequenza di polling |[Il polling viene eseguito abbastanza di frequente per ridurre la latenza percepita dell'applicazione?](#queue-polling-interval) |
| &nbsp; |Aggiornamento del messaggio |[Si sta usando l'operazione Aggiornamento del messaggio per archiviare lo stato di elaborazione dei messaggi senza dover rielaborare l'intero messaggio in caso di errori?](#use-update-message) |
| &nbsp; |Architettura |[Si stanno usando delle code per rendere più scalabile l'intera applicazione tenendo i carichi di lavoro con esecuzione prolungata fuori dal percorso critico e scalandoli indipendentemente?](#application-architecture) |

## <a name="scalability-targets"></a>Obiettivi di scalabilità

Se l'applicazione raggiunge o supera uno o più obiettivi di scalabilità, può verificarsi un aumento delle latenze o delle limitazioni della transazione. Quando Archiviazione di Azure limita l'applicazione, il servizio inizia a restituire i codici di errore "503 Server occupato" o "500 Timeout operazione". Evitare questi errori rispettando i limiti degli obiettivi di scalabilità è una parte importante del miglioramento delle prestazioni dell'applicazione.

Per altre informazioni sugli obiettivi di scalabilità per il Servizio di accodamento, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#azure-queue-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>Numero massimo di account di archiviazione

Se si sta per raggiungere il numero massimo di account di archiviazione consentiti per una determinata combinazione di sottoscrizione/area, si usano più account di archiviazione da partizionare per aumentare il traffico in ingresso, in uscita, le operazioni di I/O al secondo o la capacità? In questo scenario, se possibile Microsoft consiglia di sfruttare l'innalzamento dei limiti degli account di archiviazione per ridurre il numero di account di archiviazione necessari per il carico di lavoro. Contattare il [supporto di Azure](https://azure.microsoft.com/support/options/) per richiedere l'espansione dei limiti dell'account di archiviazione. Per altre informazioni, vedere [Annuncio di account di archiviazione di dimensioni maggiori a scalabilità più elevata](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Obiettivi di capacità e transazioni

Se l'applicazione sta raggiungendo gli obiettivi di scalabilità per un singolo account di archiviazione, valutare uno dei seguenti approcci:  

- Se gli obiettivi di scalabilità per le code non sono sufficienti per l'applicazione, è consigliabile usare più code e distribuire i messaggi fra di esse.
- Esaminare di nuovo il carico di lavoro che causa il raggiungimento o il superamento dell'obiettivo di scalabilità da parte dell'applicazione. È possibile progettarlo in modo diverso in modo che usi una quantità minore di larghezza di banda o capacità o un minor numero di transazioni?
- Se l'applicazione deve superare uno degli obiettivi di scalabilità, creare più account di archiviazione e partizionare i dati dell'applicazione tra questi account di archiviazione. Se si usa questo modello, assicurarsi di progettare l'applicazione in modo da aggiungere altri account di archiviazione in futuro per il bilanciamento del carico. Gli stessi account di archiviazione non hanno costi aggiuntivi rispetto a quelli per l'uso, ossia associati ai dati archiviati, alle transazioni effettuate o ai dati trasferiti.
- Se l'applicazione sta raggiungendo gli obiettivi di larghezza di banda, valutare la compressione dei dati sul lato client per ridurre la larghezza di banda necessaria a inviare i dati ad Archiviazione di Azure.
    La compressione dei dati, pur consentendo di risparmiare larghezza di banda e migliorare le prestazioni di rete, può avere anche degli effettivi negativi sulle prestazioni. Valutare gli effetti sulle prestazioni dei requisiti di elaborazione aggiuntivi per la compressione e la decompressione dei dati sul lato client. Tenere presente che l'archiviazione dei dati compressi può rendere più difficile la risoluzione dei problemi perché ostacola la visualizzazione dei dati usando gli strumenti standard.
- Se l'applicazione sta raggiungendo gli obiettivi di scalabilità, assicurarsi di usare un backoff esponenziale per i nuovi tentativi. È consigliabile provare a evitare di raggiungere gli obiettivi di scalabilità implementando i consigli descritti in questo articolo. Tuttavia, l'uso di un backoff esponenziale per i tentativi impedisce all'applicazione di ritentare rapidamente, il che potrebbe peggiorare la limitazione delle richieste. Per altre informazioni, vedere la sezione intitolata [Errori di timeout e server occupato](#timeout-and-server-busy-errors).

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

## <a name="disable-nagle"></a>Disabilitare Nagle

L'algoritmo Nagle viene spesso implementato nelle reti TCP/IP come strumento per migliorare le prestazioni di rete. Tuttavia, non è la soluzione ottimale in tutti gli scenari (ad esempio, gli ambienti ad alta interazione). L'algoritmo Nagle ha un impatto negativo sulle prestazioni delle richieste al servizio tabelle di Azure e, se possibile, dovrebbe essere disabilitato.

## <a name="message-size"></a>Dimensioni dei messaggi

Le prestazioni e la scalabilità della coda diminuiscono con l'aumentare delle dimensioni del messaggio. In un messaggio inserire solo le informazioni richieste dal ricevitore.  

## <a name="batch-retrieval"></a>Recupero in batch

È possibile recuperare fino a 32 messaggi da una coda in una singola operazione. Il recupero in batch può ridurre il numero di round trip dall'applicazione client, particolarmente utile per gli ambienti con latenza elevata, come i dispositivi mobili.  

## <a name="queue-polling-interval"></a>Intervallo di polling della coda

La maggior parte delle applicazioni esegue il polling dei messaggi da una coda, che può costituire una delle principali origini di transazioni per l'applicazione. L'intervallo di polling deve essere scelto con attenzione: se si esegue il polling con eccessiva frequenza, l'applicazione potrebbe avvicinarsi all'obiettivo di scalabilità per la coda. Tuttavia, al prezzo di 0,01 USD per 200.000 transazioni (al momento della stesura di questo documento), il costo del polling eseguito da un singolo processore una volta al secondo per un intero mese non supererebbe 15 centesimi. Il costo non è dunque un fattore determinante nella scelta dell'intervallo di polling.  

Per informazioni aggiornate sui costi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Usare Aggiornamento del messaggio

È possibile usare l'operazione **Aggiornamento del messaggio** per aumentare il timeout di invisibilità o aggiornare le informazioni di stato di un messaggio. L'uso di **Aggiornamento del messaggio** può essere un approccio più efficace rispetto al passaggio di un processo da una coda alla successiva mediante un flusso di lavoro man mano che i singoli passaggi del processo vengono completati. L'applicazione può salvare lo stato del processo nel messaggio e continuare il lavoro invece di riaccodare il messaggio per il passaggio successivo del processo ogni volta che viene completato un passaggio. Occorre ricordare che ogni operazione **Aggiornamento del messaggio** viene presa in considerazione per il calcolo dell'obiettivo di scalabilità.

## <a name="application-architecture"></a>Architettura dell'applicazione

Usare le code per rendere scalabile l'architettura dell'applicazione. Di seguito vengono elencati alcuni modi in cui le code possono essere usate per rendere più scalabile l'applicazione:  

- Le code possono essere usate per creare backlog di lavoro per l'elaborazione e il contenimento dei carichi di lavoro nell'applicazione. Ad esempio, è possibile accodare le richieste degli utenti per eseguire un lavoro che prevede un utilizzo intensivo del processore, ad esempio il ridimensionamento delle immagini caricate.
- È possibile usare le code per separare i componenti dell'applicazione per eseguirne la scalabilità in modo indipendente. Un front-end Web, ad esempio, può inserire i risultati di un sondaggio degli utenti in una coda per l'analisi e l'archiviazione successive. È possibile aggiungere più istanze del ruolo di lavoro per elaborare i dati della coda come richiesto.  

## <a name="next-steps"></a>Passaggi successivi

- [Obiettivi di scalabilità e prestazioni di Archiviazione di Azure per gli account di archiviazione](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Stato e codici errore](/rest/api/storageservices/Status-and-Error-Codes2)
