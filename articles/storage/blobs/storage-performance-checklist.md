---
title: Elenco di controllo delle prestazioni e della scalabilità per l'archiviazione BLOB-archiviazione di Azure
description: Elenco di controllo delle procedure comprovate per l'utilizzo con archiviazione BLOB nello sviluppo di applicazioni a prestazioni elevate.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80473943"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Elenco di controllo di prestazioni e scalabilità dell'archiviazione BLOB

Microsoft ha sviluppato numerose procedure comprovate per lo sviluppo di applicazioni a prestazioni elevate con archiviazione BLOB. Questo elenco di controllo identifica le procedure chiave che gli sviluppatori possono seguire per ottimizzare le prestazioni. Tenere presenti queste procedure durante la progettazione dell'applicazione e dell'intero processo.

Archiviazione di Azure ha degli obiettivi di scalabilità per la capacità, la frequenza di transazioni e la larghezza di banda. Per altre informazioni sugli obiettivi di scalabilità di archiviazione di Azure, vedere [obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](scalability-targets.md).

## <a name="checklist"></a>Elenco di controllo

Questo articolo organizza procedure comprovate per le prestazioni in un elenco di controllo che è possibile seguire durante lo sviluppo dell'applicazione di archiviazione BLOB.

| Operazione completata | Category | Considerazioni sulla progettazione |
| --- | --- | --- |
| &nbsp; |Obiettivi di scalabilità |[È possibile progettare l'applicazione in modo da non eccedere il numero massimo di account di archiviazione?](#maximum-number-of-storage-accounts) |
| &nbsp; |Obiettivi di scalabilità |[Si sta evitando il raggiungimento dei limiti di capacità e transazioni?](#capacity-and-transaction-targets) |
| &nbsp; |Obiettivi di scalabilità |[Un numero elevato di client che accedono contemporaneamente a un singolo BLOB?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Obiettivi di scalabilità |[L'applicazione rimane all'interno degli obiettivi di scalabilità per un singolo BLOB?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partizionamento |[La convenzione di denominazione è stata progettata per consentire un miglior bilanciamento del carico?](#partitioning) |
| &nbsp; |Rete |[I dispositivi sul lato client hanno una larghezza di banda sufficientemente alta e una latenza sufficientemente bassa per raggiungere le prestazioni richieste?](#throughput) |
| &nbsp; |Rete |[I dispositivi sul lato client hanno un collegamento di qualità elevata?](#link-quality) |
| &nbsp; |Rete |[L'applicazione client si trova nella stessa area dell'account di archiviazione?](#location) |
| &nbsp; |Accesso diretto ai client |[Si usano le firme di accesso condiviso e la condivisione di risorse tra le origini per abilitare l'accesso diretto ad Archiviazione di Azure?](#sas-and-cors) |
| &nbsp; |Memorizzazione nella cache |[L'applicazione memorizza nella cache i dati a cui si accede di frequente e modificati raramente?](#reading-data) |
| &nbsp; |Memorizzazione nella cache |[L'applicazione invia in batch gli aggiornamenti memorizzando nella cache il client e quindi caricarli in set più grandi?](#uploading-data-in-batches) |
| &nbsp; |Configurazione .NET |[Si usa .NET Core 2.1 o versione successiva per ottenere prestazioni ottimali?](#use-net-core) |
| &nbsp; |Configurazione .NET |[Il client è stato configurato per usare un numero sufficiente di connessioni simultanee?](#increase-default-connection-limit) |
| &nbsp; |Configurazione .NET |[Per le applicazioni .NET, è stato configurato l'uso di un numero sufficiente di thread?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelismo |[È stata verificata la corretta associazione del parallelismo in modo da non sovraccaricare le capacità del client o raggiungere gli obiettivi di scalabilità?](#unbounded-parallelism) |
| &nbsp; |Strumenti |[Si sta usando l'ultima versione delle librerie e degli strumenti client forniti da Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativi |[Si sta usando un criterio per l'esecuzione di nuovi tentativi per il backoff esponenziale per gli errori di limitazione e i timeout?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativi |[L'applicazione sta evitando nuovi tentativi in caso di errori irreversibili?](#non-retryable-errors) |
| &nbsp; |Copia di BLOB |[I BLOB vengono copiati nel modo più efficiente?](#blob-copy-apis) |
| &nbsp; |Copia di BLOB |[Si sta usando la versione più recente di AzCopy per le operazioni di copia bulk?](#use-azcopy) |
| &nbsp; |Copia di BLOB |[Si sta usando la famiglia di Azure Data Box per l'importazione di volumi elevati di dati?](#use-azure-data-box) |
| &nbsp; |Distribuzione di contenuti |[Viene usata una rete CDN per la distribuzione del contenuto?](#content-distribution) |
| &nbsp; |Usare i metadati |[Si stanno archiviando i metadati usati di frequente relativi ai BLOB nei rispettivi metadati?](#use-metadata) |
| &nbsp; |Caricamento rapido |[Quando si tenta di caricare rapidamente un BLOB, i blocchi vengono caricati in parallelo?](#upload-one-large-blob-quickly) |
| &nbsp; |Caricamento rapido |[Quando si tenta di caricare rapidamente più BLOB, i BLOB vengono caricati in parallelo?](#upload-many-blobs-quickly) |
| &nbsp; |Tipo di BLOB |[Si stanno usando BLOB di pagine o in blocchi quando appropriato?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Obiettivi di scalabilità

Se l'applicazione raggiunge o supera uno o più obiettivi di scalabilità, può verificarsi un aumento delle latenze o delle limitazioni della transazione. Quando Archiviazione di Azure limita l'applicazione, il servizio inizia a restituire i codici di errore "503 Server occupato" o "500 Timeout operazione". Evitare questi errori rispettando i limiti degli obiettivi di scalabilità è una parte importante del miglioramento delle prestazioni dell'applicazione.

Per altre informazioni sugli obiettivi di scalabilità per il Servizio di accodamento, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Numero massimo di account di archiviazione

Se si sta per raggiungere il numero massimo di account di archiviazione consentiti per una determinata combinazione di sottoscrizione/area, valutare lo scenario e determinare se si applica una delle condizioni seguenti:

- Si usano gli account di archiviazione per archiviare dischi non gestiti e aggiungere tali dischi alle macchine virtuali (VM)? Per questo scenario, Microsoft consiglia di usare Managed Disks. I dischi gestiti vengono ridimensionati automaticamente e senza la necessità di creare e gestire singoli account di archiviazione. Per altre informazioni, vedere [Introduzione a Managed Disks di Azure](../../virtual-machines/windows/managed-disks-overview.md)
- Si sta usando un account di archiviazione per cliente per l'isolamento dei dati? Per questo scenario, Microsoft consiglia di usare un contenitore BLOB per ogni cliente, anziché un intero account di archiviazione. Archiviazione di Azure consente ora di assegnare ruoli di controllo degli accessi in base al ruolo (RBAC) per ogni contenitore. Per altre informazioni, vedere [Concedere l'accesso ai dati dei BLOB e delle code di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](../common/storage-auth-aad-rbac-portal.md).
- Si usano più account di archiviazione per la partizione per aumentare il traffico in ingresso, in uscita, le operazioni di I/O al secondo (IOPS) o la capacità? In questo scenario, se possibile Microsoft consiglia di sfruttare l'innalzamento dei limiti degli account di archiviazione per ridurre il numero di account di archiviazione necessari per il carico di lavoro. Contattare il [supporto di Azure](https://azure.microsoft.com/support/options/) per richiedere l'espansione dei limiti dell'account di archiviazione. Per altre informazioni, vedere [Annuncio di account di archiviazione di dimensioni maggiori a scalabilità più elevata](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Obiettivi di capacità e transazioni

Se l'applicazione sta raggiungendo gli obiettivi di scalabilità per un singolo account di archiviazione, valutare uno dei seguenti approcci:  

- Se l'applicazione raggiunge la destinazione della transazione, prendere in considerazione l'uso di account di archiviazione BLOB in blocchi, ottimizzati per tassi di transazione elevati e latenza bassa e coerente. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).
- Esaminare di nuovo il carico di lavoro che causa il raggiungimento o il superamento dell'obiettivo di scalabilità da parte dell'applicazione. È possibile progettarlo in modo diverso in modo che usi una quantità minore di larghezza di banda o capacità o un minor numero di transazioni?
- Se l'applicazione deve superare uno degli obiettivi di scalabilità, creare più account di archiviazione e partizionare i dati dell'applicazione tra questi account di archiviazione. Se si usa questo modello, assicurarsi di progettare l'applicazione in modo da aggiungere altri account di archiviazione in futuro per il bilanciamento del carico. Gli stessi account di archiviazione non hanno costi aggiuntivi rispetto a quelli per l'uso, ossia associati ai dati archiviati, alle transazioni effettuate o ai dati trasferiti.
- Se l'applicazione sta raggiungendo gli obiettivi di larghezza di banda, valutare la compressione dei dati sul lato client per ridurre la larghezza di banda necessaria a inviare i dati ad Archiviazione di Azure.
    La compressione dei dati, pur consentendo di risparmiare larghezza di banda e migliorare le prestazioni di rete, può avere anche degli effettivi negativi sulle prestazioni. Valutare gli effetti sulle prestazioni dei requisiti di elaborazione aggiuntivi per la compressione e la decompressione dei dati sul lato client. Tenere presente che l'archiviazione dei dati compressi può rendere più difficile la risoluzione dei problemi perché ostacola la visualizzazione dei dati usando gli strumenti standard.
- Se l'applicazione sta raggiungendo gli obiettivi di scalabilità, assicurarsi di usare un backoff esponenziale per i nuovi tentativi. È consigliabile provare a evitare di raggiungere gli obiettivi di scalabilità implementando i consigli descritti in questo articolo. Tuttavia, l'uso di un backoff esponenziale per i tentativi impedisce all'applicazione di ritentare rapidamente, il che potrebbe peggiorare la limitazione delle richieste. Per altre informazioni, vedere la sezione intitolata [Errori di timeout e server occupato](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Più client che accedono contemporaneamente a un singolo BLOB

Se si dispone di un numero elevato di client che accedono contemporaneamente a un singolo BLOB, è necessario prendere in considerazione sia gli obiettivi di scalabilità per ogni BLOB sia per gli account di archiviazione. Il numero esatto di client che possono accedere a un singolo BLOB varia a seconda di fattori quali il numero di client che richiedono contemporaneamente il BLOB, le dimensioni del BLOB e le condizioni della rete.

Se il BLOB può essere distribuito tramite una rete CDN, ad esempio immagini o video serviti da un sito Web, è possibile usare una rete CDN. Per ulteriori informazioni, vedere la sezione distribuzione del [contenuto](#content-distribution).

In altri scenari, ad esempio le simulazioni scientifiche in cui i dati sono riservati, sono disponibili due opzioni. Il primo consiste nel scaglionare l'accesso del carico di lavoro in modo che sia possibile accedere al BLOB in un periodo di tempo rispetto al quale si accede simultaneamente. In alternativa, è possibile copiare temporaneamente il BLOB in più account di archiviazione per aumentare il numero totale di IOPS per BLOB e tra gli account di archiviazione. I risultati variano a seconda del comportamento dell'applicazione, pertanto assicurarsi di testare i modelli di concorrenza durante la progettazione.

### <a name="bandwidth-and-operations-per-blob"></a>Larghezza di banda e operazioni per BLOB

Un singolo BLOB supporta fino a 500 richieste al secondo. Se sono presenti più client che devono leggere lo stesso BLOB e si può superare questo limite, provare a usare un account di archiviazione BLOB in blocchi. Un account di archiviazione BLOB in blocchi offre una frequenza di richieste superiore o operazioni di I/O al secondo (IOPS).

È anche possibile usare una rete per la distribuzione di contenuti (CDN), ad esempio la rete CDN di Azure, per distribuire le operazioni sul BLOB. Per altre informazioni sulla rete CDN di Azure, vedere Panoramica della rete [CDN di Azure](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Partizionamento

Comprendere il modo in cui archiviazione di Azure partiziona i dati BLOB è utile per migliorare le prestazioni. Archiviazione di Azure è in grado di gestire i dati in una singola partizione più rapidamente rispetto ai dati che si estendono su più partizioni. Assegnando un nome appropriato ai BLOB, è possibile migliorare l'efficienza delle richieste di lettura.

L'archiviazione BLOB usa uno schema di partizionamento basato su intervalli per la scalabilità e il bilanciamento del carico. Ogni BLOB ha una chiave di partizione costituita dal nome del BLOB completo (account + contenitore + BLOB). La chiave di partizione viene utilizzata per partizionare i dati BLOB in intervalli. Gli intervalli vengono quindi sottoposte a bilanciamento del carico nell'archivio BLOB.

Il partizionamento basato su intervalli significa che le convenzioni di denominazione che usano l'ordinamento lessicale (ad esempio, *Payroll*, le *prestazioni*, i *dipendenti*e così via) o i timestamp (*log20160101*, *log20160102*, *log20160102*e così via) hanno più probabilità che le partizioni si trovino nello stesso server di partizione. , fino a quando l'incremento del carico richiede la suddivisione in intervalli più piccoli. La condivisione dei BLOB nello stesso server di partizione migliora le prestazioni, quindi una parte importante del miglioramento delle prestazioni prevede la denominazione dei BLOB in modo da organizzarli in modo più efficace.

Ad esempio, tutti i BLOB in un contenitore possono essere serviti da un singolo server fino a quando il carico di questi BLOB richiede un ulteriore bilanciamento degli intervalli di partizione. Analogamente, un gruppo di account con caricamento leggero con i rispettivi nomi disposti in ordine lessicale può essere servito da un singolo server fino a quando il carico su uno o tutti questi account non richiede la suddivisione in più server di partizione.

Ogni operazione di bilanciamento del carico può incidere negativamente sulla latenza delle chiamate di archiviazione durante l'operazione. La possibilità di gestire un improvviso picco di traffico in una partizione è limitata dalla scalabilità di un singolo server di partizione fino a quando non viene avviata l'operazione di bilanciamento del carico e il ribilanciamento dell'intervallo di chiavi di partizione.

Alcune procedure consigliate consentono di ridurre la frequenza di queste operazioni.  

- Se possibile, usare dimensioni di BLOB o blocchi maggiori di 4 MiB per gli account di archiviazione standard e maggiori di 256 KiB per gli account di archiviazione Premium. Dimensioni BLOB o blocchi più grandi attivano automaticamente i BLOB in blocchi con velocità effettiva elevata. I BLOB in blocchi con velocità effettiva elevata forniscono un inserimento a prestazioni elevate che non è influenzato dalla denominazione della partizione.
- Esaminare la convenzione di denominazione usata per account, contenitori, BLOB, tabelle e code. Prendere in considerazione il prefisso per i nomi di account, contenitori o BLOB con un hash di tre cifre usando una funzione di hashing più adatta alle proprie esigenze.
- Se si organizzano i dati usando timestamp o identificatori numerici, assicurarsi che non si stia usando un modello di traffico solo di Accodamento (o solo anteporre). Questi modelli non sono adatti per un sistema di partizionamento basato su intervalli. Questi modelli possono comportare tutto il traffico verso una singola partizione e limitare il bilanciamento del carico del sistema in modo efficace.

    Se, ad esempio, si dispone di operazioni quotidiane che utilizzano un BLOB con un timestamp quale *AAAAMMGG*, tutto il traffico per tale operazione giornaliera viene indirizzato a un singolo BLOB, che viene servito da un singolo server di partizione. Valutare se i limiti per BLOB e i limiti per partizione soddisfino le proprie esigenze e provare a suddividere l'operazione in più BLOB, se necessario. Analogamente, se si archiviano i dati delle serie temporali nelle tabelle, tutto il traffico può essere indirizzato all'ultima parte dello spazio dei nomi Key. Se si usano ID numerici, anteporre un hash a tre cifre al prefisso dell'ID. Se si usano i timestamp, anteporre al timestamp il valore dei secondi, ad esempio *ssaaaammgg*. Se l'applicazione esegue regolarmente l'elenco e l'esecuzione di query sulle operazioni, scegliere una funzione di hashing che limiterà il numero di query. In alcuni casi, può essere sufficiente un prefisso casuale.
  
- Per altre informazioni sullo schema di partizionamento usato in archiviazione di Azure, vedere [archiviazione di Azure: un servizio di archiviazione cloud a disponibilità elevata con coerenza](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)assoluta.

## <a name="networking"></a>Rete

I vincoli fisici della rete dell'applicazione possono avere effetti significativi sulle prestazioni. Le sezioni seguenti descrivono alcune limitazioni che gli utenti possono incontrare.  

### <a name="client-network-capability"></a>Capacità della rete client

La larghezza di banda e la qualità del collegamento di rete svolgono ruoli importanti nelle prestazioni dell'applicazione, come descritto nelle sezioni seguenti.

#### <a name="throughput"></a>Velocità effettiva

Per la larghezza di banda il problema dipende spesso dalle capacità del client. Le istanze di Azure più grandi possono avere schede di interfaccia di rete con capacità più elevate ed è quindi opportuno prendere in considerazione l'uso di un'istanza più grande o di più macchine virtuali se è necessario che un singolo computer abbia limiti di rete più elevati. Se si accede ad Archiviazione di Azure da un'applicazione locale, si applica la stessa regola: comprendere le capacità di rete del dispositivo client e la connettività di rete della posizione di archiviazione di Azure, quindi aumentarle in base alle esigenze o progettare l'applicazione in modo che funzioni con i limiti di capacità disponibili.

#### <a name="link-quality"></a>Qualità del collegamento

Come accade in ogni rete, tenere presente che le condizioni di rete che generano errori e perdita di pacchetti riducono la velocità effettiva.  L'uso di WireShark o NetMon può contribuire a diagnosticare il problema.  

### <a name="location"></a>Percorso

In qualsiasi ambiente distribuito, il posizionamento del client accanto al server offre le prestazioni migliori. Per accedere all'archiviazione di Azure con la minor latenza possibile, è opportuno posizionare il client nella stessa area di Azure. Ad esempio, se si ha un'app Web di Azure che usa Archiviazione di Azure, posizionare entrambi in un'unica area, ad esempio Stati Uniti occidentali o Asia sudorientale. Il posizionamento delle risorse nella stessa area riduce latenza e costi, in quanto l'utilizzo della larghezza di banda in un'unica area è gratuito.  

Anche se le applicazioni client hanno accesso ad Archiviazione di Azure ma non sono ospitate in Azure, ad esempio app di dispositivi mobili o servizi aziendali locali, la latenza viene ridotta se si posiziona l'account di archiviazione in un'area vicina a tali client. Se i client sono distribuiti in un'area ampia (ad esempio, alcuni in America del Nord e altri in Europa), è opportuno usare un account di archiviazione per ogni area. Questo approccio è più semplice da implementare se i dati archiviati dall'applicazione sono specifici di singoli utenti e non richiedono la replica tra gli account di archiviazione.

Per una distribuzione estesa del contenuto BLOB, usare una rete per la distribuzione di contenuti, ad esempio la rete CDN di Azure. Per altre informazioni sulla rete CDN di Azure, vedere [rete CDN di Azure](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS e CORS

Si supponga che sia necessario autorizzare il codice, ad esempio JavaScript, che viene eseguito nel Web browser dell'utente o in un'app per telefoni cellulari per accedere ai dati in Archiviazione di Azure. Un approccio consiste nel creare un'applicazione di servizio che funga da proxy. Il dispositivo dell'utente esegue l'autenticazione con il servizio, che a sua volta autorizza l'accesso alle risorse di Archiviazione di Azure. In questo modo si evita di esporre le chiavi dell'account di archiviazione in dispositivi non sicuri. Questo approccio causa tuttavia un sovraccarico significativo dell'applicazione di servizio, perché tutti i dati trasferiti tra il dispositivo dell'utente e Archiviazione di Azure devono passare attraverso l'applicazione di servizio.

È possibile evitare di usare un'applicazione di servizio come proxy per Archiviazione di Azure usando firme di accesso condiviso. Con le firme di accesso condiviso è possibile consentire al dispositivo dell'utente di indirizzare le richieste direttamente ad Archiviazione di Azure usando un token con accesso limitato. Ad esempio, se un utente vuole caricare una foto nell'applicazione, l'applicazione di servizio può generare una firma di accesso condiviso e inviarla al dispositivo dell'utente. Il token di firma di accesso condiviso può concedere l'autorizzazione per scrivere in una risorsa di Archiviazione di Azure per un intervallo di tempo specificato, dopo la scadenza del token di firma di accesso condiviso. Per altre informazioni sulla firma di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).  

Un Web browser non consente in genere codice JavaScript in una pagina ospitata da un sito Web in un dominio per l'esecuzione di determinate operazioni, come operazioni di scrittura, in un altro dominio. Noto come criterio di corrispondenza dell'origine, questo criterio impedisce a uno script dannoso in una pagina di ottenere l'accesso ai dati in un'altra pagina Web. Tuttavia, i criteri di corrispondenza dell'origine possono costituire una limitazione quando si compila una soluzione nel cloud. La condivisione di risorse tra le origini è una funzionalità del browser che consente al dominio di destinazione di comunicare con il browser di cui reputa attendibili le richieste originate nel dominio di origine.

Si supponga, ad esempio, che un'applicazione Web in esecuzione in Azure faccia una richiesta di una risorsa a un account di Archiviazione di Azure. L'applicazione Web è il dominio di origine e l'account di archiviazione è il dominio di destinazione. È possibile configurare la condivisione di risorse tra le origini per qualsiasi servizio di Archiviazione di Azure in modo che comunichi con il Web browser che le richieste provenienti dal dominio di origine siano ritenuti attendibili da Archiviazione di Azure. Per altre informazioni sulla condivisione di risorse tra le origini, vedere [Supporto della condivisione delle risorse tra le origini (CORS) per Archiviazione di Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Entrambe le tecnologie SAS e CORS possono aiutare a evitare carichi non necessari nell'applicazione Web.  

## <a name="caching"></a>Memorizzazione nella cache

La memorizzazione nella cache svolge un ruolo importante per le prestazioni. Nelle sezioni seguenti vengono descritte le procedure consigliate per la memorizzazione nella cache.

### <a name="reading-data"></a>Lettura di dati

In generale, è preferibile leggere i dati una sola volta. Si consideri l'esempio di un'applicazione Web che ha recuperato un BLOB MiB 50 dall'archiviazione di Azure per fungere da contenuto per un utente. Idealmente, l'applicazione memorizza nella cache il BLOB localmente su disco e quindi recupera la versione memorizzata nella cache per le successive richieste utente.

Un modo per evitare di recuperare un BLOB se non è stato modificato perché è stato memorizzato nella cache è quello di qualificare l'operazione GET con un'intestazione condizionale per l'ora di modifica. Se l'ora dell'Ultima modifica è successiva al momento in cui il BLOB è stato memorizzato nella cache, il BLOB viene recuperato e memorizzato nuovamente nella cache. In caso contrario, il BLOB memorizzato nella cache viene recuperato per ottenere prestazioni ottimali.

Si può anche decidere di progettare l'applicazione in modo da presupporre che il BLOB rimanga invariato per un breve periodo di tempo dopo il recupero. In questo caso, non è necessario che l'applicazione verifichi se il BLOB è stato modificato durante tale intervallo.

I dati di configurazione, i dati di ricerca e altri dati usati di frequente dall'applicazione sono ottimi candidati per la memorizzazione nella cache.  

Per ulteriori informazioni sull'utilizzo delle intestazioni condizionali, vedere [specifica di intestazioni condizionali per le operazioni del servizio BLOB](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Caricamento dei dati in batch

In alcuni scenari è possibile aggregare i dati in locale e quindi caricarli periodicamente in un batch anziché caricare immediatamente i dati. Si supponga, ad esempio, che un'applicazione Web contenga un file di log di attività. L'applicazione può caricare i dettagli di ogni attività in una tabella (che richiede molte operazioni di archiviazione) o salvare i dettagli dell'attività in un file di log locale e quindi caricare periodicamente tutti i dettagli dell'attività come file delimitato in un BLOB. Se ogni voce di log ha una dimensione di 1 KB, è possibile caricare migliaia di voci in un'unica transazione. Una singola transazione supporta il caricamento di un BLOB di dimensioni fino a 64 MiB. Lo sviluppatore di applicazioni deve progettare per la possibilità di errori di caricamento o del dispositivo client. Se i dati dell'attività devono essere scaricati per un intervallo di tempo anziché per una singola attività, è consigliabile usare l'archiviazione BLOB sull'archiviazione tabelle.

## <a name="net-configuration"></a>Configurazione .NET

Se si usa .NET Framework, in questa sezione vengono elencate diverse impostazioni di configurazione rapide che è possibile usare per migliorare significativamente le prestazioni.  Se si usano altri linguaggi, controllare se esistono procedure simili per il linguaggio prescelto.  

### <a name="use-net-core"></a>Usare .NET Core

Sviluppare applicazioni di Archiviazione di Azure con .NET Core 2.1 o versione successiva per sfruttare i miglioramenti delle prestazioni. Quando possibile, è consigliabile usare .NET Core 3.x.

Per altre informazioni sui miglioramenti delle prestazioni in .NET Core, vedere i post di blog seguenti:

- [Miglioramenti delle prestazioni in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Miglioramenti delle prestazioni in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumento del limite di connessione predefinito

In .NET, il codice seguente aumenta il limite di connessione predefinito, che in genere è due in un ambiente client o dieci in un ambiente server, a 100. Solitamente il valore deve essere impostato basandosi approssimativamente sul numero di thread usato dall'applicazione. Impostare il limite di connessione prima di aprire le connessioni.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Per altri linguaggi di programmazione, vedere la documentazione per determinare come impostare il limite di connessione.  

Per ulteriori informazioni, vedere il post di Blog relativo ai [servizi Web: connessioni simultanee](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

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

## <a name="copying-and-moving-blobs"></a>Copia e trasferimento di BLOB

Archiviazione di Azure offre una serie di soluzioni per la copia e lo trasferimento di BLOB in un account di archiviazione, tra gli account di archiviazione e tra i sistemi locali e il cloud. In questa sezione vengono descritte alcune di queste opzioni in termini di effetti sulle prestazioni. Per informazioni su come trasferire in modo efficiente i dati da e verso l'archiviazione BLOB, vedere [scegliere una soluzione di Azure per il trasferimento dei dati](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>API per la copia di BLOB

Per copiare i BLOB tra gli account di archiviazione, usare l'operazione [put block from URL](/rest/api/storageservices/put-block-from-url) . Questa operazione copia i dati in modo sincrono da qualsiasi origine URL in un BLOB in blocchi. L'uso `Put Block from URL` dell'operazione può ridurre significativamente la larghezza di banda necessaria quando si esegue la migrazione dei dati tra gli account di archiviazione. Poiché l'operazione di copia viene eseguita sul lato del servizio, non è necessario scaricare e caricare nuovamente i dati.

Per copiare i dati all'interno dello stesso account di archiviazione, usare l'operazione [Copy Blob](/rest/api/storageservices/Copy-Blob) . La copia dei dati all'interno dello stesso account di archiviazione viene in genere completata rapidamente.  

### <a name="use-azcopy"></a>Usare AzCopy

L'utilità da riga di comando AzCopy è un'opzione semplice ed efficiente per il trasferimento bulk di BLOB in, da e tra account di archiviazione. AzCopy è ottimizzato per questo scenario e può ottenere velocità di trasferimento elevate. AzCopy versione 10 usa l' `Put Block From URL` operazione per copiare i dati BLOB tra gli account di archiviazione. Per altre informazioni, vedere [copiare o spostare dati in archiviazione di Azure tramite AzCopy V10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>USA Azure Data Box

Per l'importazione di grandi volumi di dati nell'archiviazione BLOB, è consigliabile usare la famiglia di Azure Data Box per i trasferimenti offline. I dispositivi Data Box forniti da Microsoft sono la scelta ideale per lo spostamento di grandi quantità di dati in Azure quando si è limitati a tempo, disponibilità di rete o costi. Per ulteriori informazioni, vedere la [documentazione di Azure databox](/azure/databox/).

## <a name="content-distribution"></a>Distribuzione di contenuti

A volte un'applicazione deve servire lo stesso contenuto per molti utenti (ad esempio, un video dimostrativo di un prodotto usato nella home page di un sito Web), che si trova nella stessa area o in più aree. In questo scenario usare una rete per la distribuzione di contenuti (CDN), ad esempio la rete CDN di Azure, per distribuire il contenuto del BLOB geograficamente. A differenza di un account di archiviazione di Azure, presente in un'unica area e che non può fornire contenuti con una bassa latenza in altre aree, la rete CDN di Azure usa diversi server in più data center in tutto il mondo. Inoltre, una rete CDN in genere supporta limiti in uscita molto più elevati rispetto a un singolo account di archiviazione.  

Per altre informazioni sulla rete CDN di Azure, vedere [rete CDN di Azure](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Usare i metadati

Il servizio BLOB supporta le richieste HEAD, che possono includere proprietà o metadati del BLOB. Se, ad esempio, per l'applicazione sono necessari i dati EXIF (scambioable Image Format) di una foto, è possibile recuperare la foto ed estrarla. Per risparmiare larghezza di banda e migliorare le prestazioni, l'applicazione può archiviare i dati EXIF nei metadati del BLOB quando l'applicazione carica la foto. È quindi possibile recuperare i dati EXIF nei metadati usando solo una richiesta HEAD. Il recupero dei soli metadati e non il contenuto completo del BLOB consente di risparmiare larghezza di banda significativa e di ridurre il tempo di elaborazione necessario per estrarre i dati EXIF. Tenere presente che 8 KiB di metadati possono essere archiviati per BLOB.  

## <a name="upload-blobs-quickly"></a>Caricare rapidamente i BLOB

Per caricare rapidamente i BLOB, determinare prima di tutto se si intende caricare un BLOB o molti. Usare le indicazioni seguenti per determinare il metodo corretto da usare a seconda dello scenario.  

### <a name="upload-one-large-blob-quickly"></a>Caricare rapidamente un BLOB di grandi dimensioni

Per caricare rapidamente un singolo BLOB di grandi dimensioni, un'applicazione client può caricare i blocchi o le pagine in parallelo, considerando gli obiettivi di scalabilità per i singoli BLOB e l'account di archiviazione nel suo complesso. Le librerie client di archiviazione di Azure supportano il caricamento in parallelo. Ad esempio, è possibile usare le proprietà seguenti per specificare il numero di richieste simultanee consentite in .NET o Java. Le librerie client per altre lingue supportate forniscono opzioni simili.

- Per .NET, impostare la proprietà [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) .
- Per Java/Android, chiamare il metodo [BlobRequestOptions. setConcurrentRequestCount (Final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount) .

### <a name="upload-many-blobs-quickly"></a>Caricare rapidamente molti BLOB

Per caricare rapidamente più BLOB, caricarli in parallelo. Il caricamento in parallelo è più veloce rispetto al caricamento di singoli BLOB alla volta con caricamenti di blocchi paralleli perché distribuisce il caricamento tra più partizioni del servizio di archiviazione. AzCopy esegue i caricamenti in parallelo per impostazione predefinita ed è consigliato per questo scenario. Per altre informazioni, vedere [Introduzione a AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Scegliere il tipo di BLOB corretto

Archiviazione di Azure supporta BLOB in blocchi, BLOB di Accodamento e BLOB di pagine. Per un determinato scenario di utilizzo, la scelta del tipo di BLOB influisce sulle prestazioni e sulla scalabilità della soluzione.

I BLOB in blocchi sono appropriati quando si desidera caricare in modo efficiente grandi quantità di dati. Ad esempio, un'applicazione client che carica foto o video nell'archivio BLOB è destinata ai BLOB in blocchi.

I BLOB di Accodamento sono simili ai BLOB in blocchi perché sono costituiti da blocchi. Quando si modifica un BLOB di Accodamento, i blocchi vengono aggiunti solo alla fine del BLOB. I BLOB di Accodamento sono utili per scenari come la registrazione, quando un'applicazione deve aggiungere dati a un BLOB esistente.

I BLOB di pagine sono appropriati se l'applicazione deve eseguire scritture casuali sui dati. I dischi delle macchine virtuali di Azure, ad esempio, vengono archiviati come BLOB di pagine. Per altre informazioni, vedere informazioni sui [BLOB in blocchi, BLOB di Accodamento e BLOB di pagine](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Passaggi successivi

- [Obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](scalability-targets.md)
- [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Stato e codici errore](/rest/api/storageservices/Status-and-Error-Codes2)
