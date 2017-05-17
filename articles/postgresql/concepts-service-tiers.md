---
title: Livelli di servizio nel database di Azure per PostgreSQL | Microsoft Docs
description: Livelli di servizio nel database di Azure per PostgreSQL
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 23a93060697bc48084658bbbd895d13fd393b07b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-service-tier"></a>Il database di Azure per le prestazioni e le opzioni di PostgreSQL: comprendere ciò che è disponibile in ogni livello di servizio

In anteprima, il database di Azure per PostgreSQL offre solo i livelli di servizio Basic e Standard. Il livello Premium non è ancora disponibile.

Ogni servizio offre diversi livelli di prestazioni per la gestione di tipi di carichi di lavoro differenti. Livelli di prestazioni più elevati offrono risorse aggiuntive progettate per garantire un aumento della velocità effettiva. È possibile modificare i livelli di prestazioni all'interno di un livello di servizio in modo dinamico, senza alcun tempo di inattività dell'applicazione.

In futuro sarà possibile effettuare l'upgrade o il downgrade da un livello di servizio a un altro.

> [!IMPORTANT]
> Il servizio è attualmente in anteprima pubblica e pertanto non assicura alcun Contratto di servizio (SLA).

È possibile creare server PostgreSQL singoli con risorse dedicate in un livello di servizio, con un livello di prestazioni specifico. È possibile creare anche uno o più database nel server in cui le risorse sono condivise tra più database. Le risorse disponibili per un singolo server PostgreSQL sono espresse in termini di unità di calcolo e di archiviazione. Per altre informazioni sulle unità di calcolo e di archiviazione, vedere [Spiegazione dell'unità di calcolo e dell'unità di archiviazione](concepts-compute-unit-and-storage.md)

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio

Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| :----------- | :----------------|
| Basic | Adatto in particolare a piccoli carichi di lavoro che richiedono livelli di calcolo e archiviazione scalabili senza garanzia di IOPS. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Standard | Opzione ideale per le applicazioni cloud che necessitano di IOPS garantite con la capacità di scalare a un livello di calcolo e archiviazione più elevato in modo indipendente, per una velocità effettiva elevata. Tra gli esempi sono incluse le applicazioni analitiche e Web. |
| Premium | Soluzione ottimale per carichi di lavoro che richiedono latenze molto brevi per transazioni e IO, assieme a un IO e a una velocità effettiva dei carichi di lavoro elevati. Fornisce il supporto migliore per più utenti simultanei. Applicabile ai database che supportano applicazioni mission-critical.<br />Il livello di servizio Premium non è disponibile in anteprima. |
| &nbsp; | &nbsp; |

Per stabilire un livello di servizio, iniziare innanzitutto determinando se il proprio carico di lavoro necessita di IOPS garantite. Determinare quindi le funzionalità minime necessarie:

| **Funzionalità del livello di servizio** | **Basic** | **Standard** | **Premium** * |
| :------------------------ | :-------- | :----------- | :------------ |
| Unità di calcolo massime | 100 | 2000 | Non disponibile in anteprima |
| Archiviazione massima totale | 1050 GB | 10.000 GB | Non disponibile in anteprima |
| IOPS di archiviazione garantite | N/D  | Sì | Non disponibile in anteprima |
| Numero massimo di IOPS di archiviazione | N/D  | 30.000 | Non disponibile in anteprima |
| Periodo di conservazione dei backup dei database | 7 giorni | 35 giorni | 35 giorni |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; |

> [!NOTE]
> Il livello di servizio Standard in anteprima supporta attualmente fino a 800 unità di calcolo e un massimo di 1000 GB di risorse di archiviazione.

Dopo avere determinato il livello di servizio minimo è possibile determinare il livello di prestazioni del server PostgreSQL, ovvero il numero di unità di calcolo. Le unità di calcolo 200 e 400 standard sono spesso un buon punto di partenza per le applicazioni che richiedono maggiore concorrenza degli utenti per i propri carichi di lavoro analitici o Web. 

Tuttavia, è possibile aumentare o ridurre le unità di calcolo indipendentemente dalle unità di archiviazione, in base ai requisiti del carico di lavoro. Se il carico di lavoro richiede una modifica delle risorse di calcolo, è possibile incrementarle o diminuirle dinamicamente. Se il carico di lavoro necessita di altre IOPS o di risorse di archiviazione maggiori, è possibile scalare anche queste.

