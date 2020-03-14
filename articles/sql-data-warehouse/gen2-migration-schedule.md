---
title: Eseguire la migrazione del pool SQL a Gen2
description: Istruzioni per la migrazione di un pool SQL esistente a Gen2 e la pianificazione della migrazione in base all'area.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 00180c1791e765240f3f8feac188b9250162408e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256705"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Aggiornare il pool SQL a Gen2

Microsoft contribuisce a ridurre il costo di ingresso per l'esecuzione di un pool SQL.  Per il pool SQL sono ora disponibili livelli di calcolo inferiori in grado di gestire query complesse. Leggere l'annuncio completo [supporto del livello di calcolo più basso per Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nuova offerta è disponibile nelle aree geografiche indicate nella tabella seguente. Per le aree supportate, i pool SQL Gen1 esistenti possono essere aggiornati a Gen2 tramite uno dei seguenti:

- **Processo di aggiornamento automatico:** Gli aggiornamenti automatici non vengono avviati non appena il servizio è disponibile in un'area.  Quando gli aggiornamenti automatici vengono avviati in un'area specifica, vengono eseguiti singoli aggiornamenti di data warehouse durante la pianificazione di manutenzione selezionata.
- [**Aggiornamento automatico a Gen2:** ](#self-upgrade-to-gen2) È possibile controllare quando eseguire l'aggiornamento eseguendo un aggiornamento automatico a Gen2. Se l'area non è ancora supportata, è possibile eseguire il ripristino da un punto di ripristino direttamente in un'istanza di Gen2 in un'area supportata.

## <a name="automated-schedule-and-region-availability-table"></a>Tabella delle pianificazioni automatiche e della disponibilità per area

La tabella seguente indica la data in cui il livello di calcolo inferiore Gen2 sarà disponibile in ogni area e la data di inizio degli aggiornamenti automatici. Le date sono soggette a variazioni. Controllare periodicamente questa pagina per sapere quando la propria area sarà disponibile.

\* indica che una pianificazione specifica per l'area non è attualmente disponibile.

| **Area** | **Disponibilità livello inferiore Gen2** | **Inizio aggiornamenti automatici** |
|:--- |:--- |:--- |
| Canada orientale |1 giugno 2020 |1 ° luglio 2020 |
| Cina orientale |\* |\* |
| Cina settentrionale |\* |\* |
| Germania centrale |\* |\* |
| Germania centro-occidentale |Disponibile |1 maggio 2020 |
| India occidentale |Disponibile |1 maggio 2020  |

## <a name="automatic-upgrade-process"></a>Processo di aggiornamento automatico

In base al grafico di disponibilità precedente, verranno pianificati gli aggiornamenti automatici per le istanze di Gen1. Per evitare interruzioni impreviste della disponibilità del pool SQL, gli aggiornamenti automatici verranno pianificati durante la pianificazione della manutenzione. La possibilità di creare una nuova istanza di Gen1 verrà disabilitata nelle aree in cui viene eseguito l'aggiornamento automatico a Gen2. Gen1 sarà deprecato dopo il completamento degli aggiornamenti automatici. Per altre informazioni sulle pianificazioni, vedere [Visualizzare una pianificazione della manutenzione](viewing-maintenance-schedule.md).

Il processo di aggiornamento comporterà un breve calo della connettività (circa 5 minuti) quando si riavvia il pool SQL.  Una volta che il pool SQL è stato riavviato, sarà completamente disponibile per l'uso. Tuttavia, è possibile che si verifichi un calo delle prestazioni mentre il processo di aggiornamento continua ad aggiornare i file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Si può anche accelerare il processo di aggiornamento dei file di dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie con SLO e classe di risorse più grandi dopo il riavvio.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

## <a name="self-upgrade-to-gen2"></a>Aggiornamento autonomo a Gen2

È possibile scegliere di eseguire l'aggiornamento automatico attenendosi alla procedura seguente in un pool SQL Gen1 esistente. Se si sceglie di eseguire l'aggiornamento automatico, è necessario completarlo prima che il processo di aggiornamento automatico inizi nella propria area. In questo modo si evita il rischio che gli aggiornamenti automatici causino un conflitto.

L'aggiornamento autonomo offre due opzioni.  È possibile aggiornare il pool SQL corrente sul posto oppure ripristinare un pool SQL Gen1 in un'istanza di Gen2.

- [Aggiornamento sul posto](upgrade-to-latest-generation.md) : questa opzione aggiornerà il pool SQL Gen1 esistente a Gen2. Il processo di aggiornamento comporterà un breve calo della connettività (circa 5 minuti) quando si riavvia il pool SQL.  Una volta che il pool SQL è stato riavviato, sarà completamente disponibile per l'uso. Se si verificano problemi durante l'aggiornamento, aprire una [richiesta di supporto](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e fare riferimento all'aggiornamento di Gen2 come possibile.
- [Aggiornamento dal punto di ripristino](sql-data-warehouse-restore.md) : creare un punto di ripristino definito dall'utente nel pool SQL Gen1 corrente e quindi eseguire il ripristino direttamente in un'istanza di Gen2. Il pool SQL Gen1 esistente resterà in vigore. Una volta completato il ripristino, il pool SQL di Gen2 sarà completamente disponibile per l'uso.  Dopo aver eseguito tutti i processi di test e convalida sull'istanza di Gen2 ripristinata, l'istanza di Gen1 originale può essere eliminata.

   - Passaggio 1: dalla portale di Azure [creare un punto di ripristino definito dall'utente](sql-data-warehouse-restore-active-paused-dw.md).
   - Passaggio 2: durante il ripristino da un punto di ripristino definito dall'utente, impostare il "livello di prestazioni" sul livello Gen2 preferito.

Si potrebbe riscontrare un periodo di riduzione delle prestazioni mentre il processo di aggiornamento prosegue l'aggiornamento dei file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Per accelerare il processo di migrazione dei dati in background, è possibile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie su cui si prevede di eseguire query, con SLO e classe di risorse più grandi.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

Se si verificano problemi con il pool SQL, creare una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) e fare riferimento all'aggiornamento di Gen2 come possibile.

Per altre informazioni, vedere [Aggiornamento a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Domande frequenti sulla migrazione

**D: il costo di Gen2 è uguale a quello di Gen1?**

- R: Sì.

**D: in che modo gli aggiornamenti interessano gli script di automazione?**

- R: tutti gli script di automazione che fanno riferimento a un obiettivo del livello di servizio devono essere modificati in modo che corrispondano all'equivalente Gen2.  Vedere i dettagli [qui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**D: quanto tempo richiede normalmente un aggiornamento automatico?**

- R: è possibile eseguire l'aggiornamento sul posto o eseguire l'aggiornamento da un punto di ripristino.  
   - L'aggiornamento sul posto provocherà momentaneamente la sospensione e la ripresa del pool SQL.  Un processo in background continuerà mentre il pool SQL è online.  
   - L'aggiornamento da un punto di ripristino richiede più tempo, in quanto deve essere eseguito l'intero processo di ripristino.

**D: quanto tempo è necessario per l'aggiornamento automatico?**

- R: i tempi di inattività effettivi per l'aggiornamento sono solo il tempo necessario per sospendere e riprendere il servizio, che è compreso tra 5 e 10 minuti. Dopo questa breve interruzione, un processo in background eseguirà una migrazione delle risorse di archiviazione. L'intervallo di tempo per il processo in background dipende dalle dimensioni del pool SQL.

**D: quando si verifica l'aggiornamento automatico?**

- R: durante la pianificazione della manutenzione. Sfruttando la pianificazione della manutenzione scelta dal cliente si ridurrà al minimo l'interruzione delle attività aziendali.

**D: quali operazioni è necessario eseguire se il processo di aggiornamento in background sembra bloccato?**

 - R: avviare un REINDEX delle tabelle columnstore. Si noti che durante l'operazione di reindicizzazione le tabelle saranno offline.

**D: cosa accade se Gen2 non ha l'obiettivo del livello di servizio in Gen1?**
- R: se si esegue DW600 o DW1200 in Gen1, è consigliabile usare rispettivamente DW500c o compreso dw1000c, dal momento che Gen2 offre più memoria, risorse e prestazioni superiori rispetto a Gen1.

**D: è possibile disabilitare il backup geografico?**
- R: No. Il backup geografico è una funzionalità aziendale che consente di mantenere la disponibilità del pool SQL nel caso in cui un'area diventi non disponibile. Aprire una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) in caso di ulteriori problemi.

**D: esiste una differenza nella sintassi T-SQL tra Gen1 e Gen2?**

- R: non sono state apportate modifiche alla sintassi del linguaggio T-SQL da Gen1 a Gen2.

**D: Gen2 supporta le finestre di manutenzione?**

- R: Sì.

**D: sarà possibile creare una nuova istanza di Gen1 dopo l'aggiornamento dell'area?**

- R: No. Dopo l'aggiornamento di un'area, la creazione di nuove istanze di Gen1 verrà disabilitata.

## <a name="next-steps"></a>Passaggi successivi

- [Passaggi dell'aggiornamento](upgrade-to-latest-generation.md)
- [Finestre di manutenzione](maintenance-scheduling.md)
- [Monitoraggio dell'integrità delle risorse](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Elementi da verificare prima di iniziare una migrazione](upgrade-to-latest-generation.md#before-you-begin)
- [Aggiornamento sul posto e aggiornamento da un punto di ripristino](upgrade-to-latest-generation.md)
- [Creare un punto di ripristino definito dall'utente](sql-data-warehouse-restore-points.md)
- [Informazioni su come eseguire il ripristino a Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Aprire una richiesta di supporto per SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
