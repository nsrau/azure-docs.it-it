---
title: Sincronizzare i dati da SQL Edge di Azure usando la sincronizzazione dati SQL
description: Informazioni sulla sincronizzazione dei dati da SQL Edge di Azure usando la sincronizzazione dati SQL di Azure
keywords: SQL Edge,sincronizzare i dati da SQL Edge, sincronizzazione dati di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394901"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Esercitazione: Sincronizzare i dati da SQL Edge al database SQL di Azure con la sincronizzazione dati SQL

In questa esercitazione si apprenderà come usare il *gruppo di sincronizzazione* della sincronizzazione dati SQL di Azure per sincronizzare in modo incrementale i dati da SQL Edge di Azure al database SQL di Azure. La sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati in modo bidirezionale tra più database nel database SQL di Azure e istanze di SQL Server. Per altre informazioni su Sincronizzazione dati SQL, vedere [Sincronizzazione dati SQL di Azure](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Poiché SQL Edge è basato sulle versioni più recenti del [motore di database di SQL Server](/sql/sql-server/sql-server-technical-documentation/), qualsiasi meccanismo di sincronizzazione dei dati applicabile a un'istanza di SQL Server può essere usato anche per sincronizzare i dati da o verso un'istanza di SQL Edge in esecuzione in un dispositivo perimetrale.

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è necessario un computer Windows configurato con [Data Sync Agent per Sincronizzazione dati SQL di Azure](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

* Creare un database nel database SQL di Azure. Per informazioni su come creare un database con il portale di Azure, vedere [Creare un database singolo nel database SQL di Azure](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Creare le tabelle e gli altri oggetti necessari nella distribuzione del database SQL di Azure.

* Creare le tabelle e gli oggetti necessari nella distribuzione di SQL Edge di Azure. Per altre informazioni, vedere [Uso di pacchetti di applicazione livello dati del database SQL con SQL Edge](deploy-dacpac.md).

* Registrare l'istanza di SQL Edge di Azure con Data Sync Agent per la sincronizzazione dati SQL di Azure. Per altre informazioni, vedere [Aggiungere un database di SQL Server](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Sincronizzare i dati tra un database del database SQL di Azure e SQL Edge

La configurazione della sincronizzazione tra un database nel database SQL di Azure e un'istanza di SQL Edge con Sincronizzazione dati SQL prevede tre passaggi principali:  


1. Usare il portale di Azure per creare un gruppo di sincronizzazione. Per altre informazioni, vedere [Creare un gruppo di sincronizzazione](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). È possibile usare un unico database *hub* per creare più gruppi di sincronizzazione per sincronizzare i dati da diverse istanze di SQL Edge con uno o più database nel database SQL di Azure. 

2. Aggiungere membri di sincronizzazione al gruppo di sincronizzazione. Per altre informazioni, vedere [Aggiungere membri di sincronizzazione](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Configurare il gruppo di sincronizzazione per selezionare le tabelle che faranno parte della sincronizzazione. Per altre informazioni, vedere [Configurare un gruppo di sincronizzazione](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Dopo aver completato i passaggi precedenti, sarà disponibile gruppo di sincronizzazione che include un database del database SQL di Azure e un'istanza di SQL Edge.

Per altre informazioni sulla sincronizzazione dati SQL, vedere gli articoli seguenti:

* [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL Azure](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Procedure consigliate](../azure-sql/database/sql-data-sync-best-practices.md) e [Come risolvere i problemi di sincronizzazione dati SQL di Azure](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](../azure-sql/database/monitor-tune-overview.md)

* [Aggiornare lo schema di sincronizzazione con Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) o [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Passaggi successivi


* [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e SQL Edge di Azure](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). Nell'esercitazione sostituire i dettagli del database `OnPremiseServer` con i dettagli di SQL Edge di Azure.