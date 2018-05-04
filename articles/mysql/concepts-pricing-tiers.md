---
title: Piani tariffari del Database di Azure per MySQL
description: Questo articolo descrive i piani tariffari di Database di Azure per MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 490b162bcab0656388ef0b211ea693809d446346
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Piani tariffari di Database di Azure per MySQL

È possibile creare un Database di Azure per il server MySQL in uno dei tre piani tariffari disponibili: Basic, Utilizzo generico e Con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server MySQL. Un server può avere uno o più database.

|    | **Basic** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Generazione di calcolo | Generazione 4, Generazione 5 | Generazione 4, Generazione 5 | Generazione 5 |
| vCore | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Memoria per vCore | Di base | 2x Basic | 2x Utilizzo generico |
| Dimensioni della risorsa di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 2 TB | Da 5 GB a 2 TB |
| Tipo di archiviazione | Archiviazione Standard di Azure | Archiviazione Premium di Azure | Archiviazione Premium di Azure |
| Periodo di conservazione dei backup dei database | Da 7 a 35 giorni | Da 7 a 35 giorni | Da 7 a 35 giorni |

Per scegliere un piano tariffario, usare la tabella seguente come punto di partenza.

| Piano tariffario | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Basic | Carichi di lavoro con esigenze di calcolo e di prestazioni I/O ridotte. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Ad esempio, server per l'hosting di app Web e di app per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Ad esempio, server per l'elaborazione di dati in tempo reale e app transazionali o analitiche a prestazioni elevate.|

Dopo aver creato un server, il numero di vCore può essere aumentato o ridotto (all'interno dello stesso piano tariffario) in pochi secondi. È anche possibile aumentare autonomamente lo spazio di archiviazione e aumentare o ridurre il periodo di conservazione dei backup senza tempi di inattività per le applicazioni. Non è possibile modificare il piano tariffario o il tipo di archiviazione dei backup dopo aver creato il server. Per altre informazioni, vedere la sezione [Ridimensionare le risorse](#scale-resources).

## <a name="compute-generations-vcores-and-memory"></a>Generazioni di calcolo, vCore e memoria

Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Attualmente, è possibile scegliere tra due generazioni di calcolo: Generazione 4 e Generazione 5. Le CPU logiche Generazione 4 si basano sui processori Intel E5-2673 v3 (Haswell) a 2,4 GHz. Le CPU logiche Generazione 5 si basano sui processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz. Generazione 4 e Generazione 5 sono disponibili nelle aree seguenti ("X" indica la disponibilità). 

| **Area di Azure** | **Generazione 4** | **Generazione 5** |
|:---|:----------:|:--------------------:|
| Stati Uniti centrali | X |  |
| Stati Uniti orientali | X | X |
| Stati Uniti orientali 2 | X | X |
| Stati Uniti centro-settentrionali | X |  |
| Stati Uniti centro-meridionali | X | X |
| Stati Uniti occidentali | X | X |
| Stati Uniti occidentali 2 |  | X |
| Canada centrale | X | X |
| Canada orientale | X | X |
| Brasile meridionale | X | X |
| Europa settentrionale | X | X |
| Europa occidentale |  | X |
| Regno Unito occidentale |  | X |
| Regno Unito meridionale |  | X |
| Asia orientale | X |  |
| Asia sudorientale | X | X |
| Australia orientale |  | X |
| Australia sudorientale |  | X |
| India centrale | X |  |
| India occidentale | X |  |
| India meridionale |  | X |
| Giappone orientale | X | X |
| Giappone occidentale | X | X |
| Corea meridionale |  | X |

A seconda del piano tariffario, il provisioning di ogni vCore viene effettuato con una quantità di memoria specifica. Quando si aumenta o diminuisce il numero di vCore per il server, la memoria aumenta o diminuisce proporzionalmente. Il piano Utilizzo generico fornisce il doppio della memoria per ogni vCore rispetto al piano Basic. Il piano Con ottimizzazione per la memoria fornisce il doppio della memoria rispetto al piano Utilizzo generico.

## <a name="storage"></a>Archiviazione

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per MySQL. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server MySQL. Lo spazio di archiviazione totale di cui si effettua il provisioning definisce anche la capacità di I/O disponibile per il server.

|    | **Basic** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Tipo di archiviazione | Archiviazione Standard di Azure | Archiviazione Premium di Azure | Archiviazione Premium di Azure |
| Dimensioni della risorsa di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 2 TB | Da 5 GB a 2 TB |
| Dimensioni di incremento dell'archiviazione | 1 GB | 1 GB | 1 GB |
| IOPS | Variabile |3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo | 3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo |

È possibile aggiungere capacità di archiviazione durante e dopo la creazione del server. Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria, la scalabilità delle operazioni di I/O al secondo rispetto allo spazio di archiviazione sottoposto a provisioning è in un rapporto di 3 a 1.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche pertinenti al monitoraggio sono il [limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di I/O](concepts-monitoring.md).

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. Il periodo di conservazione minimo dei backup è di sette giorni. È possibile impostare un periodo di conservazione massimo di 35 giorni. È possibile modificare il periodo di conservazione in qualsiasi momento durante il ciclo di vita del server. È possibile scegliere tra backup con ridondanza locale e backup con ridondanza geografica. Anche i backup con ridondanza geografica vengono archiviati nell'[area geografica abbinata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) all'area in cui è stato creato il server. Questa ridondanza consente un livello di protezione in caso di emergenza. È anche possibile ripristinare il server in qualsiasi altra area di Azure in cui sia disponibile il servizio con backup con ridondanza geografica. Non è possibile cambiare opzione di archiviazione dei backup dopo aver creato il server.

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il numero di vCore, lo spazio di archiviazione e il periodo di conservazione dei backup. Non è possibile modificare il piano tariffario o il tipo di archiviazione dei backup dopo aver creato il server. Il numero di vCore può essere aumentato o ridotto all'interno dello stesso piano tariffario. Il periodo di conservazione dei backup può essere aumentato o ridotto da 7 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure. Per un esempio di ridimensionamento tramite l'interfaccia della riga di comando di Azure, vedere [Monitorare a scalare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure](scripts/sample-scale-server.md).

Quando si modifica il numero di vCore, viene creata una copia del server di origine con la nuova allocazione del calcolo. Quando il nuovo server è in esecuzione, le connessioni vengono trasferite al nuovo server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo intervallo è variabile, ma nella maggior parte dei casi è inferiore al minuto.

Il ridimensionamento dello spazio di archiviazione e la modifica del periodo di conservazione dei backup sono realmente operazioni online. Non si registrano tempi di inattività e l'applicazione non viene influenzata. Le operazioni di I/O al secondo vengono ridimensionate in funzione dello spazio di archiviazione sottoposto a provisioning, quindi è possibile aumentare le operazioni di I/O al secondo disponibili per il server aumentando lo spazio di archiviazione.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/). Per informazioni sui costi della configurazione desiderata, consultare la scheda **Piano tariffario** del [portale di Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) che illustra il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), selezionare **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per MySQL** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server MySQL nel portale](howto-create-manage-server-portal.md).
- Altre informazioni su come [monitorare e scalare un Database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](scripts/sample-scale-server.md).
- Altre informazioni sulle [limitazioni del servizio](concepts-limits.md).
