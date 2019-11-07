---
title: Sincronizzare i dati dal database SQL Edge di Azure usando Sincronizzazione dati SQL | Microsoft Docs
description: Informazioni sulla sincronizzazione dei dati tra il database SQL Edge di Azure e Sincronizzazione dati SQL di Azure
keywords: database sql edge, sincronizzare i dati dal database sql edge, sincronizzazione dei dati del database sql edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509195"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Esercitazione: Sincronizzare i dati dal database SQL Edge al database SQL di Azure usando Sincronizzazione dati SQL

In questa esercitazione si apprenderà come usare il *gruppo di sincronizzazione* di Sincronizzazione dati SQL per sincronizzare in modo incrementale i dati dal database SQL Edge di Azure al database SQL di Azure. La sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati bidirezionalmente tra più database SQL e istanze di SQL Server. Per altre informazioni su Sincronizzazione dati SQL di Azure, vedere [Sincronizzazione dati SQL di Azure](../sql-database/sql-database-sync-data.md).

Poiché il database SQL Edge di Azure è basato sulle versioni più recenti del [motore di database di Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation/), qualsiasi meccanismo di sincronizzazione dei dati applicabile a un'istanza di SQL Server locale può essere usato anche per sincronizzare i dati da o verso un'istanza del database SQL Edge in esecuzione in un dispositivo perimetrale.

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è necessario un computer Windows configurato con [SQL Azure Data Sync Agent](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Prima di iniziare

* Creare un database SQL di Azure. Per informazioni su come creare un database SQL di Azure tramite il portale di Azure, vedere [Creare un database singolo nel database SQL di Azure](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Creare le tabelle e gli altri oggetti necessari nella distribuzione del database SQL di Azure.

* Creare le tabelle e gli oggetti necessari nella distribuzione del database SQL Edge di Azure. Per altre informazioni, vedere [Uso di pacchetti di applicazione livello dati del database SQL con il database SQL Edge](stream-analytics.md).

* Registrare l'istanza del database SQL Edge di Azure con SQL Azure Data Sync Agent. Per altre informazioni, vedere [Aggiungere un database SQL Server locale](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Sincronizzare i dati tra un database SQL di Azure e un database SQL Edge

La configurazione della sincronizzazione tra un database SQL di Azure e un'istanza del database SQL Edge con Sincronizzazione dati SQL prevede tre passaggi principali.  

1. Usare il portale di Azure per creare un gruppo di sincronizzazione. Per creare il gruppo di sincronizzazione, vedere [Creare un gruppo di sincronizzazione usando il portale di Azure](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Lo stesso database *Hub* può essere usato per creare più gruppi di sincronizzazione diversi per sincronizzare i dati da diverse istanze del database SQL Edge a uno o più database SQL in Azure.

2. Aggiungere membri di sincronizzazione al gruppo di sincronizzazione. Per aggiungere membri al gruppo di sincronizzazione, vedere [Aggiungere membri al gruppo sincronizzazione dati SQL](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configurare il gruppo di sincronizzazione per selezionare le tabelle che faranno parte di questa sincronizzazione. Per configurare il gruppo di sincronizzazione, vedere [Configurare il gruppo di sincronizzazione](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Dopo aver completato i passaggi di questa esercitazione, si avrà un gruppo di sincronizzazione che include un database SQL di Azure e un'istanza del database SQL Edge.

Per altre informazioni su Sincronizzazione dati SQL, vedere gli articoli seguenti:

* [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL Azure](../sql-database/sql-database-data-sync-agent.md)

* [Procedure consigliate](../sql-database/sql-database-best-practices-data-sync.md) e [Come risolvere i problemi di sincronizzazione dati SQL di Azure](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](../sql-database/sql-database-sync-monitor-oms.md)

* [Aggiornare lo schema di sincronizzazione con Transact-SQL](../sql-database/sql-database-update-sync-schema.md) o [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database SQL Edge di Azure](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Nell'esercitazione sostituire i dettagli del database *OnPremiseServer* con i dettagli del database SQL Edge di Azure.
