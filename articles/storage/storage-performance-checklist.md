---
title: "Elenco di controllo di prestazioni e scalabilità per Archiviazione di Azure | Microsoft Docs"
description: Un elenco di controllo delle procedure consolidate per l&quot;utilizzo dell&quot;archiviazione di Azure nello sviluppo di applicazioni ad elevate prestazioni.
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 4931d68cf7e040539e7fcec7e0bc387c60a00827
ms.openlocfilehash: 9598f1fbe3375d3e37d283f259770cf53582a089


---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure
## <a name="overview"></a>Overview
Dal rilascio dei servizi di archiviazione di Microsoft Azure, Microsoft ha sviluppato diverse procedure comprovate per usare questi servizi in modo ottimale. In questo articolo vengono riepilogate le procedure più importanti, presentate sotto forma di elenco di controllo. L'intento dell'articolo è di consentire agli sviluppatori di applicazioni di assicurarsi di stare usando le procedure comprovate con l'archiviazione di Azure e di aiutarli a individuare altre eventuali procedure da adottare. Questo articolo non intende coprire tutti i possibili casi di ottimizzazione delle prestazioni e della scalabilità, pertanto esclude quelli che hanno effetti trascurabili o che non sono applicabili a un significativo numero di scenari. Per quanto è possibile prevedere il comportamento dell'applicazione durante la progettazione, è opportuno effettuare una valutazione preliminare di questi aspetti per evitare progettazioni che potrebbero causare problemi in termini di prestazioni.  

Ogni sviluppatore di applicazioni che usa l'archiviazione di Azure dovrebbe leggere attentamente questo articolo e verificare che l'applicazione su cui sta lavorando segua tutte le procedure comprovate elencate di seguito.  

## <a name="checklist"></a>Elenco di controllo
Questo articolo organizza le procedure comprovate nei seguenti gruppi. Procedure comprovate applicabili a:  

* Tutti i servizi di archiviazione di Azure (BLOB, tabelle, code e file)
* Blobs
* Tabelle
* Queues  

