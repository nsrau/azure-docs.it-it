---
title: Piani tariffari in Database di Azure per PostgreSQL
description: Piani tariffari in Database di Azure per PostgreSQL
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql-database
ms.topic: article
ms.date: 05/31/2017
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: e141c15b9999a33206285290b9a34c8616bf3791
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Opzioni e prestazioni di Database di Azure per PostgreSQL: funzionalità disponibili in ogni piano tariffario
Quando si crea un'istanza di Database di Azure per il server PostgreSQL, si può scegliere tra tre opzioni principali per configurare le risorse allocate per il server. Queste scelte influenzano le prestazioni e la scalabilità del server.
- Piano tariffario
- Unità di calcolo
- Archiviazione (GB)

Ogni piano tariffario ha diversi livelli di prestazioni, ovvero unità di calcolo, tra cui scegliere, a seconda dei requisiti del carico di lavoro. Livelli di prestazioni più elevati offrono risorse aggiuntive per il server, progettate per garantire una maggiore velocità effettiva. È possibile modificare il livello di prestazioni del server all'interno di un piano tariffario praticamente senza tempi di inattività dell'applicazione.

> [!IMPORTANT]
> Mentre il servizio è in anteprima pubblica, non c'è alcun contratto di servizio, SLA, garantito.

In un'istanza di Database di Azure per il server PostgreSQL è possibile avere uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. 

## <a name="choose-a-pricing-tier"></a>Scegliere un piano tariffario
Nell'anteprima, Database di Azure per PostgreSQL offre due piani tariffari: Basic e Standard. Il piano Premium non è ancora disponibile, ma lo sarà presto. 

La tabella seguente elenca esempi dei piani tariffari ottimali per i diversi carichi di lavoro delle applicazioni.

| Piano tariffario | Carichi di lavoro di destinazione |
| :----------- | :----------------|
| Basic | Adatto in particolare a piccoli carichi di lavoro che richiedono livelli di calcolo e archiviazione scalabili senza garanzia di IOPS. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Standard | Opzione ideale per le applicazioni cloud che necessitano di operazioni di I/O al secondo garantite con velocità effettiva elevata. Tra gli esempi sono incluse le applicazioni analitiche e Web. |
| Premium | Soluzione ottimale per i carichi di lavoro che richiedono una bassa latenza per transazioni e I/O. Fornisce il supporto migliore per più utenti simultanei. Applicabile ai database che supportano applicazioni di importanza strategica.<br />Il piano tariffario Premium non è disponibile in anteprima. |

Per scegliere il piano tariffario, iniziare determinando se il proprio carico di lavoro necessita di operazioni di I/O al secondo garantite. In caso affermativo, scegliere il piano tariffario Standard.

| **Caratteristiche del piano tariffario** | **Basic** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Unità di calcolo massime | 100 | 800 | 
| Archiviazione massima totale | 1 TB | 1 TB | 
| IOPS di archiviazione garantite | N/D  | Sì | 
| Archiviazione IOPS massima | N/D  | 3,000 | 
| Periodo di conservazione dei backup dei database | 7 giorni | 35 giorni | 

Durante il periodo di anteprima non è possibile modificare il piano tariffario dopo aver creato il server. In futuro sarà possibile effettuare l'upgrade o il downgrade di un server da un piano tariffario a un altro.

## <a name="understand-the-price"></a>Informazioni sul prezzo
Quando si crea un nuovo database di Azure per PostgreSQL all'interno del [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer), fare clic sul pannello **Piano tariffario** per visualizzare il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, usare il calcolatore dei prezzi di Azure per ottenere una stima di prezzo. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), quindi fare clic su **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per PostgreSQL** per personalizzare le opzioni.

## <a name="choose-a-performance-level-compute-units"></a>Scegliere un livello di prestazioni, ovvero le unità di calcolo
Dopo avere determinato il piano tariffario per l'istanza di Database di Azure per il server PostgreSQL è possibile stabilire il livello di prestazioni selezionando il numero di unità di calcolo necessarie. Si può iniziare con 200 e 400 unità di calcolo per le applicazioni che richiedono maggiore concorrenza degli utenti per i propri carichi di lavoro analitici o Web, quindi aumentare il valore in base alle esigenze. 

Le unità sono una misura della velocità effettiva di elaborazione della CPU garantita come disponibile per un singolo database di Azure per il server PostgreSQL. Un'unità di calcolo è una misura combinata di risorse di CPU e memoria.  Per altre informazioni, vedere [Spiegazione delle unità di calcolo](concepts-compute-unit-and-storage.md)

### <a name="basic-pricing-tier-performance-levels"></a>Livelli di prestazioni del piano tariffario Basic:

| **Livello di prestazioni** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Unità di calcolo massime | 50 | 100 |
| Spazio di archiviazione incluso | 50 GB | 50 GB |
| Spazio di archiviazione del server massimo\* | 1 TB | 1 TB |

