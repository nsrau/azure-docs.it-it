---
title: Problemi noti e limitazioni con le migrazioni online all'istanza gestita del database SQL di AzureKnown issues and limitations with online migrations to Azure SQL Database managed instance
description: Informazioni sui problemi noti/limitazioni di migrazione associate alle migrazioni online all'istanza gestita del database SQL di Azure.Learn about known issues/migration limitations associated with online migrations to Azure SQL Database managed instance.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648666"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Problemi noti/limitazioni di migrazione con le migrazioni online all'istanza gestita del database SQL di AzureKnown issues/migration limitations with online migrations to Azure SQL Database managed instance

Di seguito sono descritti i problemi noti e le limitazioni associati alle migrazioni online da SQL Server all'istanza gestita del database SQL di Azure.Known issues and limitations that are associated with online migrations from SQL Server to Azure SQL Database managed instance are described below.

> [!IMPORTANT]
> Con le migrazioni online di SQL Server al database SQL di Azure, la migrazione dei tipi di dati SQL_variant non è supportata.

## <a name="backup-requirements"></a>Requisiti di backup

- **Backup con checksum**

    Il servizio Migrazione del database di Azure usa il metodo di backup e ripristino per eseguire la migrazione dei database locali nell'istanza gestita del database SQL. Il servizio Migrazione del database di Azure supporta solo i backup creati usando il checksum.

    [Abilitare o disabilitare i checksum di backup durante il backup o il ripristino (SQL Server)Enable or Disable Backup Checksums during Backup or Restore (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Se si accettano i backup del database con la compressione, il checksum è un comportamento predefinito, a meno che non sia disabilitato in modo esplicito.

    Con le migrazioni offline, se si sceglie **Consentirà al Servizio migrazione database**di Azure... , quindi il servizio Migrazione del database di Azure eseguirà il backup del database con l'opzione checksum abilitata.

- **Supporti di backup**

    Assicurarsi di eseguire ogni backup su un supporto di backup separato (file di backup). Il servizio Migrazione del database di Azure non supporta i backup aggiunti a un singolo file di backup. Eseguire backup completi e i backup del log in file di backup separati.

## <a name="data-and-log-file-layout"></a>Layout dei file di dati e di registro

- **Numero di file di registro**

    Il servizio Migrazione del database di Azure non supporta i database con più file di log. Se si dispone di più file di registro, compattarli e riorganizzarli in un unico file di registro delle transazioni. Poiché non è possibile eseguire il backup del file di registro da remoto, è necessario eseguire prima il backup del file di registro.

## <a name="sql-server-features"></a>caratteristiche di SQL Server

- **FileStream/Tabelle File**

    L'istanza gestita del database SQL attualmente non supporta FileStream e FileTables. Per i carichi di lavoro dipendenti da queste funzionalità, è consigliabile optare per SQL Server in esecuzione nelle macchine virtuali di Azure come destinazione di Azure.For workloads dependent on these features, we recommend that you opt for SQL Servers running on Azure VMs as your Azure target.

- **Tabelle in memoria**

    OLTP in memoria è disponibile nei livelli Premium e Business Critical per l'istanza gestita del database SQL. il livello Scopo generale non supporta OLTP in memoria.

## <a name="migration-resets"></a>Reimpostazioni della migrazione

- **Distribuzioni**

    L'istanza gestita del database SQL è un servizio PaaS con applicazione automatico di patch e aggiornamenti delle versioni. Durante la migrazione dell'istanza gestita del database SQL, gli aggiornamenti non critici consentono di eseguire fino a 36 ore. Successivamente (e per gli aggiornamenti critici), se la migrazione viene interrotta, il processo viene reimpostato su uno stato di ripristino completo.

    Il cutover della migrazione può essere chiamato solo dopo il ripristino del backup completo e viene aggiornato con tutti i backup del log. Se i cutover della migrazione di produzione sono interessati, contattare l'alias di feedback di [Azure DMS](mailto:dmsfeedback@microsoft.com).
