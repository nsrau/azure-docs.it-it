---
title: Piani tariffari del Database di Azure per MariaDB
description: Questo articolo descrive i piani tariffari di Database di Azure per MariaDB.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb2ba509983918a55370ae0deafd019e03f53d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740285"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Piani tariffari di Database di Azure per MariaDB

È possibile creare un server Database di Azure per MariaDB in uno dei tre piani tariffari disponibili: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server MariaDB. Un server può avere uno o più database.

|    | **Basic** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Generazione di calcolo | Generazione 5 |Generazione 5 | Generazione 5 |
| vCore | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memoria per vCore | 2 GB | 5 GB | 10 GB |
| Dimensioni della risorsa di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 4 TB | Da 5 GB a 4 TB |
| Tipo di archiviazione | Archiviazione Standard di Azure | Archiviazione Premium di Azure | Archiviazione Premium di Azure |
| Periodo di conservazione dei backup dei database | Da 7 a 35 giorni | Da 7 a 35 giorni | Da 7 a 35 giorni |

Per scegliere un piano tariffario, usare la tabella seguente come punto di partenza.

| Piano tariffario | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Basic | Carichi di lavoro con esigenze di calcolo e di prestazioni I/O ridotte. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Ad esempio, server per l'hosting di app Web e di app per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Ad esempio, server per l'elaborazione di dati in tempo reale e app transazionali o analitiche a prestazioni elevate.|

Dopo aver creato un server, il numero di vCore e il piano tariffario (ad eccezione del passaggio da/a Basic) possono essere aumentati o ridotti in pochi secondi. È anche possibile aumentare autonomamente lo spazio di archiviazione e aumentare o ridurre il periodo di conservazione dei backup senza tempi di inattività per le applicazioni. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Per altre informazioni, vedere la sezione [Ridimensionare le risorse](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Generazioni di calcolo e vCore

Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Le CPU logiche Generazione 5 si basano sui processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz.

## <a name="storage"></a>Archiviazione

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per MariaDB. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server MariaDB. Lo spazio di archiviazione totale di cui si effettua il provisioning definisce anche la capacità di I/O disponibile per il server.

|    | **Basic** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Tipo di archiviazione | Archiviazione Standard di Azure | Archiviazione Premium di Azure | Archiviazione Premium di Azure |
| Dimensioni della risorsa di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 4 TB | Da 5 GB a 4 TB |
| Dimensioni di incremento dell'archiviazione | 1 GB | 1 GB | 1 GB |
| IOPS | Variabile |3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 6000 operazioni di I/O al secondo | 3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 6000 operazioni di I/O al secondo |

È possibile aggiungere capacità di archiviazione durante e dopo la creazione del server. Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria, la scalabilità delle operazioni di I/O al secondo rispetto allo spazio di archiviazione sottoposto a provisioning è in un rapporto di 3 a 1.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche pertinenti al monitoraggio sono il [limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di I/O](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Raggiungimento del limite di archiviazione

Il server viene contrassegnato di sola lettura quando lo spazio di archiviazione disponibile diventa inferiore a 5 GB o al 5% dello spazio di archiviazione sottoposto a provisioning, a seconda di quale dei due valori sia inferiore. Ad esempio, se è stato eseguito il provisioning di 100 GB di spazio di archiviazione e l'utilizzo effettivo supera 95 GB, il server viene contrassegnato come di sola lettura. In alternativa, se è stato eseguito il provisioning di 5 GB di spazio di archiviazione, il server viene contrassegnato come sola lettura quando la risorsa di archiviazione disponibile diventa inferiore a 250 MB.  

Mentre il servizio tenta di impostare il server come sola lettura, tutte le nuove richieste di transazione di scrittura vengono bloccate e le transazioni attive esistenti continueranno a essere eseguite. Quando il server è impostato su sola lettura, tutte le operazioni di scrittura e i commit delle transazioni successivi avranno esito negativo. Le query in lettura continueranno a funzionare senza interruzioni. Dopo avere aumentato lo spazio di archiviazione sottoposto a provisioning, il server sarà pronto per accettare nuovamente le transazioni in scrittura.

È consigliabile configurare un avviso per ricevere una notifica quando l'archiviazione server sta per raggiungere la soglia in modo tale da evitare di ottenere lo stato di sola lettura. 

Per altre informazioni, vedere la documentazione sulla [procedura di configurazione di un avviso](howto-alert-metric.md).

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. Il periodo di conservazione minimo dei backup è di sette giorni. È possibile impostare un periodo di conservazione massimo di 35 giorni. È possibile modificare il periodo di conservazione in qualsiasi momento durante il ciclo di vita del server. È possibile scegliere tra backup con ridondanza locale e backup con ridondanza geografica. Anche i backup con ridondanza geografica vengono archiviati nell'[area geografica abbinata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) all'area in cui è stato creato il server. Questa ridondanza consente un livello di protezione in caso di emergenza. È anche possibile ripristinare il server in qualsiasi altra area di Azure in cui sia disponibile il servizio con backup con ridondanza geografica. Non è possibile cambiare opzione di archiviazione dei backup dopo aver creato il server.

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il numero di vCore, il piano tariffario (ad eccezione del passaggio da/a Basic), lo spazio di archiviazione e il periodo di conservazione dei backup. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Il numero di vCore può essere aumentato o ridotto. Il periodo di conservazione dei backup può essere aumentato o ridotto da 7 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Quando si modifica il numero di vCore, o il piano tariffario, viene creata una copia del server di origine con la nuova allocazione del calcolo. Quando il nuovo server è in esecuzione, le connessioni vengono trasferite al nuovo server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo intervallo è variabile, ma nella maggior parte dei casi è inferiore al minuto.

Il ridimensionamento dello spazio di archiviazione e la modifica del periodo di conservazione dei backup sono realmente operazioni online. Non si registrano tempi di inattività e l'applicazione non viene influenzata. Le operazioni di I/O al secondo vengono ridimensionate in funzione dello spazio di archiviazione sottoposto a provisioning, quindi è possibile aumentare le operazioni di I/O al secondo disponibili per il server aumentando lo spazio di archiviazione.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mariadb/). Per informazioni sui costi della configurazione desiderata, consultare la scheda **Piano tariffario** del [portale di Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) che illustra il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), selezionare **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per MariaDB** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [limitazioni del servizio](concepts-limits.md).
- Informazioni su come [creare un server MariaDB nel portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