### <a name="standard-pricing-tier-performance-levels"></a>Livelli di prestazioni del piano tariffario Standard:

| **Livello di prestazioni** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Unità di calcolo massime | 100 | 200 | 400 | 800 |
| Spazio di archiviazione e IOPS predisposte inclusi | 125 GB<br/> 375 operazioni di I/O al secondo | 125 GB<br/> 375 operazioni di I/O al secondo | 125 GB<br/> 375 operazioni di I/O al secondo | 125 GB<br/> 375 operazioni di I/O al secondo |
| Spazio di archiviazione del server massimo\* | 1 TB | 1 TB | 1 TB | 1 TB |
| Numero massimo di IOPS predisposte nel server | 3.000 operazioni di I/O al secondo | 3.000 operazioni di I/O al secondo | 3.000 operazioni di I/O al secondo | 3.000 operazioni di I/O al secondo |
| Numero massimo di IOPS predisposte nel server per GB | 3 IOPS fisse per GB | 3 IOPS fisse per GB | 3 IOPS fisse per GB | 3 IOPS fisse per GB |

\* Lo spazio di archiviazione del server massimo fa riferimento allo spazio di archiviazione massimo predisposto per il server.

## <a name="storage"></a>Archiviazione 
La configurazione dell'archiviazione definisce la quantità di capacità di archiviazione disponibile per un'istanza di Database di Azure per il server PostgreSQL. La memoria usata dal servizio include i file del database, i log di transazione e i log del server PostgreSQL. Considerare le dimensioni di archiviazione necessarie per ospitare i database e i requisiti delle prestazioni, ovvero le operazioni di I/O al secondo, quando si seleziona la configurazione dell'archiviazione.

Ogni piano tariffario include una capacità di archiviazione minima, come riportato nella tabella precedente in "Spazio di archiviazione incluso". È possibile aggiungere spazio di archiviazione quando si crea il server, in incrementi di 125 GB, fino a raggiungere lo spazio di archiviazione massimo consentito. La capacità di archiviazione aggiuntiva può essere configurata separatamente dalle unità di calcolo. Il prezzo può variare in base alla quantità di spazio di archiviazione selezionato.

La configurazione delle operazioni di I/O al secondo in ogni livello di prestazioni è correlata al piano tariffario e allo spazio archiviazione scelti. Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nel piano tariffario Standard, le operazioni di I/O al secondo variano proporzionalmente allo spazio di archiviazione massimo in un rapporto 3:1 fisso. Le risorse di archiviazione incluse di 125 GB garantiscono il provisioning di 375 IOPS, ognuna con una dimensione I/O massima di 256 KB. È possibile scegliere di aggiungere altro spazio di archiviazione fino a un massimo di 1 TB, per garantire il provisioning di 3.000 operazioni di I/O al secondo.

Monitorare il grafico delle metriche nel portale di Azure o scrivere comandi dell'interfaccia della riga di comando di Azure per misurare l'uso dello spazio di archiviazione e delle operazioni di I/O al secondo. Le metriche pertinenti al monitoraggio sono il limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di IO.

>[!IMPORTANT]
> In anteprima scegliere lo spazio di archiviazione al momento della creazione del server. La modifica delle dimensioni di archiviazione in un server esistente non è ancora supportata. 

## <a name="scaling-a-server-up-or-down"></a>Ridimensionamento di un server
Al momento della creazione dell'istanza di Database di Azure per PostgreSQL, è possibile scegliere il livello di prestazioni e il piano tariffario. In seguito è possibile aumentare o ridurre le unità di calcolo in modo dinamico, nell'ambito dello stesso piano tariffario. Nel portale di Azure, selezionare le unità di calcolo con il dispositivo di scorrimento nel pannello del piano tariffario del server o creare degli script seguendo questo esempio: [Monitorare e ridimensionare un singolo server PostgreSQL tramite l'interfaccia della riga di comando di Azure](scripts/sample-scale-server-up-or-down.md)

Il ridimensionamento delle unità di calcolo viene eseguito indipendentemente dalle dimensioni massime dello spazio di archiviazione scelto.

In background, la modifica del livello di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Non si perdono dati durante questo processo. Durante il breve intervallo nel quale si passa alla replica, le connessioni al database sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.

La durata dell'intero processo di ridimensionamento dipende dalle dimensioni e dal piano tariffario del server prima e dopo la modifica. Ad esempio, la modifica delle unità di calcolo di un server nell'ambito del piano tariffario Standard verrà completata in pochi minuti. Le nuove proprietà del server non vengono applicate finché l'applicazione delle modifiche non è terminata.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle unità di calcolo, vedere [Spiegazione delle unità di calcolo](concepts-compute-unit-and-storage.md)
- Informazioni su come [monitorare e ridimensionare un singolo server PostgreSQL tramite l'interfaccia della riga di comando di Azure](scripts/sample-scale-server-up-or-down.md)

