---
title: Eseguire la migrazione di Azure SQL Data Warehouse a Gen2 | Microsoft Docs
description: Istruzioni per la migrazione di un data warehouse esistente a Gen2, con la pianificazione della migrazione per area.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 02/09/2019
ms.openlocfilehash: a3ec74d0b22bfafb4353eca400b389b07a58ba39
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246322"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Aggiornare il data warehouse a Gen2
Microsoft aiuta a ridurre il costo di esecuzione di un data warehouse in grado di gestire query complesse aggiungendo livelli di calcolo inferiori per Azure SQL Data Warehouse. È possibile leggere l'annuncio completo relativo al [supporto di livelli di calcolo inferiori per Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nuova offerta è già disponibile nelle aree indicate nella tabella seguente. Per le aree supportate, i data warehouse Gen1 esistenti possono essere aggiornati a Gen2 in uno dei modi seguenti:
- **Processo di aggiornamento automatico:** gli aggiornamenti automatici non iniziano appena il servizio è disponibile in un'area.  Quando iniziano in un'area specifica, gli aggiornamenti di data warehouse singoli avvengono durante la pianificazione della manutenzione selezionata. 
- **Aggiornamento autonomo a Gen2:** se si preferisce decidere autonomamente quando aggiornare, è possibile eseguire un aggiornamento autonomo a Gen2. Se la propria area non è ancora supportata, è possibile eseguire la migrazione del data warehouse in un'area supportata e quindi procedere con un aggiornamento autonomo a Gen 2.

## <a name="automated-schedule-and-region-availability-table"></a>Tabella delle pianificazioni automatiche e della disponibilità per area
La tabella seguente indica la data in cui il livello di calcolo inferiore Gen2 sarà disponibile in ogni area e la data di inizio degli aggiornamenti automatici. Le date sono soggette a variazioni. Controllare periodicamente questa pagina per sapere quando la propria area sarà disponibile.

\* indica che una pianificazione specifica per l'area non è attualmente disponibile.

| **Area** | **Disponibilità livello inferiore Gen2** | **Inizio aggiornamenti automatici** |
|:--- |:--- |:--- |
| Australia centrale 1 |1 marzo 2019 |15 giugno 2019 |
| Australia centrale 2 |1 marzo 2019 |15 giugno 2019 |
| Australia orientale |Disponibile |1 maggio 2019 |
| Australia sud-orientale |1 marzo 2019 |15 giugno 2019 |
| Brasile meridionale |\* |\* |
| Canada centrale |Disponibile |1 maggio 2019 |
| Canada orientale |\* |\* |
| Stati Uniti centrali |Disponibile |1 maggio 2019 |
| Cina orientale |\* |\* |
| Cina settentrionale 1 |\* |\* |
| Asia orientale |Disponibile |1 maggio 2019 |
| Stati Uniti orientali 1 |Disponibile |16 marzo 2019 |
| Stati Uniti orientali 2 |Disponibile |16 marzo 2019 |
| Francia centrale |1 marzo 2019 |1 maggio 2019 |
| Germania centrale |\* |\* |
| India centrale |Disponibile |1 maggio 2019 |
| India meridionale 1 |1 marzo 2019 |15 giugno 2019 |
| Giappone orientale |Disponibile |1 maggio 2019 |
| Giappone occidentale |Disponibile |15 giugno 2019 |
| Corea del Sud centrale |1 marzo 2019 |1 maggio 2019 |
| Corea meridionale 1 |1 marzo 2019 |15 giugno 2019 |
| Stati Uniti centro-settentrionali |1 marzo 2019 |15 giugno 2019 |
| Europa settentrionale |Disponibile |16 marzo 2019 |
| Stati Uniti centro-meridionali |Disponibile |1 maggio 2019 |
| Asia sudorientale |Disponibile |16 marzo 2019 |
| Regno Unito meridionale |1 marzo 2019 |1 maggio 2019 |
| Regno Unito occidentale 1 |1 marzo 2019 |15 giugno 2019 |
| Stati Uniti centro-occidentali |\* |\* |
| Europa occidentale |Disponibile |16 marzo 2019 |
| Stati Uniti occidentali 1 |1 marzo 2019 |15 giugno 2019 |
| Stati Uniti occidentali 2 |Disponibile |16 marzo 2019 |

## <a name="automatic-upgrade-process"></a>Processo di aggiornamento automatico

A partire dal 16 marzo 2019 Microsoft inizierà a pianificare gli aggiornamenti automatici per le istanze di Gen1. Per evitare interruzioni impreviste della disponibilità del data warehouse, gli aggiornamenti automatici verranno pianificati durante la pianificazione della manutenzione del cliente. Per altre informazioni sulle pianificazioni, vedere [Visualizzare una pianificazione della manutenzione](viewing-maintenance-schedule.md).

Il processo di aggiornamento comporterà una breve interruzione della connettività (circa 5 minuti) per riavviare il data warehouse.  Dopo il riavvio, il data warehouse sarà completamente disponibile per l'uso, tuttavia si potrebbe riscontrare una riduzione delle prestazioni mentre il processo aggiorna i file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Si può anche accelerare il processo di aggiornamento dei file di dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie con SLO e classe di risorse più grandi dopo il riavvio.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

## <a name="self-upgrade-to-gen2"></a>Aggiornamento autonomo a Gen2

Se si vuole eseguire subito l'aggiornamento, è possibile farlo autonomamente seguendo questa procedura in un data warehouse Gen1 esistente. Se si sceglie questa opzione, è necessario completare l'aggiornamento autonomo prima che inizi il processo di aggiornamento automatico in tale area. In questo modo si evita il rischio che gli aggiornamenti automatici causino un conflitto.