> [!NOTE]
> In anteprima, i livelli Basic e Standard attualmente non supportano il ridimensionamento dinamico delle risorse di archiviazione. Si prevede di aggiungere questa funzionalità in futuro.

> [!NOTE]
> Al livello di servizio Standard, le IOPS scalano in modo proporzionale alle dimensioni delle risorse di archiviazione predisposte con un rapporto fisso di 3:1. Le risorse di archiviazione incluse di 125 GB garantiscono il provisioning di 375 IOPS, ognuna con una dimensione I/O massima di 256 KB. Eseguendo il provisioning di 1000 GB, si otterranno 3000 IOPS predisposte. È necessario monitorare il consumo di unità di calcolo del server e aumentarle per sfruttare appieno le IOPS predisposte.

## <a name="service-tiers-and-performance-levels"></a>Livelli di servizio e livelli di prestazioni

Il database di Azure per PostgreSQL offre più livelli di prestazioni all'interno di ogni livello di servizio. È possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro scegliendo una delle opzioni seguenti:

- [Portale di Azure](/azure/azure-portal-overview), accessibile all'indirizzo [http://portal.azure.com](http://portal.azure.com)
- [Interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

Indipendentemente dal numero di database ospitati in ogni server PostgreSQL, il database in uso ottiene un set garantito di risorse e le caratteristiche delle prestazioni previste per il server non saranno interessate.

Livello di servizio Basic:

| **Livello di prestazioni** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Unità di calcolo massime | 50 | 100 |
| Spazio di archiviazione incluso | 50 GB | 50 GB |
| Spazio di archiviazione del server massimo\* | 1050 GB | 1050 GB |
| Numero massimo di accessi simultanei | &nbsp; | &nbsp; |
| Numero massimo di connessioni | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; |

Livello di servizio Standard:

| **Livello di prestazioni** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Unità di calcolo massime | 100 | 200 | 400 | 800 |
| Spazio di archiviazione e IOPS predisposte inclusi | 125 GB, 375 IOPS | &nbsp; | &nbsp; | &nbsp; |
| Spazio di archiviazione del server massimo\* | 1 TB | &nbsp; | &nbsp; | &nbsp; |
| Numero massimo di IOPS predisposte nel server | 3000 IOPS | &nbsp; | &nbsp; | &nbsp; |
| Numero massimo di IOPS predisposte nel server per GB | 3 IOPS fisse per GB | &nbsp; | &nbsp; | &nbsp; |
| Numero massimo di accessi simultanei | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| Numero massimo di connessioni | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |

\* Lo spazio di archiviazione del server massimo fa riferimento allo spazio di archiviazione massimo predisposto per il server.

## <a name="scaling-up-or-down-a-single-server"></a>Ridimensionamento di un singolo server

Dopo avere selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare il server in modo dinamico in base ai requisiti del carico di lavoro. Se è necessario un ridimensionamento, è possibile modificare facilmente il livello del database usando il portale o l'interfaccia della riga di comando di Azure.

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del server prima e dopo la modifica. Ad esempio, un server che cambia unità di calcolo al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard deve essere completato entro pochi minuti. Le nuove proprietà del server non vengono applicate finché l'applicazione delle modifiche non è terminata.

### <a name="documentation-about-the-steps-for-scaling-up-or-down"></a>Documentazione sulle procedure di ridimensionamento

- [Manage a single server in the Azure portal](quickstart-create-server-database-portal.md) (Gestire un server singolo nel portale di Azure)
- [Manage a single database with Azure CLI](quickstart-create-server-database-azure-cli.md) (Gestire un database singolo con l'interfaccia della riga di comando di Azure)

### <a name="details-about-scaling-up-or-down"></a>Informazioni dettagliate sulla scalabilità verso l'alto o verso il basso

- Per effettuare il downgrade di un server, le dimensioni delle unità di archiviazione del server devono essere inferiori alle dimensioni massime consentite per il livello del servizio di destinazione.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per altre informazioni, vedere [Come eseguire la procedura di backup e ripristino nel database di Azure per PostgreSQL usando il portale di Azure](howto-restore-server-portal.md)
- Le nuove proprietà del server non vengono applicate finché l'applicazione delle modifiche non è terminata.

