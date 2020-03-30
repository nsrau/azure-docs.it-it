---
title: Eseguire la migrazione del pool SQL a Gen2Migrate your SQL pool to Gen2
description: Istruzioni per la migrazione di un pool SQL esistente a Gen2 e la pianificazione della migrazione in base all'area.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4714d5908fffb6f5c1440c3ec512fb8173da4b57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346770"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Aggiornare il pool SQL a Gen2Upgrade your SQL pool to Gen2

Microsoft sta contribuendo a ridurre il costo entry-level di esecuzione di un pool SQL.  Sono ora disponibili livelli di elaborazione inferiori in grado di gestire query richieste per il pool SQL. Leggere l'annuncio completo Supporto del livello di [calcolo inferiore per Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nuova offerta è disponibile nelle regioni indicate nella tabella seguente. Per le aree supportate, i pool SQL Gen1 esistenti possono essere aggiornati a Gen2 tramite:For supported regions, existing Gen1 SQL pools can be upgraded to Gen2 through either:

- Il processo di **aggiornamento automatico:** Gli aggiornamenti automatici non vengono avviati non appena il servizio è disponibile in un'area geografica.  Quando gli aggiornamenti automatici iniziano in un'area specifica, i singoli aggiornamenti del data warehouse verranno eseguiti durante la pianificazione di manutenzione selezionata.
- [**Auto-aggiornamento a Gen2:**](#self-upgrade-to-gen2) È possibile controllare quando eseguire l'aggiornamento eseguendo un auto-aggiornamento a Gen2.You can control when to upgrade by doing a self-upgrade to Gen2. Se l'area non è ancora supportata, è possibile eseguire il ripristino da un punto di ripristino direttamente a un'istanza Gen2 in un'area supportata.

## <a name="automated-schedule-and-region-availability-table"></a>Tabella delle pianificazioni automatiche e della disponibilità per area

La tabella seguente indica la data in cui il livello di calcolo inferiore Gen2 sarà disponibile in ogni area e la data di inizio degli aggiornamenti automatici. Le date sono soggette a variazioni. Controllare periodicamente questa pagina per sapere quando la propria area sarà disponibile.

\* indica che una pianificazione specifica per l'area non è attualmente disponibile.

| **Regione** | **Disponibilità livello inferiore Gen2** | **Inizio aggiornamenti automatici** |
|:--- |:--- |:--- |
| Canada orientale |1o giugno 2020 |1o luglio 2020 |
| Cina orientale |\* |\* |
| Cina settentrionale |\* |\* |
| Germania centrale |\* |\* |
| Germania Centro Ovest |Disponibile |1o maggio 2020 |
| India occidentale |Disponibile |1o maggio 2020  |

## <a name="automatic-upgrade-process"></a>Processo di aggiornamento automatico

In base al grafico sulla disponibilità riportato sopra, la pianificazione degli aggiornamenti automatici per le istanze Gen1 verrà eseguita. Per evitare interruzioni impreviste della disponibilità del pool SQL, gli aggiornamenti automatici verranno pianificati durante la pianificazione della manutenzione. La possibilità di creare una nuova istanza Gen1 verrà disabilitata nelle aree sottoposte a aggiornamento automatico a Gen2.The ability to create a new Gen1 instance will be disabled in regions under auto upgrade to Gen2. Gen1 sarà deprecato una volta completati gli aggiornamenti automatici. Per altre informazioni sulle pianificazioni, vedere [Visualizzare una pianificazione della manutenzione](maintenance-scheduling.md#view-a-maintenance-schedule).

Il processo di aggiornamento comporterà un breve calo della connettività (circa 5 min) quando riavviiamo il pool SQL.  Una volta riavviato, il pool SQL sarà completamente disponibile per l'utilizzo. Tuttavia, si verifichi un calo delle prestazioni mentre il processo di aggiornamento continua ad aggiornare i file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Si può anche accelerare il processo di aggiornamento dei file di dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie con SLO e classe di risorse più grandi dopo il riavvio.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

## <a name="self-upgrade-to-gen2"></a>Aggiornamento autonomo a Gen2

È possibile scegliere di eseguire l'auto-aggiornamento seguendo questi passaggi in un pool SQL Gen1 esistente. Se si sceglie di eseguire l'aggiornamento automatico, è necessario completarlo prima che inizi il processo di aggiornamento automatico nella propria area geografica. In questo modo si garantisce qualsiasi rischio di aggiornamenti automatici che causano un conflitto.

L'aggiornamento autonomo offre due opzioni.  È possibile aggiornare il pool SQL corrente sul posto oppure ripristinare un pool SQL Gen1 in un'istanza Gen2.You can either upgrade your current SQL pool in-place or you can restore a Gen1 SQL pool into a Gen2 instance.

- [Aggiorna sul posto:](upgrade-to-latest-generation.md) questa opzione aggiornerà il pool SQL Gen1 esistente a Gen2.Upgrade in-place - This option will upgrade your existing Gen1 SQL pool to Gen2. Il processo di aggiornamento comporterà un breve calo della connettività (circa 5 min) quando riavviiamo il pool SQL.  Una volta riavviato, il pool SQL sarà completamente disponibile per l'utilizzo. Se si verificano problemi durante l'aggiornamento, aprire una richiesta di [supporto](sql-data-warehouse-get-started-create-support-ticket.md) e fare riferimento a "Aggiornamento Gen2" come possibile causa.
- [Eseguire l'aggiornamento dal punto di ripristino:](sql-data-warehouse-restore-points.md) creare un punto di ripristino definito dall'utente nel pool SQL Gen1 corrente e quindi eseguire il ripristino direttamente in un'istanza Gen2.Upgrade from restore point - Create a user-defined restore point on your current Gen1 SQL pool and then restore directly to a Gen2 instance. Il pool SQL Gen1 esistente rimarrà in posizione. Una volta completato il ripristino, il pool SQL Gen2 sarà completamente disponibile per l'uso.  Dopo aver eseguito tutti i processi di test e convalida sull'istanza di Gen2 ripristinata, l'istanza di Gen1 originale può essere eliminata.

   - Passaggio 1: Dal portale di Azure creare un punto di [ripristino definito dall'utente.](sql-data-warehouse-restore-active-paused-dw.md)
   - Passaggio 2: Quando si esegue il ripristino da un punto di ripristino definito dall'utente, impostare il "Livello di prestazioni" sul livello Gen2 preferito.

Si potrebbe riscontrare un periodo di riduzione delle prestazioni mentre il processo di aggiornamento prosegue l'aggiornamento dei file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Per accelerare il processo di migrazione dei dati in background, è possibile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie su cui si prevede di eseguire query, con SLO e classe di risorse più grandi.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

Se si verificano problemi con il pool SQL, creare una richiesta di [supporto](sql-data-warehouse-get-started-create-support-ticket.md) e fare riferimento a "Aggiornamento Gen2" come possibile causa.

Per altre informazioni, vedere [Aggiornamento a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Domande frequenti sulla migrazione

**D: Gen2 costa lo stesso costo di Gen1?**

- A: Sì.

**D: In che modo gli aggiornamenti influiranno sui miei script di automazione?**

- R: Qualsiasi script di automazione che fa riferimento a un obiettivo del livello di servizio deve essere modificato in modo da corrispondere all'equivalente Gen2.  Vedere i dettagli [qui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**D: Quanto tempo richiede normalmente un auto-aggiornamento?**

- R: È possibile eseguire l'aggiornamento sul posto o da un punto di ripristino.  
   - L'aggiornamento sul posto causerà momentaneamente la sospensione e la ripresa del pool SQL.  Un processo in background continuerà mentre il pool SQL è online.  
   - L'aggiornamento da un punto di ripristino richiede più tempo, in quanto deve essere eseguito l'intero processo di ripristino.

**D: Quanto tempo richiederà l'aggiornamento automatico?**

- R: Il tempo di inattività effettivo per l'aggiornamento è solo il tempo necessario per sospendere e riprendere il servizio, che è compreso tra 5 e 10 minuti. Dopo questa breve interruzione, un processo in background eseguirà una migrazione delle risorse di archiviazione. Il periodo di tempo per il processo in background dipende dalle dimensioni del pool SQL.

**D: Quando avrà luogo questo aggiornamento automatico?**

- R: Durante il programma di manutenzione. Sfruttando la pianificazione della manutenzione scelta dal cliente si ridurrà al minimo l'interruzione delle attività aziendali.

**D: Cosa devo fare se il mio processo di aggiornamento in background sembra essere bloccato?**

 - R: Avviare una reindicizzazione delle tabelle Columnstore. Si noti che durante l'operazione di reindicizzazione le tabelle saranno offline.

**D: Cosa succede se Gen2 non ha l'obiettivo del livello di servizio che ho su Gen1?**
- R: Se si esegue un DW600 o DW1200 su Gen1, si consiglia di utilizzare rispettivamente DW500c o DW1000c poiché Gen2 fornisce più memoria, risorse e prestazioni superiori rispetto a Gen1.

**D: È possibile disabilitare il geo-backup?**
- R: No. Il geo-backup è una funzionalità aziendale per mantenere la disponibilità del pool SQL nel caso in cui un'area non sia più disponibile. Aprire una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) in caso di ulteriori problemi.

**D: Esiste una differenza nella sintassi T-SQL tra Gen1 e Gen2?**

- R: Non viene apportata alcuna modifica alla sintassi del linguaggio T-SQL da Gen1 a Gen2.

**D: Gen2 supporta la manutenzione di Windows?**

- A: Sì.

**D: Sarà possibile creare una nuova istanza Gen1 dopo l'aggiornamento dell'area?**

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
