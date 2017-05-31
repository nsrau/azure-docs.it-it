---
title: Livelli di servizio nel database di Azure per MySQL | Microsoft Docs
description: Livelli di servizio nel database di Azure per MySQL
services: mysql
author: v-chenyh
manager: jhubbard
editor: jasonh
ms.service: mysql-database
ms.topic: article
ms.date: 05/16/2017
ms.author: v-chenyh
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 9ae42c9b151c53a1f57d6856bc29cd7f71a7f9be
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opzioni e prestazioni disponibili per il database per SQL di Azure: Identificare gli elementi disponibili in ogni livello di servizio
Il database di Azure per MySQL offre i livelli di servizio Basic e Standard. Il livello Premium non è ancora disponibile.

Ogni servizio offre diversi livelli di prestazioni per la gestione di tipi di carichi di lavoro differenti. Livelli di prestazioni più elevati offrono risorse aggiuntive progettate per garantire un aumento della velocità effettiva. È possibile modificare i livelli di prestazioni all'interno di un livello di servizio in modo dinamico, senza alcun tempo di inattività dell'applicazione.

In futuro sarà possibile effettuare l'upgrade o il downgrade da un livello di servizio a un altro. 

> [!IMPORTANT]
> Il servizio è attualmente in anteprima pubblica e pertanto non assicura alcun Contratto di servizio (SLA).

È possibile creare database di Azure singoli con risorse dedicate in un livello di servizio, con un livello di prestazioni specifico. È possibile creare anche uno o più database nel server in cui le risorse sono condivise tra più database. Le risorse disponibili per un singolo database di Azure per MySQL sono espresse in termini di unità di calcolo e di archiviazione. Per altre informazioni sulle unità di calcolo e di archiviazione, vedere [Spiegazione dell'unità di calcolo e dell'unità di archiviazione](concepts-compute-unit-and-storage.md)

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio

Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| :----------- | :----------------|
| Basic | Adatto in particolare a piccoli carichi di lavoro che richiedono livelli di calcolo e archiviazione scalabili senza garanzia di IOPS. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Standard | Opzione ideale per le applicazioni cloud che necessitano di IOPS garantite con la capacità di scalare a un livello di calcolo e archiviazione più elevato in modo indipendente, per una velocità effettiva elevata. Tra gli esempi sono incluse le applicazioni analitiche e Web. |
| Premium | Soluzione ottimale per carichi di lavoro che richiedono latenze molto brevi per transazioni e IO, assieme a un IO e a una velocità effettiva dei carichi di lavoro elevati. Fornisce il supporto migliore per più utenti simultanei. Applicabile ai database che supportano applicazioni mission-critical.<br />Il livello di servizio Premium non è disponibile in anteprima. |


Per stabilire un livello di servizio, iniziare innanzitutto determinando se il proprio carico di lavoro necessita di IOPS garantite. Determinare quindi le funzionalità minime necessarie:

| **Funzionalità del livello di servizio** | **Basic** | **Standard** | **Premium** * |
| :------------------------ | :-------- | :----------- | :------------ |
| Unità di calcolo massime | 100 | 2.000 | Non disponibile in anteprima |
| Archiviazione massima totale | 1.050 GB | 10.000 GB | Non disponibile in anteprima |
| IOPS di archiviazione garantite | N/D  | Sì | Non disponibile in anteprima |
| Numero massimo di IOPS di archiviazione | N/D  | 3,000 | Non disponibile in anteprima |
| Periodo di conservazione dei backup dei database | 7 giorni | 35 giorni | 35 giorni |

> [!NOTE]
> Il livello di servizio Standard in anteprima supporta attualmente fino a 800 unità di calcolo e un massimo di 1000 GB di risorse di archiviazione.

Dopo avere determinato il livello di servizio minimo è possibile determinare il livello di prestazioni del database di Azure per il server MySQL, ovvero il numero di unità di calcolo. Le unità di calcolo 200 e 400 standard sono spesso un buon punto di partenza per le applicazioni che richiedono maggiore concorrenza degli utenti per i propri carichi di lavoro analitici o Web. 

Tuttavia, è possibile aumentare o ridurre le unità di calcolo indipendentemente dalle unità di archiviazione, in base ai requisiti del carico di lavoro. Se il carico di lavoro richiede una modifica delle risorse di calcolo, è possibile incrementarle o diminuirle dinamicamente. Se il carico di lavoro necessita di altre IOPS o di risorse di archiviazione maggiori, è possibile scalare anche queste.

