---
title: Piani tariffari - Database di Azure per MySQL
description: Informazioni sui vari piani tariffari per il database di Azure per MySQL, incluse le generazioni di elaborazione, i tipi di archiviazione, le dimensioni di archiviazione, i valori vCore, la memoria e i periodi di conservazione dei backup.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: cf959112a2a717da1005be062dda3b83163d8d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269445"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Piani tariffari di Database di Azure per MySQL

È possibile creare un Database di Azure per il server MySQL in uno dei tre piani tariffari disponibili: Basic, Utilizzo generico e Con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server MySQL. Un server può avere uno o più database.

|    | **Base** | **Scopo generale** | **Ottimizzazione della memoria** |
|:---|:----------|:--------------------|:---------------------|
| Generazione di calcolo | Generazione 4, Generazione 5 | Generazione 4, Generazione 5 | Generazione 5 |
| vCore | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memoria per vCore | 2 GB | 5 GB | 10 GB |
| Dimensioni dello spazio di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 16 TB | Da 5 GB a 16 TB |
| Periodo di conservazione dei backup dei database | Da 7 a 35 giorni | Da 7 a 35 giorni | Da 7 a 35 giorni |

Per scegliere un piano tariffario, usare la tabella seguente come punto di partenza.

| Piano tariffario | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Basic | Carichi di lavoro con esigenze di calcolo e di prestazioni I/O ridotte. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Ad esempio, server per l'hosting di app Web e di app per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Ad esempio, server per l'elaborazione di dati in tempo reale e app transazionali o analitiche a prestazioni elevate.|

Dopo aver creato un server, il numero di vCore, la generazione dell'hardware e il piano tariffario (ad eccezione del passaggio da/a Basic) possono essere aumentati o ridotti in pochi secondi. È anche possibile aumentare autonomamente lo spazio di archiviazione e aumentare o ridurre il periodo di conservazione dei backup senza tempi di inattività per le applicazioni. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Per altre informazioni, vedere la sezione [Ridimensionare le risorse.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Generazioni di calcolo e vCore

Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Cina orientale 1, Cina settentrionale 1, US DoD Central, e US DoD East utilizzano Gen 4 CPU logiche basate su intel E5-2673 v3 (Haswell) processori da 2,4 GHz. Tutte le altre aree utilizzano CPU logiche Gen 5 basate su processori Intel E5-2673 v4 (Broadwell) da 2,3 GHz.

## <a name="storage"></a>Archiviazione

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per MySQL. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server MySQL. Lo spazio di archiviazione totale di cui si effettua il provisioning definisce anche la capacità di I/O disponibile per il server.

|    | **Base** | **Scopo generale** | **Ottimizzazione della memoria** |
|:---|:----------|:--------------------|:---------------------|
| Tipo di archiviazione | Archiviazione di base | Archiviazione per uso generale | Archiviazione per uso generale |
| Dimensioni dello spazio di archiviazione | Da 5 GB a 1 TB | Da 5 GB a 16 TB | Da 5 GB a 16 TB |
| Dimensioni di incremento dell'archiviazione | 1 GB | 1 GB | 1 GB |
| IOPS | Variabile |3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 20.000 IOPS | 3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 20.000 IOPS |

> [!NOTE]
> Lo storage fino a 16 TB e 20.000 IOPS è supportato nelle seguenti regioni: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti occidentali, Stati Uniti centro-settentrionali, Stati Uniti centro-settentrionali, Nord Europa, Europa occidentale, Regno Unito meridionale, Ovest, Sud-est asiatico, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale , Corea del Sud, Australia Est, Australia Sud Est.
>
> Tutte le altre aree supportano fino a 4 TB di spazio di archiviazione e fino a 6000 IOPS.
>

È possibile aggiungere ulteriore capacità di archiviazione durante e dopo la creazione del server e consentire al sistema di aumentare automaticamente l'archiviazione in base all'utilizzo dello spazio di lavoro. 

>[!NOTE]
> Lo storage può essere aumentato solo, non verso il basso.

Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria, la scalabilità delle operazioni di I/O al secondo rispetto allo spazio di archiviazione sottoposto a provisioning è in un rapporto di 3 a 1.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche rilevanti da monitorare sono il limite di archiviazione, la [percentuale di archiviazione, lo spazio di archiviazione utilizzato e](concepts-monitoring.md)la percentuale di I/O .

### <a name="reaching-the-storage-limit"></a>Raggiungimento del limite di archiviazione

I server con meno di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning sono contrassegnati come di sola lettura se lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione di cui è stato eseguito il provisioning. I server con più di 100 GB di spazio di archiviazione con provisioning sono contrassegnati come di sola lettura quando lo spazio di archiviazione disponibile è inferiore a 5 GB.

