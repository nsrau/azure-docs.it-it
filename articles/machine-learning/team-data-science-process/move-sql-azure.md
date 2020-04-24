---
title: Spostare i dati in un database SQL di Azure - Processo di analisi scientifica dei dati per i team
description: Spostare i dati da file flat (formati CSV o TSV) o da dati archiviati in un SQL Server locale a un database SQL di Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722459"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Spostamento dei dati in un database SQL di Azure per Azure Machine Learning

Questo articolo descrive le opzioni per lo stato di trasferimento dei dati da file flat (formati CSV o TSV) o da dati archiviati in un SQL Server locale a un database SQL di Azure. Queste attività per lo spostamento dei dati nel cloud fanno parte del Processo di analisi scientifica dei dati per i team.

Per un argomento che descrive le opzioni per lo spostamento dei dati a SQL Server locale per Machine Learning, vedere [Spostamento dei dati in SQL Server in una macchina virtuale di Azure](move-sql-server-virtual-machine.md).

Nella tabella seguente vengono riepilogate le opzioni per lo spostamento dei dati a un database SQL di Azure.

| <b>ORIGINE</b> | <b>DESTINATION: database SQL di Azure</b> |
| --- | --- |
| <b>File flat (con formato CSV o TSV)</b> |[Inserimento di massa query SQL](#bulk-insert-sql-query) |
| <b>SQL Server locale</b> |1. [Esportazione in un file flat](#export-flat-file)<br> 2. [migrazione guidata database SQL](#insert-tables-bcp)<br> 3. [backup e ripristino del database](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Prerequisiti
Questa procedura descritta di seguito richiede di disporre di:

* Una **sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un **account di archiviazione di Azure**. In questa esercitazione si userà un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md) . Dopo avere creato l'account di archiviazione, è necessario ottenere la chiave dell'account usata per accedere alla risorsa di archiviazione. Vedere [gestire le chiavi di accesso dell'account di archiviazione](../../storage/common/storage-account-keys-manage.md).
* Accesso a un **database SQL di Azure**. Se è necessario impostare un database SQL di Azure, la [Guida introduttiva al database SQL di Microsoft Azure](../../sql-database/sql-database-get-started.md) fornisce informazioni su come eseguire il provisioning di una nuova istanza di un database SQL di Azure.
* Installazione e configurazione di **Azure PowerShell** in locale. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

**Dati**: i processi di migrazione vengono illustrati usando il [set di dati NYC Taxi](https://chriswhong.com/open-data/foil_nyc_taxi/). Il set di dati NYC Taxi contiene informazioni sulle tariffe e sui dati delle tratte ed è disponibile nell'archivio BLOB di Azure ([dati NYC Taxi](https://www.andresmh.com/nyctaxitrips/)). Un esempio e una descrizione di questi file sono inclusi in [Descrizione del set di dati relativo alle corse dei taxi di NYC](sql-walkthrough.md#dataset).

È possibile adattare le procedure descritte di seguito a un set di dati personalizzati o seguire i passaggi come descritto utilizzando il set di dati NYC Taxi. Per caricare il set di dati NYC Taxi nel database di SQL Server locale, seguire la procedura descritta in [Importazione in blocco dei dati nel database SQL Server](sql-walkthrough.md#dbload). Queste istruzioni sono per SQL Server in una macchina virtuale di Azure, ma la procedura per il caricamento in SQL Server locale è la stessa.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>Trasferimento di dati da un'origine file flat a un database SQL di Azure
I dati nei file flat (CSV o TSV formattati) possono essere spostati in un database SQL di Azure usando una query SQL BULK INSERT.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>Query SQL BULK INSERT
I passaggi per la procedura che usano la query BULK INSERT SQL sono simili alle direzioni per lo trasferimento dei dati da un'origine file flat a SQL Server in una macchina virtuale di Azure. Per altre informazioni, vedere [Inserimento di massa query SQL](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>Trasferimento di dati da SQL Server locali a un database SQL di Azure
Se i dati di origine vengono archiviati in un SQL Server locale, esistono diverse possibilità per lo trasferimento dei dati in un database SQL di Azure:

1. [Esporta nel file flat](#export-flat-file)
2. [Migrazione guidata database SQL](#insert-tables-bcp)
3. [Backup e ripristino del database](#db-migration)
4. [Azure Data Factory](#adf)

I passaggi per le prime tre sono simili a quelle delle sezioni in [spostare i dati in SQL Server in una macchina virtuale di Azure](move-sql-server-virtual-machine.md) che coprono le stesse procedure. Le istruzioni seguenti forniscono i collegamenti alle sezioni appropriate al riguardo.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Esportazione in un file flat
I passaggi per l'esportazione in un file flat sono simili a quelli descritti nell'esportazione in un [file flat](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Migrazione guidata database SQL
I passaggi per l'utilizzo della migrazione guidata database SQL sono simili a quelli descritti nella [migrazione guidata database SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Backup e ripristino database
I passaggi per l'utilizzo del backup e del ripristino del database sono simili a quelli elencati in [backup e ripristino del database](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Data factory di Azure
Informazioni su come spostare i dati in un database SQL di Azure con Azure Data Factory (ADF) in questo argomento [spostare i dati da un'istanza di SQL Server locale a SQL Azure con Azure Data Factory](move-sql-azure-adf.md). Questo argomento illustra come usare ADF per spostare i dati da un database di SQL Server locale a un database SQL di Azure tramite l'archiviazione BLOB di Azure.

È consigliabile usare ADF quando i dati devono essere migrati continuamente con origini ibride locali e cloud.  ADF aiuta anche quando i dati necessitano di trasformazioni o necessitano di una nuova logica di business durante la migrazione. L’ADF consente la pianificazione e il monitoraggio dei processi utilizzando semplici script JSON che gestiscono lo spostamento dei dati su base periodica. ADF dispone anche di altre funzionalità quali il supporto di operazioni complesse.
