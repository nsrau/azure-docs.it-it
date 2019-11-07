---
title: Eseguire la migrazione del data warehouse a Gen2
description: Istruzioni per la migrazione di un data warehouse esistente a Gen2, con la pianificazione della migrazione per area.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.custom: seo-lt-2019
ms.openlocfilehash: 888f50d645c9b3babf95335e434db65423108ccb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693027"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Aggiornare il data warehouse a Gen2

Microsoft contribuisce a ridurre il costo di ingresso per l'esecuzione di un data warehouse.  Sono ora disponibili più livelli di calcolo in grado di gestire query complesse per Azure SQL Data Warehouse. Leggere l'annuncio completo [supporto del livello di calcolo più basso per Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nuova offerta è disponibile nelle aree geografiche indicate nella tabella seguente. Per le aree supportate, i data warehouse Gen1 esistenti possono essere aggiornati a Gen2 in uno dei modi seguenti:

- **Processo di aggiornamento automatico:** Gli aggiornamenti automatici non vengono avviati non appena il servizio è disponibile in un'area.  Quando iniziano in un'area specifica, gli aggiornamenti di data warehouse singoli avvengono durante la pianificazione della manutenzione selezionata.
- [**Aggiornamento automatico a Gen2:** ](#self-upgrade-to-gen2) È possibile controllare quando eseguire l'aggiornamento eseguendo un aggiornamento automatico a Gen2. Se l'area non è ancora supportata, è possibile eseguire il ripristino da un punto di ripristino direttamente in un'istanza di Gen2 in un'area supportata.

## <a name="automated-schedule-and-region-availability-table"></a>Tabella delle pianificazioni automatiche e della disponibilità per area

La tabella seguente indica la data in cui il livello di calcolo inferiore Gen2 sarà disponibile in ogni area e la data di inizio degli aggiornamenti automatici. Le date sono soggette a variazioni. Controllare periodicamente questa pagina per sapere quando la propria area sarà disponibile.

\* indica che una pianificazione specifica per l'area non è attualmente disponibile.

| **Area** | **Disponibilità livello inferiore Gen2** | **Inizio aggiornamenti automatici** |
|:--- |:--- |:--- |
| Australia orientale |Disponibile |Complete |
| Australia sudorientale |Disponibile |Complete |
| Brasile meridionale |Disponibile |Complete |
| Canada centrale |Disponibile |Complete |
| Canada orientale |1 giugno 2020 |1 ° luglio 2020 |
| Stati Uniti centrali |Disponibile |Complete |
| Cina orientale |\* |\* |
| Cina orientale 2 |Disponibile |Complete |
| Cina settentrionale |\* |\* |
| Cina settentrionale 2 |Disponibile |Complete |
| Asia orientale |Disponibile |Complete |
| Stati Uniti orientali |Disponibile |Complete |
| Stati Uniti orientali 2 |Disponibile |Complete |
| Francia centrale |Disponibile |In corso |
| Germania centrale |\* |\* |
| Germania centro-occidentale |1 settembre 2019|1 ottobre 2019 |
| India centrale |Disponibile |Complete |
| India meridionale |Disponibile |Complete |
| India occidentale |1 ° luglio 2019 |In corso |
| Giappone orientale |Disponibile |Complete |
| Giappone occidentale |Disponibile |Complete |
| Corea del Sud centrale |Disponibile |Complete |
| Corea del Sud meridionale |Disponibile |Complete |
| Stati Uniti centro-settentrionali |Disponibile |Complete |
| Europa settentrionale |Disponibile |Complete |
| Sudafrica settentrionale |12 luglio 2019 |Complete |
| Stati Uniti centro-meridionali |Disponibile |Complete |
| Asia sudorientale |Disponibile |Complete |
| Emirati Arabi Uniti settentrionali |20 luglio 2019 |Complete |
| Regno Unito meridionale |Disponibile |In corso |
| Regno Unito occidentale |Disponibile |In corso |
| Stati Uniti centro-occidentali |1 novembre 2019 |1 dicembre 2019|
| Europa occidentale |Disponibile |Complete |
| Stati Uniti occidentali |Disponibile |Complete |
| Stati Uniti occidentali 2 |Disponibile |Complete |

## <a name="automatic-upgrade-process"></a>Processo di aggiornamento automatico

In base al grafico di disponibilità precedente, verranno pianificati gli aggiornamenti automatici per le istanze di Gen1. Per evitare interruzioni impreviste della disponibilità del data warehouse, gli aggiornamenti automatici verranno pianificati durante la pianificazione della manutenzione del cliente. La possibilità di creare una nuova istanza di Gen1 verrà disabilitata nelle aree in cui viene eseguito l'aggiornamento automatico a Gen2. Gen1 sarà deprecato dopo il completamento degli aggiornamenti automatici. Per altre informazioni sulle pianificazioni, vedere [Visualizzare una pianificazione della manutenzione](viewing-maintenance-schedule.md).

Il processo di aggiornamento comporterà una breve riduzione della connettività (circa 5 minuti) quando si riavvia il data warehouse.  Dopo il riavvio, il data warehouse sarà completamente disponibile per l'uso. Tuttavia, è possibile che si verifichi un calo delle prestazioni mentre il processo di aggiornamento continua ad aggiornare i file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Si può anche accelerare il processo di aggiornamento dei file di dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie con SLO e classe di risorse più grandi dopo il riavvio.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

## <a name="self-upgrade-to-gen2"></a>Aggiornamento autonomo a Gen2

È possibile scegliere di eseguire l'aggiornamento automatico attenendosi alla procedura seguente in un data warehouse Gen1 esistente. Se si sceglie di eseguire l'aggiornamento automatico, è necessario completarlo prima che il processo di aggiornamento automatico inizi nella propria area. In questo modo si evita il rischio che gli aggiornamenti automatici causino un conflitto.

L'aggiornamento autonomo offre due opzioni.  È possibile aggiornare il data warehouse corrente sul posto oppure ripristinare un data warehouse Gen1 in un'istanza di data warehouse Gen2.

- [Aggiornamento sul posto](upgrade-to-latest-generation.md): questa opzione aggiorna il data warehouse Gen1 esistente a Gen2. Il processo di aggiornamento comporterà una breve riduzione della connettività (circa 5 minuti) quando si riavvia il data warehouse.  Dopo il riavvio, il data warehouse sarà completamente disponibile per l'uso. Se si verificano problemi durante l'aggiornamento, aprire una [richiesta di supporto](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e fare riferimento all'aggiornamento di Gen2 come possibile.
- [Aggiornamento da un punto di ripristino](sql-data-warehouse-restore.md): questa opzione crea un punto di ripristino definito dall'utente nel data warehouse Gen1 corrente e quindi esegue il ripristino direttamente in un'istanza di Gen2. Il data warehouse Gen1 esistente rimarrà nella sua posizione. Una volta completato il ripristino, il data warehouse Gen2 sarà completamente disponibile per l'uso.  Dopo aver eseguito tutti i processi di test e convalida sull'istanza di Gen2 ripristinata, l'istanza di Gen1 originale può essere eliminata.

   - Passaggio 1: dalla portale di Azure [creare un punto di ripristino definito dall'utente](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - Passaggio 2: durante il ripristino da un punto di ripristino definito dall'utente, impostare il "livello di prestazioni" sul livello Gen2 preferito.

Si potrebbe riscontrare un periodo di riduzione delle prestazioni mentre il processo di aggiornamento prosegue l'aggiornamento dei file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Per accelerare il processo di migrazione dei dati in background, è possibile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie su cui si prevede di eseguire query, con SLO e classe di risorse più grandi.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

In caso di problemi con il data warehouse, creare una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) indicando l'aggiornamento a Gen2 come possibile causa.

Per altre informazioni, vedere [Aggiornamento a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Domande frequenti sulla migrazione

**D: il costo di Gen2 è uguale a quello di Gen1?**

- A: Sì.

**D: in che modo gli aggiornamenti interessano gli script di automazione?**

- R: tutti gli script di automazione che fanno riferimento a un obiettivo del livello di servizio devono essere modificati in modo che corrispondano all'equivalente Gen2.  Vedere i dettagli [qui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**D: quanto tempo richiede normalmente un aggiornamento automatico?**

- R: è possibile eseguire l'aggiornamento sul posto o eseguire l'aggiornamento da un punto di ripristino.  
   - Con l'aggiornamento sul posto l'esecuzione del data warehouse dovrà essere momentaneamente sospesa e quindi ripresa.  Un processo in background continuerà mentre il data warehouse è online.  
   - L'aggiornamento da un punto di ripristino richiede più tempo, in quanto deve essere eseguito l'intero processo di ripristino.

**D: quanto tempo è necessario per l'aggiornamento automatico?**

- R: i tempi di inattività effettivi per l'aggiornamento sono solo il tempo necessario per sospendere e riprendere il servizio, che è compreso tra 5 e 10 minuti. Dopo questa breve interruzione, un processo in background eseguirà una migrazione delle risorse di archiviazione. La durata del processo in background dipende dalle dimensioni del data warehouse.

**D: quando si verifica l'aggiornamento automatico?**

- R: durante la pianificazione della manutenzione. Sfruttando la pianificazione della manutenzione scelta dal cliente si ridurrà al minimo l'interruzione delle attività aziendali.

**D: quali operazioni è necessario eseguire se il processo di aggiornamento in background sembra bloccato?**

 - R: avviare un REINDEX delle tabelle columnstore. Si noti che durante l'operazione di reindicizzazione le tabelle saranno offline.

**D: cosa accade se Gen2 non ha l'obiettivo del livello di servizio in Gen1?**
- R: se si esegue DW600 o DW1200 in Gen1, è consigliabile usare rispettivamente DW500c o compreso dw1000c, dal momento che Gen2 offre più memoria, risorse e prestazioni superiori rispetto a Gen1.

**D: è possibile disabilitare il backup geografico?**
- R: No. Il backup geografico è una funzionalità aziendale che mantiene la disponibilità del data warehouse nell'eventualità in cui un'area diventi non disponibile. Aprire una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) in caso di ulteriori problemi.

**D: esiste una differenza nella sintassi T-SQL tra Gen1 e Gen2?**

- R: non sono state apportate modifiche alla sintassi del linguaggio T-SQL da Gen1 a Gen2.

**D: Gen2 supporta le finestre di manutenzione?**

- A: Sì.

**D: sarà possibile creare una nuova istanza di Gen1 dopo l'aggiornamento dell'area?**

- R: No. Dopo l'aggiornamento di un'area, la creazione di nuove istanze di Gen1 verrà disabilitata.

## <a name="next-steps"></a>Passaggi successivi

- [Passaggi dell'aggiornamento](upgrade-to-latest-generation.md)
- [Finestre di manutenzione](maintenance-scheduling.md)
- [Monitoraggio dell'integrità delle risorse](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Elementi da verificare prima di iniziare una migrazione](upgrade-to-latest-generation.md#before-you-begin)
- [Aggiornamento sul posto e aggiornamento da un punto di ripristino](upgrade-to-latest-generation.md)
- [Creare un punto di ripristino definito dall'utente](sql-data-warehouse-restore-points.md)
- [Informazioni su come eseguire il ripristino a Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Aprire una richiesta di supporto per SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
