---
title: Piani tariffari nel database di Azure per MySQL
description: Questo articolo descrive i piani tariffari di Database di Azure per MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6bd24da05c337a902ce0e4a2b9acf22a809eb653
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Piani tariffari di Database di Azure per MySQL

Un server Database di Azure per MySQL può essere creato in uno dei tre piani tariffari disponibili: Basic, Utilizzo generico e Con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server MySQL. Un server può avere uno o più database.

|    | **Basic** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Generazione di calcolo | Generazione 4, Generazione 5 | Generazione 4, Generazione 5 | Generazione 5 |
| vCore | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16, 32 |
| Memoria per vCore | 1x | 2x Basic | 2x Utilizzo generico |
| Dimensioni di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 1 TB | Da 5 GB a 1 TB |
| Tipo di archiviazione | Archiviazione Standard di Azure | Archiviazione Premium di Azure | Archiviazione Premium di Azure |
| Periodo di conservazione dei backup dei database | 7 - 35 giorni | 7 - 35 giorni | 7 - 35 giorni |

La tabella seguente può essere usata come punto di partenza per la scelta di un piano tariffario:

| Piano tariffario | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Basic | Carichi di lavoro con esigenze di calcolo e di prestazioni I/O ridotte. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Esempi comprendono server per l'hosting di app Web e per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Esempi includono server per l'elaborazione di dati in tempo reale e app transazionali o analitiche ad alte prestazioni.|

Dopo aver creato un server, il numero di vCore può essere aumentato o ridotto in pochi secondi. È anche possibile aumentare autonomamente lo spazio di archiviazione e aumentare o ridurre il periodo di conservazione dei backup senza tempi di inattività per le applicazioni. Per altre informazioni, vedere la sezione relativa al ridimensionamento più avanti.

## <a name="compute-generations-vcores-and-memory"></a>Generazioni di calcolo, vCore e memoria

Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. È attualmente possibile scegliere tra due generazioni di calcolo, ovvero Generazione 4 e Generazione 5. Le CPU logiche Generazione 4 si basano sui processori Intel E5-2673 v3 (Haswell) a 2,4 GHz. Le CPU logiche Generazione 5 si basano sui processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz.

A seconda del piano tariffario, il provisioning di ogni vCore viene effettuato con una quantità di memoria specifica. Quando si aumenta o diminuisce il numero di vCore per il server, la memoria aumenta o diminuisce proporzionalmente. Il piano Utilizzo generico fornisce il doppio della memoria per ogni vCore rispetto al piano Basic. Il piano Con ottimizzazione per la memoria fornisce il doppio della memoria rispetto al piano Utilizzo generico.

## <a name="storage"></a>Archiviazione

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per MySQL. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server MySQL. Lo spazio di archiviazione totale di cui si esegue il provisioning definisce anche la capacità di I/O disponibile per il server:

|    | **Basic** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Tipo di archiviazione | Archiviazione Standard di Azure | Archiviazione Premium di Azure | Archiviazione Premium di Azure |
| Dimensioni di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 1 TB | Da 5 GB a 1 TB |
| Dimensioni di incremento dell'archiviazione | 1 GB | 1 GB | 1 GB |
| IOPS | Variabile |3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo | 3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo |

È possibile aggiungere capacità di archiviazione durante e dopo la creazione del server. Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria, la scalabilità delle operazioni di I/O al secondo rispetto allo spazio di archiviazione sottoposto a provisioning è in un rapporto di 3 a 1.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche pertinenti al monitoraggio sono il [limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di IO](concepts-monitoring.md).

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. Il periodo di conservazione minimo dei backup è di sette giorni. È possibile impostare un periodo di conservazione massimo di 35 giorni. È possibile modificare il periodo di conservazione in qualsiasi momento durante il ciclo di vita del server. È possibile scegliere tra backup con ridondanza locale e backup con ridondanza geografica. I backup con ridondanza geografica vengono archiviati anche nell'[area geografica abbinata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) all'area in cui è stato creato il server. Ciò consente un livello di protezione in caso di emergenza. È anche possibile ripristinare il server in qualsiasi altra area di Azure in cui sia disponibile il servizio con backup con ridondanza geografica. Si noti che non è possibile cambiare opzione di archiviazione dei backup dopo aver creato il server.

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il numero di vCore, lo spazio di archiviazione e il periodo di conservazione dei backup. Non è possibile modificare il piano tariffario o il tipo di archiviazione dei backup dopo aver creato il server. Il numero di vCore e il periodo di conservazione dei backup possono essere aumentati o ridotti. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure. Un esempio di ridimensionamento tramite l'interfaccia della riga di comando è disponibile [qui](scripts/sample-scale-server.md).

Quando si modifica il numero di vCore, viene creata una copia del server di origine con la nuova allocazione del calcolo. Quando il nuovo server è in esecuzione, le connessioni vengono trasferite al nuovo server. Durante il breve intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo intervallo è variabile, ma nella maggior parte dei casi è inferiore al minuto.

Il ridimensionamento dello spazio di archiviazione e la modifica del periodo di conservazione dei backup sono realmente operazioni online, che non comportano alcun tempo di inattività o impatto per le applicazioni. Le operazioni di I/O al secondo vengono ridimensionate in funzione dello spazio di archiviazione sottoposto a provisioning, quindi è possibile aumentare le operazioni di I/O al secondo disponibili per il server aumentando lo spazio di archiviazione.

## <a name="pricing"></a>Prezzi

Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/) per le informazioni più aggiornate sui prezzi. Il [portale di Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) visualizza il costo mensile della configurazione desiderata nella scheda **Piano tariffario**, in funzione delle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), quindi fare clic su **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per MySQL** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server MySQL nel portale](howto-create-manage-server-portal.md)
- Informazioni su come [monitorare e ridimensionare un server Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](scripts/sample-scale-server.md)
- Informazioni sulle [limitazioni del servizio](concepts-limits.md)