| Operazione completata | Area | Categoria | Domanda |
| --- | --- | --- | --- |
| &nbsp; | Tutti i servizi |Obiettivi di scalabilità |[L'applicazione è progettata per evitare di raggiungere gli obiettivi di scalabilità?](#subheading1) |
| &nbsp; | Tutti i servizi |Obiettivi di scalabilità |[La convenzione di denominazione è stata progettata per consentire un miglior bilanciamento del carico?](#subheading47) |
| &nbsp; | Tutti i servizi |Rete |[I dispositivi sul lato client hanno una larghezza di banda sufficientemente alta e una latenza sufficientemente bassa per raggiungere le prestazioni richieste?](#subheading2) |
| &nbsp; | Tutti i servizi |Rete |[I dispositivi sul lato client hanno un collegamento di qualità adeguata?](#subheading3) |
| &nbsp; | Tutti i servizi |Rete |[L'applicazione client è "accanto" all'account di archiviazione?](#subheading4) |
| &nbsp; | Tutti i servizi |Distribuzione del contenuto |[Viene usata una rete CDN per la distribuzione del contenuto?](#subheading5) |
| &nbsp; | Tutti i servizi |Accesso client diretto |[Si stanno usando le firme di accesso condiviso (SAS) e la condivisione risorse tra le origini (CORS) al posto del proxy per consentire l'accesso diretto all'archiviazione?](#subheading6) |
| &nbsp; | Tutti i servizi |Memorizzazione nella cache |[L'applicazione sta memorizzando nella cache i dati usati di frequente e modificati raramente?](#subheading7) |
| &nbsp; | Tutti i servizi |Memorizzazione nella cache |[L'applicazione sta creando batch di aggiornamenti (memorizzandoli nella cache sul lato client e caricandoli in set più grandi)?](#subheading8) |
| &nbsp; | Tutti i servizi |Configurazione .NET |[Il client è stato configurato per usare un numero sufficiente di connessioni simultanee?](#subheading9) |
| &nbsp; | Tutti i servizi |Configurazione .NET |[.NET è stato configurato per usare un numero sufficiente di thread?](#subheading10) |
| &nbsp; | Tutti i servizi |Configurazione .NET |[Si sta usando .NET 4.5 o versione successiva, che dispone di una funzionalità migliorata di Garbage Collection?](#subheading11) |
| &nbsp; | Tutti i servizi |Parallelismo |[È stata verificata la corretta associazione del parallelismo in modo da non sovraccaricare le capacità del client o gli obiettivi di scalabilità?](#subheading12) |
| &nbsp; | Tutti i servizi |Strumenti |[Si sta usando l'ultima versione delle librerie e degli strumenti client forniti da Microsoft?](#subheading13) |
| &nbsp; | Tutti i servizi |Tentativi |[Si sta usando un criterio per l'esecuzione di nuovi tentativi per il backoff esponenziale per gli errori di limitazione e i timeout?](#subheading14) |
| &nbsp; | Tutti i servizi |Tentativi |[L'applicazione sta evitando nuovi tentativi in caso di errori irreversibili?](#subheading15) |
| &nbsp; | Blobs |Obiettivi di scalabilità |[È presente un numero elevato di client che accedono contemporaneamente a un singolo oggetto?](#subheading46) |
| &nbsp; | Blobs |Obiettivi di scalabilità |[L'applicazione sta restando all'interno della larghezza di banda o dell'obiettivo di scalabilità delle operazioni per un singolo BLOB?](#subheading16) |
| &nbsp; | Blobs |Copia dei BLOB |[La copia dei BLOB sta avvenendo in modo efficace?](#subheading17) |
| &nbsp; | Blobs |Copia dei BLOB |[Si sta usando AzCopy per le copie bulk dei BLOB?](#subheading18) |
| &nbsp; | Blobs |Copia dei BLOB |[Si sta usando Importazione/Esportazione di Azure per trasferire volumi di dati molto grandi?](#subheading19) |
| &nbsp; | Blobs |Usare i metadati |[Si stanno archiviando i metadati usati di frequente relativi ai BLOB nei rispettivi metadati?](#subheading20) |
| &nbsp; | Blobs |Caricamento rapido |[Quando si tenta di caricare rapidamente un BLOB, i blocchi vengono caricati in parallelo?](#subheading21) |
| &nbsp; | Blobs |Caricamento rapido |[Quando si tenta di caricare rapidamente più BLOB, i BLOB vengono caricati in parallelo?](#subheading22) |
| &nbsp; | Blobs |Tipo di BLOB corretto |[Si stanno usando BLOB di pagine o in blocchi quando appropriato?](#subheading23) |
| &nbsp; | Tabelle |Obiettivi di scalabilità |[Si stanno raggiungendo gli obiettivi di scalabilità per le entità al secondo?](#subheading24) |
| &nbsp; | Tabelle |Configurazione |[Si sta usando JSON per le richieste della tabella?](#subheading25) |
| &nbsp; | Tabelle |Configurazione |[Nagle è stato disattivato per migliorare le prestazioni per le piccole richieste?](#subheading26) |
| &nbsp; | Tabelle |Tabelle e partizioni |[I dati sono stati partizionati correttamente?](#subheading27) |
| &nbsp; | Tabelle |Partizioni critiche |[Si stanno evitando i modelli Solo accodamenti e Solo anteposizioni?](#subheading28) |
| &nbsp; | Tabelle |Partizioni critiche |[Gli inserimenti/aggiornamenti sono distribuiti in più partizioni?](#subheading29) |
| &nbsp; | Tabelle |Ambito delle query |[Lo schema è stato progettato per consentire l'uso delle query di tipo punto nella maggior parte dei casi e delle query sulle tabelle solo in casi limitati?](#subheading30) |
| &nbsp; | Tabelle |Densità delle query |[Le query in genere analizzano e restituiscono solo le righe usate dall'applicazione?](#subheading31) |
| &nbsp; | Tabelle |Limitazione dei dati restituiti |[Vengono usati dei filtri per evitare che vengano restituite entità non necessarie?](#subheading32) |
| &nbsp; | Tabelle |Limitazione dei dati restituiti |[Viene usata la proiezione per evitare che vengano restituite proprietà non necessarie?](#subheading33) |
| &nbsp; | Tabelle |Denormalizzazione |[I dati sono stati denormalizzati in modo da evitare query non efficaci o più richieste di lettura quando si tenta di ottenere i dati?](#subheading34) |
| &nbsp; | Tabelle |Inserimento/aggiornamento/eliminazione |[Si stanno creando dei batch per le richieste che devono essere transazionali o che possono essere eseguite simultaneamente per ridurre i round-trip?](#subheading35) |
| &nbsp; | Tabelle |Inserimento/aggiornamento/eliminazione |[Si sta evitando di recuperare un'entità solo per determinare se chiamare un inserimento o un aggiornamento?](#subheading36) |
| &nbsp; | Tabelle |Inserimento/aggiornamento/eliminazione |[È stata considerata l'archiviazione di serie di dati che vengono spesso recuperate insieme in una singola entità sotto forma di proprietà invece che in più entità?](#subheading37) |
| &nbsp; | Tabelle |Inserimento/aggiornamento/eliminazione |[Per le entità recuperate sempre insieme e che possono essere scritte in batch (ad esempio, dati di serie temporali) è stato valutato l'uso di BLOB invece che di tabelle?](#subheading38) |
| &nbsp; | Queues |Obiettivi di scalabilità |[Si stanno raggiungendo gli obiettivi di scalabilità per i messaggi al secondo?](#subheading39) |
| &nbsp; | Queues |Configurazione |[Nagle è stato disattivato per migliorare le prestazioni per le piccole richieste?](#subheading40) |
| &nbsp; | Queues |Dimensioni del messaggio |[I messaggi vengono compattati per migliorare le prestazioni della coda?](#subheading41) |
| &nbsp; | Queues |Recupero bulk |[Si stanno recuperando più messaggi con un'unica operazione "Get"?](#subheading42) |
| &nbsp; | Queues |Frequenza di polling |[Il polling viene eseguito abbastanza di frequente per ridurre la latenza percepita dell'applicazione?](#subheading43) |
| &nbsp; | Queues |Aggiornamento del messaggio |[Si sta usando UpdateMessage per archiviare lo stato di elaborazione dei messaggi senza dover rielaborare l'intero messaggio in caso di errori?](#subheading44) |
| &nbsp; | Queues |Architettura |[Si stanno usando delle code per rendere più scalabile l'intera applicazione tenendo i carichi di lavoro con esecuzione prolungata fuori dal percorso critico e scalandoli indipendentemente?](#subheading45) |

## <a name="a-nameallservicesaall-services"></a><a name="allservices"></a>Tutti i servizi
Questa sezione elenca le procedure comprovate applicabili all'uso di tutti i servizi di archiviazione di Azure (BLOB, tabelle, code o file).  

### <a name="a-namesubheading1ascalability-targets"></a><a name="subheading1"></a>Obiettivi di scalabilità
Tutti i servizi di archiviazione di Azure hanno degli obiettivi di scalabilità per la capacità (GB), la frequenza di transazioni e la larghezza di banda. Se l'applicazione raggiunge o supera uno o più obiettivi di scalabilità, può verificarsi un aumento delle latenze o delle limitazioni della transazione. Quando un servizio di archiviazione limita l'applicazione, il servizio inizia a restituire i codici di errore “503 server occupato” o “500 timeout operazione” per alcune transazioni di archiviazione. Questa sezione descrive l'approccio generale per la gestione degli obiettivi di scalabilità e, in dettaglio, gli obiettivi di scalabilità della larghezza di banda. Le sezioni successive, che si occupano dei singoli servizi di archiviazione, trattano gli obiettivi di scalabilità nel contesto del servizio specifico:  

* [Larghezza di banda BLOB e richieste al secondo](#subheading16)
* [Entità di tabella al secondo](#subheading24)
* [Messaggi della coda al secondo](#subheading39)  

#### <a name="a-namesub1bandwidthabandwidth-scalability-target-for-all-services"></a><a name="sub1bandwidth"></a>Obiettivo di scalabilità della larghezza di banda per tutti i servizi
Al momento della redazione di questo documento, gli obiettivi di larghezza di banda negli Stati Uniti per un account di archiviazione con ridondanza geografica (GRS) sono di 10 gigabit al secondo (Gbps) in ingresso (dati inviati all'account di archiviazione) e di 20 Gbps in uscita (dati inviati dall'account di archiviazione). Per un account di archiviazione con ridondanza locale (LRS), i limiti sono più alti: 20 Gbps in ingresso e 30 Gbps in uscita.  I limiti di larghezza di banda internazionali possono essere inferiori e possono essere visualizzati nella [pagina degli obiettivi di scalabilità](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Per altre informazioni sulle opzioni di ridondanza dell'archiviazione, vedere i collegamenti nella sezione seguente [Risorse utili](#sub1useful) .  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Cosa fare quando si raggiunge un obiettivo di scalabilità
Se l'applicazione sta raggiungendo gli obiettivi di scalabilità per un singolo account di archiviazione, valutare uno dei seguenti approcci:  

* Esaminare di nuovo il carico di lavoro che causa il raggiungimento o il superamento dell'obiettivo di scalabilità da parte dell'applicazione. È possibile progettarlo in modo diverso in modo che usi una quantità minore di larghezza di banda o capacità o un minor numero di transazioni?
* Se un'applicazione deve superare uno degli obiettivi di scalabilità, è necessario creare più account di archiviazione e partizionare i dati dell'applicazione tra questi account di archiviazione. Se si usa questo modello, assicurarsi di progettare l'applicazione in modo da aggiungere altri account di archiviazione in futuro per il bilanciamento del carico. Al momento della redazione di questo documento, ogni sottoscrizione di Azure può supportare fino a 100 account di archiviazione.  Gli account di archiviazione, inoltre, non hanno costi aggiuntivi rispetto a quelli per l'uso, ossia associati ai dati archiviati, alle transazioni effettuate o ai dati trasferiti.
* Se l'applicazione raggiunge gli obiettivi di larghezza di banda, valutare la compressione dei dati nel client per ridurre la larghezza di banda necessaria a inviare i dati al servizio di archiviazione.  Questa operazione, che consente di risparmiare la larghezza di banda e migliorare le prestazioni di rete, può avere anche degli effettivi negativi.  Valutare gli effetti sulle prestazioni dovuti ai requisiti di elaborazione aggiuntivi per la compressione e la decompressione dei dati nel client. Inoltre, l'archiviazione dei dati compressi può rendere più difficile la risoluzione dei problemi perché ostacola la visualizzazione dei dati archiviati mediante gli strumenti standard.
* Se l'applicazione raggiunge gli obiettivi di scalabilità, assicurarsi di usare un backoff esponenziale per i nuovi tentativi (vedere [Tentativi](#subheading14)).  È meglio cercare di non raggiungere mai gli obiettivi di scalabilità (usando uno dei metodi descritti), tuttavia questo comporta che l'applicazione non riprende a eseguire rapidamente nuovi tentativi, peggiorando così la limitazione.  

#### <a name="useful-resources"></a>Risorse utili
I seguenti collegamenti forniscono ulteriori dettagli sugli obiettivi di scalabilità:

* Per informazioni sugli obiettivi di scalabilità, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md) .
* Per informazioni sulle opzioni di ridondanza di archiviazione, vedere [Replica di Archiviazione di Azure](storage-redundancy.md) e il post di blog relativo alle [opzioni di ridondanza di Archiviazione di Azure e archiviazione con ridondanza geografica e accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx).
* Per informazioni aggiornate sui prezzi dei servizi Azure, vedere [Prezzi di Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="a-namesubheading47apartition-naming-convention"></a><a name="subheading47"></a>Convenzione di denominazione delle partizioni
Archiviazione di Azure usa uno schema di partizionamento basato sugli intervalli per la scalabilità e il bilanciamento del carico del sistema. La chiave di partizione è usata per suddividere i dati in intervalli; tali intervalli vengono bilanciati a livello di carico nell'intero sistema. Ciò significa che le convenzioni di denominazione quali l'ordinamento lessicale (ad esempio msftpayroll, msftperformance, msftemployees e così via) o l'uso di timestamp (log20160101, log20160102, log20160102 e così via) determinano la possibilità che le partizioni vengano condivise nello stesso server di partizioni, finché un'operazione di bilanciamento del carico non le suddivide in intervalli più piccoli. Ad esempio, tutti i BLOB in un contenitore possono essere serviti da un singolo server fino a quando il carico di questi BLOB richiede un ulteriore bilanciamento degli intervalli di partizione. Analogamente, un gruppo di account con carico ridotto i cui nomi sono disposti in ordine lessicale può essere servito da un unico server, fino a quando il carico di uno o di tutti gli account non ne richiede la suddivisione su più server di partizioni. Ogni operazione di bilanciamento del carico può incidere negativamente sulla latenza delle chiamate di archiviazione durante l'operazione. La capacità del sistema di gestire un improvviso picco di traffico destinato a una partizione è limitata dalla scalabilità di un singolo server di partizioni, fino a quando non viene attivata l'operazione di bilanciamento del carico che torna a bilanciare l'intervallo di chiavi di partizione.  

Alcune procedure consigliate consentono di ridurre la frequenza di queste operazioni.  

* Esaminare nel dettaglio le convenzioni di denominazione usate per account, contenitori, BLOB, tabelle e code. Considerare l'aggiunta di un prefisso ai nomi di account con un hash di 3 cifre, mediante la funzione di hashing più adatta alle esigenze.  
* Se si organizzano i dati con timestamp o identificatori numerici, assicurarsi di non usare modelli di traffico Solo accodamenti (o Solo anteposizioni). Questi modelli non sono adatti a un sistema di partizionamento basato su intervalli e potrebbero indirizzare tutto il traffico a una singola partizione, impedendo un bilanciamento del carico efficiente nel sistema. Ad esempio, se sono presenti operazioni giornaliere che usano un oggetto BLOB con un timestamp quale aaaammgg, tutto il traffico dell'operazione giornaliera viene indirizzato a un singolo oggetto, gestito da un unico server di partizioni. Verificare se i limiti per BLOB e i limiti per partizione soddisfano le esigenze e, se necessario, considerare la possibilità di suddividere l'operazione in più BLOB. In modo analogo, se si archiviano dati della serie temporale nelle tabelle, è possibile che tutto il traffico venga indirizzato all'ultima parte dello spazio dei nomi della chiave. Se l'uso di timestamp o ID numerici è imprescindibile, aggiungere un hash di 3 cifre come prefisso dell'ID. Nel caso dei timestamp, aggiungere come prefisso i secondi nella stringa di data/ora, ad esempio ssaaaammgg. Se vengono eseguite regolarmente operazioni di query e creazione elenchi, scegliere una funzione di hashing che limiti il numero di query. In altri casi può essere sufficiente un prefisso casuale.  
* Per altre informazioni sullo schema di partizionamento usato in Archiviazione di Azure, leggere il documento SOSP [qui](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Rete
Oltre alle chiamate API, anche i vincoli fisici della rete dell'applicazione hanno spesso effetti significativi sulle prestazioni. Di seguito vengono descritte alcune limitazioni che gli utenti possono incontrare.  

#### <a name="client-network-capability"></a>Capacità della rete client
##### <a name="a-namesubheading2athroughput"></a><a name="subheading2"></a>Velocità effettiva.
Per la larghezza di banda il problema dipende spesso dalle capacità del client. Ad esempio, se un singolo account di archiviazione può gestire 10 Gbps o più in ingresso (vedere gli [obiettivi di scalabilità della larghezza di banda](#sub1bandwidth)), la velocità di rete in un'istanza "ridotta" del ruolo di lavoro di Azure arriva solo a circa 100 Mbps. Le istanze di Azure più grandi possono avere schede di interfaccia di rete con capacità più elevate ed è quindi opportuno prendere in considerazione l'uso di un'istanza più grande o di più macchine virtuali se è necessario che un singolo computer abbia limiti i rete più elevati. Se si accede a un servizio di archiviazione da un'applicazione locale, si applica la stessa regola: comprendere le capacità di rete del dispositivo client e la connettività di rete della posizione di archiviazione di Azure, quindi aumentarle in base alle esigenze o progettare l'applicazione in modo che funzioni con i limiti di capacità disponibili.  

##### <a name="a-namesubheading3alink-quality"></a><a name="subheading3"></a>Qualità del collegamento
Come accade in ogni rete, tenere presente che le condizioni di rete che generano errori e perdita di pacchetti riducono la velocità effettiva.  L'uso di WireShark o NetMon può contribuire a diagnosticare il problema.  

##### <a name="useful-resources"></a>Risorse utili
Per altre informazioni sulle dimensioni della macchina virtuale e sulla larghezza di banda allocata, vedere [Dimensioni delle macchine virtuali di Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Dimensioni delle macchine virtuali di Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="a-namesubheading4alocation"></a><a name="subheading4"></a>Posizione
In qualsiasi ambiente distribuito, il posizionamento del client accanto al server offre le prestazioni migliori. Per accedere all'archiviazione di Azure con la minor latenza possibile, è opportuno posizionare il client nella stessa area di Azure. Ad esempio, se si ha un sito Web di Azure che usa l'archiviazione di Azure, posizionare entrambi in un'unica area (ad esempio, Stati Uniti occidentali o Asia sudorientale). In questo modo si riducono latenza e costi. Attualmente, infatti, l'uso della larghezza di banda in un'unica area è gratuito.  

Anche nel caso di applicazioni client non ospitate in Azure (ad esempio, app di dispositivi mobili o servizi aziendali locali) la latenza viene ridotta se si posiziona l'account di archiviazione in un'area vicina ai dispositivi che si connetteranno a tale account. Se i client sono distribuiti in un'area ampia (ad esempio, alcuni in America del Nord e altri in Europa), è opportuno usare più account di archiviazione: uno nell'area nordamericana e uno in quella europea. Ciò consente di ridurre la latenza per gli utenti di entrambe le aree. Questo approccio è generalmente più semplice da implementare se i dati archiviati dall'applicazione sono specifici di singoli utenti e non richiedono la replica tra gli account di archiviazione.  Per una distribuzione dei contenuti di grandi dimensioni si consiglia l'uso di una rete CDN. Vedere la sezione seguente per altri dettagli.  

### <a name="a-namesubheading5acontent-distribution"></a><a name="subheading5"></a>Distribuzione di contenuti
A volte un'applicazione deve fornire lo stesso contenuto a più utenti (ad esempio, un video dimostrativo di un prodotto usato nella home page di un sito Web) che si trovano in un'unica area o in aree diverse. In questo scenario è opportuno usare una rete per la distribuzione di contenuti (CDN, Content Delivery Network), ad esempio la rete CDN di Azure, che usa l'archiviazione di Azure come origine dei dati. A differenza di un account di archiviazione di Azure, presente in un'unica area e che non può fornire contenuti con una bassa latenza in altre aree, la rete CDN di Azure usa diversi server in più data center in tutto il mondo. Inoltre, una rete CDN in genere supporta limiti in uscita molto più elevati rispetto a un singolo account di archiviazione.  

Per altre informazioni sulla rete CDN di Azure, vedere [rete CDN di Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="a-namesubheading6ausing-sas-and-cors"></a><a name="subheading6"></a>Uso delle firme di accesso condiviso (SAS) e della condivisione risorse tra le origini (CORS)
Per autorizzare codice come JavaScript in un Web browser dell'utente o in un'app di un telefono cellulare per accedere ai dati nell'archiviazione di Azure, un approccio consiste nell'usare un'applicazione nel ruolo Web come proxy: il dispositivo dell'utente viene autenticato con il ruolo Web che a sua volta viene autenticato con il servizio di archiviazione. In questo modo si evita di esporre le chiavi dell'account di archiviazione in dispositivi non sicuri. Tuttavia, ciò causa un notevole sovraccarico del ruolo Web perché tutti i dati trasferiti tra il dispositivo dell'utente e il servizio di archiviazione devono passare attraverso il ruolo Web. È possibile evitare l'uso di un ruolo Web come proxy per il servizio di archiviazione usando le firme di accesso condiviso, talvolta insieme alle intestazioni di condivisione risorse tra le origini (CORS). Con le firme di accesso condiviso è possibile consentire al dispositivo dell'utente di indirizzare le richieste direttamente a un servizio di archiviazione attraverso un token con accesso limitato. Ad esempio, se un utente vuole caricare una foto nell'applicazione, il ruolo Web può generare e inviare al dispositivo dell'utente un token SAS che concede un'autorizzazione di scrittura in uno specifico BLOB o in un contenitore per i successivi 30 minuti (dopo i quali il token SAS scade).

In genere un browser non consente JavaScript in una pagina ospitata da un sito Web in un dominio per l'esecuzione di operazioni specifiche come “PUT” in un altro dominio. Ad esempio, se si ospita un ruolo Web in “contosomarketing.cloudapp.net” e si vuole usare JavaScript sul lato client per caricare un BLOB nell'account di archiviazione in “contosoproducts.blob.core.windows.net”, il "criterio della stessa origine" del browser impedisce l'operazione. CORS è una funzionalità del browser che consente al dominio di destinazione (in questo caso l'account di archiviazione) di comunicare con il browser di cui reputa attendibili le richieste originate nel dominio di origine (in questo caso il ruolo Web).  

Entrambe le tecnologie possono aiutare a evitare carichi o colli di bottiglia non necessari nell'applicazione Web.  

#### <a name="useful-resources"></a>Risorse utili
Per altre informazioni sulle firme di accesso condiviso, vedere [Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).  

Per altre informazioni sulla condivisione risorse tra le origini, vedere [Supporto della condivisione delle risorse tra le origini (CORS) per i servizi di archiviazione Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Memorizzazione nella cache
#### <a name="a-namesubheading7agetting-data"></a><a name="subheading7"></a>Recupero dei dati
In generale è meglio recuperare i dati da un servizio una sola volta e non ripetutamente. Considerare l'esempio di un'applicazione Web MVC eseguita in un ruolo Web che ha già recuperato un BLOB da 50 MB dal servizio di archiviazione da usare come contenuto per un utente. L'applicazione può quindi recuperare lo stesso BLOB ogni volta che un utente lo richiede o può memorizzarlo nella cache locale sul disco e riusare questa versione per le successive richieste dell'utente. Inoltre, quando un utente richiede i dati, l'applicazione può generare un'operazione GET con un'intestazione condizionale per il tempo di modifica che evita di recuperare l'intero BLOB se non è stato modificato. È possibile applicare lo stesso modello quando si usano le entità di tabella.  

In alcuni casi si può impostare l'applicazione in modo che consideri valido il BLOB per un breve periodo di tempo dopo il recupero e che, durante questo periodo, non controlli se il BLOB sia stato modificato.

I dati di configurazione, di ricerca e altri dati usati di frequente dall'applicazione sono ottimi candidati per la memorizzazione nella cache.  

Per un esempio di come recuperare le proprietà di un BLOB per individuare la data dell'ultima modifica con .NET, vedere [Impostare e recuperare proprietà e metadati](storage-properties-metadata.md). Per altre informazioni sui download condizionali, vedere [Aggiornare una copia locale di un Blob in modo condizionale](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="a-namesubheading8auploading-data-in-batches"></a><a name="subheading8"></a>Caricamento dei dati in batch
In alcuni scenari dell'applicazione è possibile aggregare i dati localmente e caricarli periodicamente in un batch invece di caricare subito i singoli dati. Ad esempio, un'applicazione Web può mantenere un file di log delle attività: l'applicazione può caricare i dettagli delle singole attività man mano che vengono eseguite come entità di tabella (il che richiede molte operazioni di archiviazione) oppure può salvarli in un file di log locale e caricare periodicamente tutti i dettagli dell'attività in un file delimitato in un BLOB. Se ogni voce di log ha una dimensione pari a 1 KB, è possibile caricare migliaia di voci in un'unica transazione “Put Blob” (è possibile caricare un BLOB con una dimensione massima di 64 MB in un'unica transazione). Ovviamente se il computer locale si arresta prima del caricamento, è possibile che vengano persi dei dati del log: lo sviluppatore di applicazioni deve tenere conto della possibilità di un arresto del dispositivo client o del caricamento.  Se i dati dell'attività devono essere scaricati in base agli intervalli di tempo (non alle singole attività), si consiglia di usare i BLOB invece delle tabelle.

### <a name="net-configuration"></a>Configurazione .NET
Se si usa .NET Framework, in questa sezione vengono elencate diverse impostazioni di configurazione rapide che è possibile usare per migliorare significativamente le prestazioni.  Se si usano altri linguaggi, controllare se esistono procedure simili per il linguaggio prescelto.  

#### <a name="a-namesubheading9aincrease-default-connection-limit"></a><a name="subheading9"></a>Aumento del limite di connessione predefinito
In .NET, il seguente codice aumenta il limite di connessione predefinito (in genere pari a 2 in un ambiente client o 10 in un ambiente server) a 100. Solitamente il valore deve essere impostato basandosi approssimativamente sul numero di thread usato dall'applicazione.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

È necessario impostare il limite di connessione prima di aprire le connessioni.  

Per gli altri linguaggi di programmazione, vedere la documentazione specifica per determinare come impostare il limite di connessione.  

Per altre informazioni, vedere il blog post [Servizi Web: connessioni simultanee](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="a-namesubheading10aincrease-threadpool-min-threads-if-using-synchronous-code-with-async-tasks"></a><a name="subheading10"></a>Aumentare il numero minimo di thread di ThreadPool se si usa codice sincrono con attività asincrone
Questo codice aumenta il numero minimo di thread del pool di thread:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Per altre informazioni, vedere [Metodo ThreadPool.SetMinThreads](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="a-namesubheading11atake-advantage-of-net-45-garbage-collection"></a><a name="subheading11"></a>Vantaggi della funzionalità Garbage Collection di .NET 4.5
Usare .NET 4.5 o versione successiva per consentire all'applicazione client di sfruttare i miglioramenti delle prestazioni relativi alla funzionalità Garbage Collection del server.

Per altre informazioni, vedere l'articolo [Panoramica dei miglioramenti delle prestazioni in .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="a-namesubheading12aunbounded-parallelism"></a><a name="subheading12"></a>Parallelismo non associato
Il parallelismo è un metodo efficace per mantenere il livello delle prestazioni, tuttavia è necessario prestare attenzione quando si usa il parallelismo non associato (senza limiti sul numero di thread e/o di richieste parallele) per caricare o scaricare i dati, quando si usano più lavori per accedere a diverse partizioni (contenitori, code o partizioni di tabelle) nello stesso account di archiviazione o per accedere a più elementi nella stessa partizione. Se il parallelismo non è associato, l'applicazione può superare le capacità del dispositivo client o gli obiettivi di scalabilità dell'account di archiviazione producendo latenze più lunghe e limitazioni.  

### <a name="a-namesubheading13astorage-client-libraries-and-tools"></a><a name="subheading13"></a>Librerie e strumenti client dell'archiviazione
Usare sempre l'ultima versione delle librerie e degli strumenti client forniti da Microsoft. Al momento della redazione di questo documento, sono disponibili librerie client per .NET, Windows Phone, Windows Runtime, Java e C++ nonché librerie di anteprima per altri linguaggi. Inoltre, Microsoft ha rilasciato i cmdlet di PowerShell e i comandi CLI Azure da usare con l'archiviazione di Azure. Microsoft sviluppa attivamente questi strumenti concentrandosi sulle prestazioni, li mantiene aggiornati con le ultime versioni del servizio e verifica che siano in grado di gestire internamente gran parte delle procedure comprovate relative alle prestazioni.  

### <a name="retries"></a>Tentativi
#### <a name="a-namesubheading14athrottlingserverbusy"></a><a name="subheading14"></a>Limitazione/ServerBusy
In alcuni casi il servizio di archiviazione può limitare l'applicazione o semplicemente non essere in grado di completare la richiesta a causa di condizioni temporanee; in questi casi, viene restituito un messaggio “503 server occupato” o “500 Timeout”.  Questo accade se l'applicazione sta raggiungendo gli obiettivi di scalabilità o se il sistema sta ribilanciando i dati partizionati per consentire una maggiore velocità effettiva.  L'applicazione client in genere deve ripetere l'operazione che ha causato l'errore: un tentativo successivo della stessa richiesta può avere esito positivo. Tuttavia, se il servizio di archiviazione sta limitando l'applicazione perché supera gli obiettivi di scalabilità o non è in grado di completare la richiesta per altri motivi, la ripetizione dei tentativi non fa che peggiorare il problema. Per questo motivo è opportuno usare un backoff esponenziale (nelle librerie client è il comportamento predefinito). Ad esempio, l'applicazione può riprovare l'operazione dopo 2 secondi, 4 secondi, 10 secondi, 30 secondi, dopo di che non effettua altri tentativi. Piuttosto che peggiorare il problema, questo comportamento consente di ridurre notevolmente il carico dell'applicazione sul servizio.  

I nuovi tentativi relativi a errori di connettività possono eseguiti immediatamente perché non dipendono dalle limitazioni e sono considerati temporanei.  

#### <a name="a-namesubheading15anon-retryable-errors"></a><a name="subheading15"></a>Errori irreversibili
Le librerie client distinguono gli errori reversibili da quelli irreversibili. Tuttavia, se si scrive il proprio codice in base all'API REST di archiviazione, ricordare che esistono degli errori per i quali non devono essere effettuati nuovi tentativi: ad esempio, una risposta 400 (Richiesta non valida) indica che l'applicazione client ha inviato una richiesta che non è possibile elaborare perché non è nel formato previsto. Un nuovo invio di questa richiesta genererà sempre la stessa risposta, quindi è inutile riprovare. Se si scrive il proprio codice in base all'API REST di archiviazione, tenere presente il significato dei codici di errore e il modo corretto (e quello errato) di eseguire nuovi tentativi per ciascuno di essi.  

#### <a name="useful-resources"></a>Risorse utili
Per altre informazioni sui codici di errore di archiviazione, vedere [Codici ed errori di stato](http://msdn.microsoft.com/library/azure/dd179382.aspx) nel sito Web di Microsoft Azure.  

## <a name="blobs"></a>Blobs
Oltre alle procedure comprovate per [Tutti i servizi](#allservices) descritte prima, le seguenti procedure comprovate si applicano specificamente al servizio BLOB.  

### <a name="blob-specific-scalability-targets"></a>Obiettivi di scalabilità specifici per BLOB
#### <a name="a-namesubheading46amultiple-clients-accessing-a-single-object-concurrently"></a><a name="subheading46"></a>Accesso simultaneo di più client a un singolo oggetto
Se si ha un numero elevato di client che accedono contemporaneamente a un singolo oggetto, è necessario prendere in considerazione obiettivi di scalabilità per oggetto e account di archiviazione. Il numero esatto di client che possono accedere a un singolo oggetto dipende da fattori quali il numero di client che richiedono l'oggetto contemporaneamente, le dimensioni dell'oggetto, le condizioni della rete e così via.

Se l'oggetto può essere distribuito tramite una rete CDN, ad esempio nel caso di immagini o video serviti da un sito Web, è consigliabile usare una rete CDN. Vedere [qui](#subheading5).

In altri scenari, ad esempio simulazioni scientifiche in cui i dati sono riservati, sono disponibili due opzioni. La prima è suddividere l'accesso del carico di lavoro in fasi successive in modo che l'accesso all'oggetto sia diluito nel tempo e non simultaneo. In alternativa, è possibile copiare temporaneamente l'oggetto in più account di archiviazione, aumentando il numero totale di IOPS per ogni oggetto e tra gli account di archiviazione. In test limitati è stato rilevato che circa 25 macchine virtuali possono scaricare simultaneamente un BLOB di 100 GB in parallelo, ogni macchina virtuale ha parallelizzato il download con 32 thread. Se 100 client devono accedere all'oggetto, copiarlo prima di tutto in un secondo account di archiviazione e quindi fare in modo che le prime 50 macchine virtuali accedano al primo BLOB e le seconde 50 macchine virtuali accedano al secondo BLOB. I risultati variano a seconda del comportamento delle applicazioni. È quindi opportuno eseguire dei test in fase di progettazione. 

#### <a name="a-namesubheading16abandwidth-and-operations-per-blob"></a><a name="subheading16"></a>Larghezza di banda e operazioni per BLOB
È possibile leggere o scrivere in un singolo BLOB a un massimo di 60 MB/secondo (circa 480 Mbps, un valore che supera le capacità di molte reti sul lato client inclusa la scheda di interfaccia di rete fisica nel dispositivo client). Inoltre, un singolo BLOB supporta fino a 500 richieste al secondo. Se si hanno più client che devono leggere lo stesso BLOB e si teme di superare questi limiti, valutare l'uso di una rete CDN per la distribuzione del BLOB.  

Per altre informazioni sulla velocità effettiva da raggiungere per i BLOB, vedere [Obiettivi di scalabilità e prestazioni di archiviazione di Azure](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copia e spostamento dei BLOB
#### <a name="a-namesubheading17acopy-blob"></a><a name="subheading17"></a>Copia Blob
La versione dell'API REST di archiviazione 2012-02-12 ha introdotto un'utile funzionalità che consente di copiare i BLOB in diversi account: un'applicazione client può impostare il servizio di archiviazione in modo che copi un BLOB da un'altra origine (possibilmente in un altro account di archiviazione) e lasciare che il servizio esegua la copia in modo asincrono. Ciò consente di ridurre notevolmente la larghezza di banda necessaria all'applicazione quando si esegue la migrazione dei dati da altri account di archiviazione perché non si devono scaricare e caricare i dati.  

Tuttavia, tenere presente che quando si eseguono copie tra gli account di archiviazione non esistono garanzie sul tempo richiesto per il completamento. Se l'applicazione deve completare rapidamente una copia BLOB sotto il controllo dell'utente, si consiglia di copiare il BLOB scaricandolo in una macchina virtuale e caricandolo nella destinazione desiderata.  Per una prevedibilità completa in questa situazione verificare che la copia venga eseguita da una macchina virtuale in esecuzione nella stessa area di Azure; in caso contrario, è possibile (anzi, probabile) che le condizioni di rete abbiano effetto sulle prestazioni di copia.  Inoltre, è possibile monitorare lo stato di una copia asincrona a livello di codice.  

Le copie nello stesso account di archiviazione vengono generalmente completate rapidamente.  

Per altre informazioni, vedere [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="a-namesubheading18ause-azcopy"></a><a name="subheading18"></a>Usare AzCopy
Il team di archiviazione di Azure ha rilasciato lo strumento da riga di comando "AzCopy" progettato per supportare il trasferimento in blocco di più BLOB verso, da e tra account di archiviazione.  Questo strumento è ottimizzato per questo scenario e può raggiungere elevate velocità di trasferimento.  Se ne consiglia l'uso negli scenari di caricamento, download e copia bulk. Per altre informazioni sullo strumento e per scaricarlo, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).  

#### <a name="a-namesubheading19aazure-importexport-service"></a><a name="subheading19"></a>Servizio di importazione/esportazione di Azure
Per volumi di dati molto grandi (più di 1 TB), l'archiviazione di Azure offre il servizio di importazione/esportazione che consente di caricare e scaricare dall'archiviazione BLOB tramite l'invio di dischi rigidi.  È possibile inserire i dati in un disco rigido e inviarlo a Microsoft per il caricamento oppure inviare un disco rigido vuoto a Microsoft per scaricare i dati.  Per altre informazioni, vedere [Usare il servizio di Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](storage-import-export-service.md).  Questo metodo può risultare più efficace rispetto al caricamento/download di elevati volumi di dati tramite rete.  

### <a name="a-namesubheading20ause-metadata"></a><a name="subheading20"></a>Usare i metadati
Il servizio BLOB supporta le richieste head che possono includere i metadati relativi al BLOB. Ad esempio, se l'applicazione richiede i dati EXIF inclusi in una foto, può recuperare la foto ed estrarli. Per risparmiare larghezza di banda e migliorare le prestazioni, l'applicazione può archiviare i dati EXIF nei metadati del BLOB quando l'applicazione carica la foto: i dati EXIF possono essere poi recuperati nei metadati usando semplicemente una richiesta HEAD, risparmiando una notevole quantità di larghezza di banda e il tempo di elaborazione necessario a estrarre i dati EXIF a ogni lettura del BLOB. Questa funzionalità è utile in scenari in cui sono necessari solo i metadati e non l'intero contenuto di un BLOB.  È possibile archiviare solo 8 KB di metadati per BLOB (il servizio non accetta richieste per valori superiori), quindi se i dati superano queste dimensioni non sarà possibile usare questo approccio.  

Per un esempio su come recuperare i metadati di un BLOB con .NET, vedere [Impostare e recuperare proprietà e metadati](storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Caricamento rapido
Per caricare rapidamente i BLOB, è necessario definire se caricare un solo BLOB o più BLOB.  Usare le indicazioni seguenti per determinare il metodo corretto da usare a seconda dello scenario.  

#### <a name="a-namesubheading21auploading-one-large-blob-quickly"></a><a name="subheading21"></a>Caricamento rapido di un BLOB di grandi dimensioni
Per caricare rapidamente un singolo BLOB di grandi dimensioni, l'applicazione client deve caricare i blocchi o le pagine in parallelo (tenendo presenti gli obiettivi di scalabilità per i singoli BLOB e per l'intero account di archiviazione).  Le librerie client di archiviazione RTM fornite da Microsoft (.NET, Java) includono questa funzionalità.  Per ciascuna libreria usare l'oggetto/proprietà specificato di seguito per impostare il livello di concorrenza:  

* .NET: Impostare ParallelOperationThreadCount in un oggetto BlobRequestOptions da usare.
* Java/Android: Usare BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Usare parallelOperationThreadCount nelle opzioni della richiesta o nel servizio BLOB.
* C++: Usare il metodo blob_request_options::set_parallelism_factor.

#### <a name="a-namesubheading22auploading-many-blobs-quickly"></a><a name="subheading22"></a>Caricamento rapido di più BLOB
Per caricare rapidamente più BLOB, caricarli in parallelo. È una procedura più rapida rispetto al caricamento dei singoli BLOB con caricamenti di blocchi paralleli perché distribuisce il caricamento su più partizioni del servizio di archiviazione. Un singolo BLOB supporta una velocità effettiva di soli 60 MB/secondo (circa 480 Mbps). Al momento della redazione di questo documento, un account di archiviazione con ridondanza locale (LRS) basato negli Stati Uniti supporta fino a 20 Gbps in ingresso, un valore molto più alto della velocità effettiva supportata da un singolo BLOB.  [AzCopy](#subheading18) esegue i caricamenti in parallelo per impostazione predefinita ed è consigliato per questo scenario.  

### <a name="a-namesubheading23achoosing-the-correct-type-of-blob"></a><a name="subheading23"></a>Scelta del tipo di BLOB corretto
Archiviazione di Azure supporta due tipi di BLOB: BLOB di *pagine* e BLOB in *blocchi*. Per un determinato scenario di utilizzo, la scelta del tipo di BLOB influisce sulle prestazioni e sulla scalabilità della soluzione. I BLOB in blocchi sono appropriati quando si vogliono caricare grandi quantità di dati in modo efficace: ad esempio nel caso di un'applicazione client che necessita di caricare foto o video nell'archiviazione BLOB. I BLOB di pagine sono appropriati quando l'applicazione deve eseguire scritture casuali sui dati: ad esempio, i dischi rigidi virtuali di Azure vengono archiviati come BLOB di pagine.  

Per altre informazioni, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabelle
Oltre alle procedure comprovate per [Tutti i servizi](#allservices) descritte prima, le seguenti procedure comprovate si applicano specificamente al servizio tabelle.  

### <a name="a-namesubheading24atable-specific-scalability-targets"></a><a name="subheading24"></a>Obiettivi di scalabilità specifici per tabelle
Oltre ai limiti della larghezza di banda dell'intero account di archiviazione, le tabelle hanno il seguente limite di scalabilità.  Il sistema bilancia il carico man mano che il traffico aumenta, ma se si verificano incrementi improvvisi del traffico il volume della velocità effettiva potrebbe non essere raggiunto immediatamente.  Se il modello presenta degli incrementi improvvisi è probabile che vengano visualizzai messaggi di limitazione e/o timeout durante l'incremento perché il servizio di archiviazione bilancia automaticamente il carico della tabella.  Un incremento lento in genere produce risultati migliori perché lascia al sistema il tempo di bilanciare il carico in modo corretto.  

#### <a name="entities-per-second-account"></a>Entità al secondo (account)
Il limite di scalabilità per l'accesso alle tabelle è di 20.000 entità (1 KB ciascuna) al secondo per un account.  In generale ogni entità inserita, aggiornata, eliminata o analizzata viene presa in considerazione ai fini del calcolo per l'obiettivo.  Quindi, un inserimento batch che contiene 100 entità conta come 100 entità.  Una query che analizza 1000 entità e ne restituisce 5 conta come 1000 entità.  

#### <a name="entities-per-second-partition"></a>Entità al secondo (partizione)
In una singola partizione l'obiettivo di scalabilità per l'accesso alle tabelle è di 2.000 entità (1 KB ciascuna) al secondo, usando lo stesso conteggio descritto nella sezione precedente.  

### <a name="configuration"></a>Configurazione
In questa sezione vengono elencate diverse impostazioni di configurazione rapide che è possibile usare per migliorare significativamente le prestazioni nel servizio tabelle:  

#### <a name="a-namesubheading25ause-json"></a><a name="subheading25"></a>Usare JSON
A partire dalla versione del servizio di archiviazione 2013-08-15, il servizio tabelle supporta l'uso di JSON al posto del formato AtomPub basato su XML per il trasferimento dei dati della tabella. Ciò consente di ridurre le dimensioni del payload di una percentuale massima del 75% e può migliorare notevolmente le prestazioni dell'applicazione.

Per altre informazioni, vedere il post [Microsoft Azure Tables: Introducing JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) (Tabelle di Microsoft Azure: introduzione a JSON) e [Payload Format for Table Service Operations](http://msdn.microsoft.com/library/azure/dn535600.aspx) (Formato di Payload per operazioni del servizio tabelle).

#### <a name="a-namesubheading26anagle-off"></a><a name="subheading26"></a>Disattivazione di Nagle
L'algoritmo Nagle viene spesso implementato nelle reti TCP/IP come strumento per migliorare le prestazioni di rete. Tuttavia, non è la soluzione ottimale in tutti gli scenari (ad esempio, gli ambienti ad alta interazione). Nell'archiviazione di Azure l'algoritmo Nagle ha un impatto negativo sulle prestazioni delle richieste ai servizi tabelle e code e, se possibile, dovrebbe essere disabilitato.  

Per altre informazioni, vedere il post del blog relativo al [comportamento dell'algoritmo Nagle nei confronti delle piccole richieste](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx)che spiega il motivo per cui l'algoritmo Nagle interagisce in modo poco efficace con le richieste relative a tabelle e code e che mostra come disabilitarlo nell'applicazione client.  

### <a name="schema"></a>Schema
La modalità con cui vengono rappresentati i dati e vengono eseguite query su di essi è il fattore singolo più importante che influisce sulle prestazioni del servizio tabelle. Ogni applicazione è diversa, ma in questa sezione vengono descritte alcune procedure comprovate generali relative a:  

* Progettazione della tabella
* Query efficaci
* Aggiornamenti dei dati efficaci  

#### <a name="a-namesubheading27atables-and-partitions"></a><a name="subheading27"></a>Tabelle e partizioni
Le tabella sono divise in partizioni. Ogni entità archiviata in una partizione condivide la stessa chiave di partizione e dispone di una chiave di riga univoca che la identifica all'interno della partizione. Le partizioni offrono dei vantaggi ma introducono anche dei limiti di scalabilità.  

* Benefici: È possibile aggiornare le entità nella stessa partizione in un'unica transazione batch atomica che contiene fino a 100 operazioni di archiviazione distinte (limite di 4 MB di dimensioni totali). Presupponendo lo stesso numero di entità da recuperare, è anche possibile eseguire query sui dati all'interno di una singola partizione in modo più efficiente rispetto ai dati distribuiti in più partizioni (tuttavia, leggere più avanti per ulteriori consigli sulle query sui dati nelle tabelle).
* Limite di scalabilità: L'accesso alle entità archiviate in una singola partizione non può essere sottoposto a bilanciamento del carico perché le partizioni supportano le transazioni batch atomiche. Per questo motivo, l'obiettivo di scalabilità per una singola partizione della tabella è inferiore rispetto a quello dell'intero servizio tabelle.  

Tenendo conto delle caratteristiche descritte di tabelle e partizioni, adottare i seguenti principi di progettazione:  

* I dati aggiornati o su cui vengono eseguite query più di frequente da parte dell'applicazione client nella stessa unità logica di lavoro devono trovarsi nella stessa partizione.  Questo perché l'applicazione sta aggregando le scritture oppure perché l'utente vuole usare le operazioni batch atomiche.  Inoltre, le query sui dati in una singola partizione possono essere eseguite in modo più efficace con un'unica query rispetto a quelle sui dati in più partizioni.
* I dati che l'applicazione client non inserisce/aggiorna o di cui non esegue query nella stessa unità logica di lavoro (query singola o aggiornamento batch) devono essere posizionati in partizioni separate.  È importante notare che non esiste un limite al numero di chiavi di partizione in una singola tabella, quindi l'esistenza di milioni di chiavi di partizione non rappresenta un problema e non influisce sulle prestazioni.  Ad esempio, se l'applicazione è un sito Web diffuso con accesso utente, l'uso dell'ID utente come chiave di partizione potrebbe rappresentare una buona scelta.  

#### <a name="hot-partitions"></a>Partizioni critiche
Una partizione critica riceve una percentuale sproporzionata del traffico di un account e non è possibile sottoporla a bilanciamento del carico perché è una partizione singola.  In generale, le partizioni critiche vengono create in uno di questi due modi:  

##### <a name="a-namesubheading28aappend-only-and-prepend-only-patterns"></a><a name="subheading28"></a>Modelli Solo accodamenti e Solo anteposizioni
In un modello “Solo accodamenti” tutto (o quasi tutto) il traffico verso un determinato PK aumenta o diminuisce in base all'ora corrente.  Un esempio è quello dell'applicazione che usa la data corrente come chiave di partizione per i dati di log.  Ciò comporta che tutti gli inserimenti vanno nell'ultima partizione della tabella e il sistema non riesce a bilanciare il carico perché tutte le scritture si trovano alla fine della tabella.  Se il volume di traffico verso la partizione supera l'obiettivo di scalabilità a livello della partizione viene generata una limitazione.  È opportuno assicurarsi che il traffico sia inviato a più partizioni per abilitare il bilanciamento del carico delle richieste nella tabella.  

##### <a name="a-namesubheading29ahigh-traffic-data"></a><a name="subheading29"></a>Dati con traffico elevato
Se lo schema di partizionamento produce una singola partizione con dati molto più usati rispetto alle altre partizioni, può essere generata una limitazione man mano che la partizione raggiunge l'obiettivo di scalabilità per una singola partizione.  È opportuno verificare che lo schema di partizione non generi partizioni singole che raggiungono gli obiettivi di scalabilità.  

#### <a name="querying"></a>Query
Questa sezione descrive le procedure comprovate per le eseguire query nel servizio tabelle.  

##### <a name="a-namesubheading30aquery-scope"></a><a name="subheading30"></a>Ambito delle query
Esistono diversi metodi per specificare l'intervallo di entità su cui eseguire query.  Di seguito viene descritto l'uso di ciascun metodo.  

In generale si consiglia di evitare le analisi (query più grandi di una singola entità), ma, se sono necessarie, provare a organizzare i dati in modo che le analisi recuperino i dati necessari senza analizzare o restituire grandi quantità di entità non necessarie.  

###### <a name="point-queries"></a>Query di tipo punto
Una query di tipo punto recupera esattamente un'entità. Per farlo specifica sia la chiave di partizione che la chiave di riga dell'entità da recuperare. Queste query sono molto efficaci e vanno usate quando possibile.  

###### <a name="partition-queries"></a>Query sulle partizioni
Una query sulle partizioni recupera un set di dati che condivide una chiave di partizione comune. In genere la query specifica un intervallo di valori della chiave di riga o un intervallo di valori relativi a una proprietà dell'entità, oltre alla chiave di partizione. Queste query sono meno efficaci delle query di tipo punto e devono essere usate in casi limitati.  

###### <a name="table-queries"></a>Query sulle tabelle
Una query sulle tabelle recupera un set di entità che non condivide una chiave di partizione comune. Queste query non sono efficaci e, se possibile, vanno evitate.  

##### <a name="a-namesubheading31aquery-density"></a><a name="subheading31"></a>Densità delle query
Un altro fattore importante in termini di efficacia delle query è il numero di entità restituite rispetto al numero di entità analizzate per trovare il set restituito. Se l'applicazione esegue una query sulle tabelle con un filtro per un valore della proprietà condiviso solo dall'1% dei dati, la query analizza 100 entità per ciascuna entità restituita. Gli obiettivi di scalabilità della tabella descritti in precedenza fanno tutti riferimento al numero di entità analizzate e non al numero di entità restituite: è probabile che una densità delle query bassa produca una limitazione dell'applicazione nel servizio tabelle perché devono essere analizzate troppe entità per recuperare quella cercata.  Vedere la sezione seguente sulla [denormalizzazione](#subheading34) per altre informazioni su come evitare il problema.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitazione della quantità di dati restituiti
###### <a name="a-namesubheading32afiltering"></a><a name="subheading32"></a>Filtri
Se si sa che una query restituisce entità non necessarie nell'applicazione client, valutare l'uso di un filtro per ridurre le dimensioni del set restituito. Le entità non restituite al client vengono comunque considerate per il calcolo dei limiti di scalabilità, ma le prestazioni dell'applicazione migliorano grazie alle minori dimensioni del payload di rete e al numero ridotto di entità che l'applicazione client deve elaborare.  Tuttavia, anche qui si applica la nota precedente sulla [Densità delle query](#subheading31): gli obiettivi di scalabilità fanno riferimento al numero di entità analizzate quindi una query che esclude molte entità può comunque produrre una limitazione, anche se vengono restituite poche entità.  

###### <a name="a-namesubheading33aprojection"></a><a name="subheading33"></a>Proiezione
Se l'applicazione client necessita solo di un set di proprietà limitato dall'entità nella tabella, è possibile usare la proiezione per limitare le dimensioni del set di dati restituito. Come nel caso dei filtri, questa funzionalità consente di ridurre il carico di rete e l'elaborazione del client.  

##### <a name="a-namesubheading34adenormalization"></a><a name="subheading34"></a>Denormalizzazione
A differenza dell'uso dei database relazionali, le procedure comprovate per l'esecuzione di query efficaci sui dati della tabella portano alla denormalizzazione dei dati. In altre parole, invece di analizzare numeri elevati di entità per individuare i dati richiesti dall'applicazione, vengono duplicati gli stessi dati in più entità (una per ogni chiave usata per trovare i dati) per ridurre il numero di entità che una query deve analizzare per trovare i dati richiesti dal client.  Ad esempio, in un sito Web di e-commerce è possibile cercare un ordine in base all'ID cliente (ricerca degli ordini dello specifico cliente) e alla data (ricerca degli ordini con una specifica data).  Nell'archiviazione tabelle si consiglia di archiviare l'entità (o un riferimento all'entità) due volte: una con nome tabella, PK e RK per semplificare la ricerca in base all'ID cliente e una per semplificare la ricerca in base alla data.  

#### <a name="insertupdatedelete"></a>Inserimento/aggiornamento/eliminazione
Questa sezione descrive le procedure comprovate per modificare le entità archiviate nel servizio tabelle.  

##### <a name="a-namesubheading35abatching"></a><a name="subheading35"></a>Creazione di batch
Le transazioni batch sono note come transazioni dei gruppi di entità (ETG, Entity Group Transaction) nell'archiviazione di Azure; tutte le operazioni all'interno di un'ETG devono essere su una singola partizione in una tabella singola. Dove possibile, usare le ETG per eseguire inserimenti, aggiornamenti ed eliminazioni in batch. In questo modo si riduce il numero di round trip dall'applicazione client al server, si limita il numero di transazioni fatturabili (un'ETG conta come una singola transazione ai fini della fatturazione e può contenere fino a 100 operazioni di archiviazione) e si abilitano gli aggiornamenti atomici (l'esito positivo o negativo di un processo si applica a tutte le operazioni all'interno dell'ETG). Gli ambienti con latenze elevate come i dispositivi mobili possono trarre grandi vantaggi dall'uso delle ETG.  

##### <a name="a-namesubheading36aupsert"></a><a name="subheading36"></a>Upsert
Usare le operazioni della tabella **Upsert** quando possibile. Esistono due tipi di **Upsert**, entrambi più efficaci di una tradizionale operazione di **inserimento** e **aggiornamento**:  

* **InsertOrMerge**: da usare quando si vuole caricare un subset di proprietà dell'entità, ma non si è certi che l'entità esista già. Se l'entità esiste, questa chiamata aggiorna le proprietà incluse nell'operazione **Upsert** e lascia inalterate tutte le proprietà esistenti; se l'entità non esiste, ne inserisce una nuova. La procedura è analoga all'uso della proiezione in una query perché è necessario caricare solo le proprietà modificate.
* **InsertOrReplace**: da usare quando si vuole caricare un'entità completamente nuova, ma non si è certi se l'entità esista già. Va usato solo se si è certi che l'entità appena caricata è corretta perché questa sovrascrive completamente l'entità esistente. Ad esempio, si vuole aggiornare l'entità in cui è archiviata la posizione corrente di un utente indipendentemente dal fatto che l'applicazione abbia già archiviato dei dati sulla posizione dell'utente. La nuova entità di posizione è completa e non occorrono altre informazioni da entità esistenti.

##### <a name="a-namesubheading37astoring-data-series-in-a-single-entity"></a><a name="subheading37"></a>Archiviazione di serie di dati in una singola entità
A volte un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente: ad esempio, un'applicazione può tenere traccia dell'utilizzo della CPU nel tempo per tracciare un grafico in sequenza dei dati relativo alle ultime 24 ore. Un approccio prevede un'entità di tabella all'ora, in cui ogni entità rappresenta un'ora specifica e archivia l'utilizzo della CPU per quell'ora. Per tracciare questi dati, l'applicazione deve recuperare le entità che comprendono i dati delle ultime 24 ore.  

In alternativa, l'applicazione può archiviare l'utilizzo della CPU per ciascuna ora sotto forma di proprietà separata di una singola entità: per aggiornare le singole ore, l'applicazione può usare una singola chiamata **InsertOrMerge Upsert** per aggiornare il valore per l'ora più recente. Per tracciare i dati, l'applicazione deve recuperare solo una singola entità invece di 24, aumentando l'efficacia della query (vedere la discussione precedente sull' [ambito delle query](#subheading30)).

##### <a name="a-namesubheading38astoring-structured-data-in-blobs"></a><a name="subheading38"></a>Archiviazione di dati strutturati in BLOB
A volte sembra che i dati strutturati debbano essere inseriti nelle tabelle, tuttavia gli intervalli delle entità vengono sempre recuperati insieme e possono essere inseriti in batch.  Per spiegarlo efficacemente, si prenda l'esempio di un file di log.  In questo caso è possibile creare in batch e inserire diversi minuti di log. Questi minuti di log vengono anche recuperati tutti in una volta.  In questo caso, per le prestazioni, si consiglia di usare i BLOB invece delle tabelle perché consentono di ridurre significativamente il numero di oggetti scritti/restituiti e, di solito, anche il numero di richieste che occorre effettuare.  

## <a name="queues"></a>Queues
Oltre alle procedure comprovate per [tutti i servizi](#allservices) descritte prima, le procedure comprovate seguenti si applicano specificamente al servizio di accodamento.  

### <a name="a-namesubheading39ascalability-limits"></a><a name="subheading39"></a>Limiti di scalabilità
Una singola coda può elaborare circa 2000 messaggi (da 1 KB ciascuno) al secondo (in questo caso, i metodi AddMessage, GetMessage e DeleteMessage vengono considerati come singoli messaggi). Se questo non è sufficiente per l'applicazione, è consigliabile usare più code e distribuire i messaggi fra di esse.  

È possibile visualizzare gli obiettivi di scalabilità correnti nella pagina [Obiettivi di scalabilità e prestazioni di archiviazione Azure](storage-scalability-targets.md).  

### <a name="a-namesubheading40anagle-off"></a><a name="subheading40"></a>Disattivazione di Nagle
Vedere la sezione nella configurazione della tabella che descrive l'algoritmo Nagle. Questo algoritmo in genere ha un effetto negativo sulle prestazioni delle richieste relative alle code ed è opportuno disabilitarlo.  

### <a name="a-namesubheading41amessage-size"></a><a name="subheading41"></a>Dimensioni del messaggio
Le prestazioni e la scalabilità della coda diminuiscono con l'aumentare delle dimensioni del messaggio. In un messaggio è consigliabile inserire solo le informazioni richieste dal ricevitore.  

### <a name="a-namesubheading42abatch-retrieval"></a><a name="subheading42"></a>Recupero in batch
È possibile recuperare fino a 32 messaggi da una coda in una singola operazione. Questo può ridurre il numero di round trip dall'applicazione client, particolarmente utile per gli ambienti con latenza elevata, come i dispositivi mobili.  

### <a name="a-namesubheading43aqueue-polling-interval"></a><a name="subheading43"></a>Intervallo di polling della coda
La maggior parte delle applicazioni esegue il polling dei messaggi da una coda, che può costituire una delle principali origini di transazioni per l'applicazione. L'intervallo di polling deve essere scelto con attenzione: se si esegue il polling con eccessiva frequenza, l'applicazione potrebbe avvicinarsi all'obiettivo di scalabilità per la coda. Tuttavia, al prezzo di 0,01 USD per 200.000 transazioni (al momento della stesura di questo documento), il costo del polling eseguito da un singolo processore una volta al secondo per un intero mese non supererebbe 15 centesimi. Il costo non è dunque un fattore determinante nella scelta dell'intervallo di polling.  

Per informazioni aggiornate sui costi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="a-namesubheading44aupdatemessage"></a><a name="subheading44"></a>UpdateMessage
È possibile usare **UpdateMessage** per aumentare il timeout di invisibilità o aggiornare le informazioni di stato di un messaggio. Si tratta di una funzionalità potente, ma occorre ricordare che ogni operazione **UpdateMessage** viene presa in considerazione per il calcolo dell'obiettivo di scalabilità. Tuttavia, può essere un approccio più efficace rispetto al passaggio di un processo da una coda alla successiva mediante un flusso di lavoro man mano che i singoli passaggi del processo vengono completati. Con l'operazione **UpdateMessage** , l'applicazione può salvare lo stato del processo nel messaggio e continuare il lavoro invece di riaccodare il messaggio per il passaggio successivo del processo ogni volta che viene completato un passaggio.  

Per ulteriori informazioni, vedere l'articolo [Procedura: modificare il contenuto di un messaggio in coda](storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="a-namesubheading45aapplication-architecture"></a><a name="subheading45"></a>Architettura dell'applicazione
Usare le code per rendere scalabile l'architettura dell'applicazione. Di seguito vengono elencati alcuni modi in cui le code possono essere usate per rendere più scalabile l'applicazione:  

* Le code possono essere usate per creare backlog di lavoro per l'elaborazione e il contenimento dei carichi di lavoro nell'applicazione. Ad esempio, è possibile accodare le richieste degli utenti per eseguire un lavoro che prevede un utilizzo intensivo del processore, ad esempio il ridimensionamento delle immagini caricate.
* È possibile usare le code per separare i componenti dell'applicazione per eseguirne la scalabilità in modo indipendente. Ad esempio, un front-end Web può inserire i risultati di un sondaggio degli utenti in una coda per l'analisi e l'archiviazione successive. È possibile aggiungere più istanze del ruolo di lavoro per elaborare i dati della coda come richiesto.  

## <a name="conclusion"></a>Conclusione
In questo articolo sono state descritte alcune delle procedure comprovate più comuni per l'ottimizzazione delle prestazioni durante l'uso d Archiviazione di Azure. Si consiglia a tutti gli sviluppatori di applicazioni di valutare la propria applicazione in base alle procedure descritte sopra e di prendere in considerazione l'attuazione di alcune misure per migliorare le prestazioni delle applicazioni che usano Archiviazione di Azure.


<!--HONumber=Nov16_HO3-->