> [!NOTE]
> In anteprima, i livelli Basic e Standard attualmente non supportano il ridimensionamento dinamico delle risorse di archiviazione. Si prevede di aggiungere questa funzionalità in futuro.

> [!NOTE]
> Al livello di servizio Standard, le IOPS scalano in modo proporzionale alle dimensioni delle risorse di archiviazione predisposte con un rapporto fisso di 3:1. Le risorse di archiviazione incluse di 125 GB garantiscono il provisioning di 375 IOPS, ognuna con una dimensione I/O massima di 256 KB. Eseguendo il provisioning di 1.000 GB, si ottengono 3.000 operazioni di I/O al secondo predisposte. È necessario monitorare il consumo di unità di calcolo del server e aumentarle per sfruttare appieno le IOPS predisposte.

## <a name="service-tiers-and-performance-levels"></a>Livelli di servizio e livelli di prestazioni

Il database di Azure per MySQL offre più livelli di prestazioni all'interno di ogni livello di servizio. È possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro scegliendo una delle opzioni seguenti:
- [Portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Interfaccia della riga di comando di Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Indipendentemente dal numero di database ospitati in ogni server MySQL, il database in uso ottiene un set garantito di risorse e le caratteristiche delle prestazioni previste per il server non saranno interessate.

### <a name="basic-service-tier"></a>Livello di servizio Basic:

| **Livello di prestazioni** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Unità di calcolo massime | 50 | 100 |
| Spazio di archiviazione incluso | 50 GB | 50 GB |
| Spazio di archiviazione del server massimo\* | 1.050 GB | 1.050 GB |

\* Lo spazio di archiviazione del server massimo fa riferimento allo spazio di archiviazione massimo predisposto per il server.


### <a name="standard-service-tier"></a>Livello di servizio Standard:

| **Livello di prestazioni** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Unità di calcolo massime | 100 | 200 | 400 | 800 |
| Spazio di archiviazione e IOPS predisposte inclusi | 125 GB<br/> 375 operazioni di I/O al secondo | 125 GB<br/> 375 operazioni di I/O al secondo | 125 GB<br/> 375 operazioni di I/O al secondo | 125 GB<br/> 375 operazioni di I/O al secondo |
| Spazio di archiviazione del server massimo\* | 1 TB | 1 TB | 1 TB | 1 TB |
| Numero massimo di IOPS predisposte nel server | 3.000 operazioni di I/O al secondo | 3.000 operazioni di I/O al secondo | 3.000 operazioni di I/O al secondo | 3.000 operazioni di I/O al secondo |
| Numero massimo di IOPS predisposte nel server per GB | 3 IOPS fisse per GB | 3 IOPS fisse per GB | 3 IOPS fisse per GB | 3 IOPS fisse per GB |

\* Lo spazio di archiviazione del server massimo fa riferimento allo spazio di archiviazione massimo predisposto per il server.

## <a name="scaling-up-or-down-a-single-server"></a>Ridimensionamento di un singolo server

Dopo avere selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare il server in modo dinamico in base ai requisiti del carico di lavoro. Se è necessario un ridimensionamento, è possibile modificare facilmente il livello del database usando il portale o l'interfaccia della riga di comando di Azure.

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del server prima e dopo la modifica. Ad esempio, un server che cambia unità di calcolo al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard deve essere completato entro pochi minuti. Le nuove proprietà del server non vengono applicate finché l'applicazione delle modifiche non è terminata.

### <a name="documentation-about-the-steps-for-scaling-up-or-down"></a>Documentazione sulle procedure di ridimensionamento
[Monitorare e ridimensionare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure](scripts/sample-scale-server.md)


### <a name="details-about-scaling-up-or-down"></a>Informazioni dettagliate sulla scalabilità verso l'alto o verso il basso

- Per effettuare il downgrade di un server, le dimensioni delle unità di archiviazione del server devono essere inferiori alle dimensioni massime consentite per il livello del servizio di destinazione.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per altre informazioni, vedere [Come eseguire la procedura di backup e ripristino nel database di Azure per il server MySQL usando il portale di Azure](howto-restore-server-portal.md)
- Le nuove proprietà del server non vengono applicate finché l'applicazione delle modifiche non è terminata.

## <a name="next-steps"></a>Passaggi successivi
[Spiegazione dell'unità di calcolo e dell'unità di archiviazione](concepts-compute-unit-and-storage.md)