Ad esempio, se è stato eseguito il provisioning di 110 GB di spazio di archiviazione e l'utilizzo effettivo supera i 105 GB, il server viene contrassegnato come di sola lettura. In alternativa, se è stato eseguito il provisioning di 5 GB di spazio di archiviazione, il server viene contrassegnato in sola lettura quando lo spazio di archiviazione disponibile raggiunge meno di 256 MB.

Mentre il servizio tenta di impostare il server come sola lettura, tutte le nuove richieste di transazione di scrittura vengono bloccate e le transazioni attive esistenti continueranno a essere eseguite. Quando il server è impostato su sola lettura, tutte le operazioni di scrittura e i commit delle transazioni successivi avranno esito negativo. Le query in lettura continueranno a funzionare senza interruzioni. Dopo avere aumentato lo spazio di archiviazione sottoposto a provisioning, il server sarà pronto per accettare nuovamente le transazioni in scrittura.

È consigliabile attivare l'aumento automatico dell'archiviazione o impostare un avviso per notificare quando lo spazio di archiviazione del server sta per raggiungere la soglia in modo da evitare di entrare nello stato di sola lettura. Per altre informazioni, vedere la documentazione sulla [procedura di configurazione di un avviso](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Aumento automatico dello storage

L'aumento automatico dello spazio di archiviazione impedisce al server di esaurire l'archiviazione e diventa di sola lettura. Se l'aumento automatico dell'archiviazione è abilitato, l'archiviazione cresce automaticamente senza influire sul carico di lavoro. Per i server con meno di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, la dimensione dello spazio di archiviazione di cui è stato eseguito il provisioning viene aumentata di 5 GB quando lo spazio di archiviazione disponibile è inferiore al 10% dello spazio di archiviazione di cui è stato eseguito il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, la dimensione dello spazio di archiviazione di cui è stato eseguito il provisioning viene aumentata del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB della dimensione di archiviazione di cui è stato eseguito il provisioning. Si applicano i limiti di archiviazione massimi specificati in precedenza.

Ad esempio, se è stato eseguito il provisioning di 1000 GB di spazio di archiviazione e l'utilizzo effettivo supera i 990 GB, la dimensione dello spazio di archiviazione del server viene aumentata a 1050 GB. In alternativa, se è stato eseguito il provisioning di 10 GB di spazio di archiviazione, le dimensioni dello spazio di archiviazione aumentano a 15 GB quando lo spazio di archiviazione è gratuito meno di 1 GB.

Tenere presente che lo spazio di archiviazione può essere aumentato solo, non verso il basso.

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. È possibile selezionare un periodo di conservazione da un intervallo compreso tra 7 e 35 giorni. I server con scopo generale e ottimizzazione della memoria possono scegliere di disporre di un'archiviazione con ridondanza geografica per i backup. Ulteriori informazioni sui backup sono riportati [nell'articolo concetti.](concepts-backup.md)

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il numero di vCore, la generazione dell'hardware, il piano tariffario (ad eccezione del passaggio da/a Basic), lo spazio di archiviazione e il periodo di conservazione dei backup. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Il numero di vCore può essere aumentato o ridotto. Il periodo di conservazione dei backup può essere aumentato o ridotto da 7 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure. Per un esempio di ridimensionamento tramite l'interfaccia della riga di comando di Azure, vedere [Monitorare a scalare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure](scripts/sample-scale-server.md).

Quando si modifica il numero di vCore, la generazione dell'hardware o il piano tariffario, viene creata una copia del server di origine con la nuova allocazione del calcolo. Quando il nuovo server è in esecuzione, le connessioni vengono trasferite al nuovo server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo intervallo è variabile, ma nella maggior parte dei casi è inferiore al minuto.

Il ridimensionamento dello spazio di archiviazione e la modifica del periodo di conservazione dei backup sono realmente operazioni online. Non si registrano tempi di inattività e l'applicazione non viene influenzata. Le operazioni di I/O al secondo vengono ridimensionate in funzione dello spazio di archiviazione sottoposto a provisioning, quindi è possibile aumentare le operazioni di I/O al secondo disponibili per il server aumentando lo spazio di archiviazione.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/). Per informazioni sui costi della configurazione desiderata, consultare la scheda **Piano tariffario** del [portale di Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) che illustra il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), selezionare **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per MySQL** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server MySQL nel portale](howto-create-manage-server-portal.md).
- Informazioni sui [limiti dei servizi](concepts-limits.md).
- Altre informazioni su come [ampliare un livello di servizio con repliche in lettura](howto-read-replicas-portal.md).