L'aggiornamento autonomo offre due opzioni.  È possibile aggiornare il data warehouse corrente sul posto oppure ripristinare un data warehouse Gen1 in un'istanza di data warehouse Gen2.

- [Aggiornamento sul posto](upgrade-to-latest-generation.md): questa opzione aggiorna il data warehouse Gen1 esistente a Gen2. Il processo di aggiornamento comporterà una breve interruzione della connettività (circa 5 minuti) per riavviare il data warehouse.  Dopo il riavvio, il data warehouse sarà completamente disponibile per l'uso. Se per qualsiasi motivo è necessario ripristinare Gen1 da un'istanza di Gen2, aprire una [richiesta di supporto](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket).
- [Aggiornamento da un punto di ripristino](sql-data-warehouse-restore.md): questa opzione crea un punto di ripristino definito dall'utente nel data warehouse Gen1 corrente e quindi esegue il ripristino direttamente in un'istanza di Gen2. Il data warehouse Gen1 esistente rimarrà nella sua posizione. Una volta completato il ripristino, il data warehouse Gen2 sarà completamente disponibile per l'uso.  Dopo aver eseguito tutti i processi di test e convalida sull'istanza di Gen2 ripristinata, l'istanza di Gen1 originale può essere eliminata.
    - Passaggio 1: Nel portale di Azure [creare un punto di ripristino definito dall'utente](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
    - Passaggio 2: Quando si esegue il ripristino da un punto di ripristino definito dall'utente, impostare il "livello di prestazioni" sul piano Gen2 preferito.

Si potrebbe riscontrare un periodo di riduzione delle prestazioni mentre il processo di aggiornamento prosegue l'aggiornamento dei file di dati in background. La durata totale di questa riduzione del livello delle prestazioni dipende dalle dimensioni dei file di dati.

Per accelerare il processo di migrazione dei dati in background, è possibile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie su cui si prevede di eseguire query, con SLO e classe di risorse più grandi.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

In caso di problemi con il data warehouse, creare una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) indicando l'aggiornamento a Gen2 come possibile causa.

Per altre informazioni, vedere [Aggiornamento a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Domande frequenti sulla migrazione

**D: Il costo di Gen2 è uguale a quello di Gen1?**
- R: Sì.

**D: Quale effetto avranno gli aggiornamenti sugli script di automazione?**
- R: Tutti gli script di automazione che fanno riferimento a un obiettivo del livello di servizio devono essere modificati in modo da corrispondere all'equivalente di Gen2.  Vedere i dettagli [qui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**D: Quanto tempo richiede normalmente un aggiornamento autonomo?**
- R: È possibile eseguire l'aggiornamento sul posto o da un punto di ripristino.  
   - Con l'aggiornamento sul posto l'esecuzione del data warehouse dovrà essere momentaneamente sospesa e quindi ripresa.  Un processo in background continuerà mentre il data warehouse è online.  
   - L'aggiornamento da un punto di ripristino richiede più tempo, in quanto deve essere eseguito l'intero processo di ripristino.

**D: Quanto tempo richiede l'aggiornamento automatico?**
- R: Il tempo di inattività effettivo per l'aggiornamento corrisponde al tempo necessario per sospendere e riprendere l'esecuzione del servizio, ossia tra i 5 e i 10 minuti. Dopo questa breve interruzione, un processo in background eseguirà una migrazione delle risorse di archiviazione. La durata del processo in background dipende dalle dimensioni del data warehouse.

**D: Quando avverrà questo aggiornamento automatico?**
- R: Durante la pianificazione della manutenzione. Sfruttando la pianificazione della manutenzione scelta dal cliente si ridurrà al minimo l'interruzione delle attività aziendali.

**D: Cosa occorre fare se il processo di aggiornamento in background sembra bloccato?**
 - R: Attivare una reindicizzazione delle tabelle Columnstore. Si noti che durante l'operazione di reindicizzazione le tabelle saranno offline.

**D: Cosa occorre fare se Gen2 non ha lo stesso obiettivo del livello di servizio di cui si dispone con Gen1?**
- R: Se si esegue un DW600 o un DW1200 in Gen1, è consigliabile usare rispettivamente DW500c o DW1000c, in quanto Gen2 offre una quantità maggiore di memoria e risorse, oltre a prestazioni superiori rispetto a Gen1.

**D: È possibile disabilitare il backup geografico?**
- R: No. Il backup geografico è una funzionalità aziendale che mantiene la disponibilità del data warehouse nell'eventualità in cui un'area diventi non disponibile. Aprire una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) in caso di ulteriori problemi.

**D: La sintassi T-SQL di Gen1 è diversa da quella di Gen2?**
- R: La sintassi del linguaggio T-SQL è rimasta invariata da Gen1 a Gen2.

**D: Gen2 supporta le finestre di manutenzione?**
- R: Sì.

**D: Sarà possibile creare una nuova istanza di Gen1 dopo l'aggiornamento dell'area?**
- R: No. Dopo l'aggiornamento di un'area, la creazione di nuove istanze di Gen1 verrà disabilitata.

## <a name="next-steps"></a>Passaggi successivi

- [Passaggi dell'aggiornamento](upgrade-to-latest-generation.md)
- [Finestre di manutenzione](maintenance-scheduling.md)
- [Monitoraggio dell'integrità delle risorse](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Elementi da verificare prima di iniziare una migrazione](upgrade-to-latest-generation.md#before-you-begin)
- [Aggiornamento sul posto e aggiornamento da un punto di ripristino](upgrade-to-latest-generation.md)
- [Creare un punto di ripristino definito dall'utente](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Informazioni su come eseguire il ripristino a Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Aprire una richiesta di supporto per SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)