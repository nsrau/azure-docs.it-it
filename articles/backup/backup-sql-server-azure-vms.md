---
title: Informazioni su SQL Server macchina virtuale di Azure con Backup di Azure | Microsoft Docs
description: Informazioni sui database SQL Server in macchine Virtuali di Azure e funzionalità tenendo in considerazione questa funzionalità.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sachdevaswati
ms.openlocfilehash: a57b52b3b0cc493fdde60e9bddfb0125ff2ce3e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175965"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sui Backup di SQL Server in macchine virtuali di Azure

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di database SQL Server in esecuzione nelle macchine virtuali di Azure usando [Backup di Azure](backup-overview.md).

## <a name="backup-process"></a>Processo di backup

Questa soluzione sfrutta le API native di SQL per eseguire i backup dei database SQL.

* Dopo aver specificato la VM di SQL Server che si desidera proteggere ed eseguire query per i database in it, il servizio Backup di Azure installerà un'estensione di backup del carico di lavoro nella macchina virtuale dal nome della `AzureBackupWindowsWorkload`  estensione.
* Questa estensione è costituito da un plug-in SQL e un coordinatore. Mentre il coordinatore è responsabile dell'attivazione dei flussi di lavoro per varie operazioni su come configurare il backup, backup e ripristino, il plug-in è responsabile per il flusso di dati effettivo.
* Per essere in grado di individuare i database in questa VM, Backup di Azure crea l'account `NT SERVICE\AzureWLBackupPluginSvc`. Questo account viene usato per il backup e ripristino e richiede autorizzazioni sysadmin SQL. Azure si basa su Backup di `NT AUTHORITY\SYSTEM` account individuazione database / (interrogazione), pertanto questo account è necessario essere un account di accesso pubblico su SQL. Se la VM SQL Server è stato creato da Azure Marketplace, è possibile ricevere un errore **UserErrorSQLNoSysadminMembership**. In questo caso [, seguire queste istruzioni](backup-azure-sql-database.md).
* Una volta che si attiveranno configurare la protezione per i database selezionati, il servizio di backup consente di impostare il coordinatore con le pianificazioni di backup e altri dettagli dei criteri, che l'estensione memorizza nella cache in locale nella macchina virtuale 
* All'ora pianificata, il coordinatore comunica con il plug-in e avvia il flusso di dati di backup di SQL server usando l'infrastruttura VDI.  
* Il plug-in Invia i dati direttamente i servizi di ripristino, eliminando così la necessità di un percorso di gestione temporanea. I dati vengono crittografati e archiviati dal servizio Backup di Azure negli account di archiviazione.
* Una volta completato il trasferimento dei dati, coordinator conferma commit con il servizio di backup.

  ![Architettura di Backup di SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare quanto segue:

1. Assicurarsi che sia in esecuzione un'istanza di SQL Server in Azure. È possibile [creare rapidamente un'istanza di SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) nel Marketplace.
2. Rivedere le [considerazione delle funzionalità](#feature-consideration) e [supporto dello scenario](#scenario_support).
3. [Esaminare le domande comuni](faq-backup-sql-server.md) su questo scenario.


## <a name="feature-consideration-and-limitations"></a>Considerazioni sulla funzionalità e limitazioni

- La macchina virtuale che esegue SQL Server richiede la connettività Internet per accedere agli indirizzi IP pubblici di Azure.
- I backup dei [gruppi di disponibilità distribuiti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) gruppi non funzionano completamente.
- Le istanze del cluster di failover di SQL Server Always On non sono supportate per il backup.
- Backup di SQL Server deve essere configurato nel portale o tramite PowerShell.
- Operazioni di backup e ripristino per i database mirror di infrastruttura di classificazione file, gli snapshot del database e i database non sono supportate.
- Non è possibile proteggere i database con un numero elevato di file. Il numero massimo di file è 1000.
- È possibile eseguire il database di SQL Server fino a circa 2000 in un insieme di credenziali. Se sono presenti altri database, creare un altro insieme di credenziali.
- È possibile configurare il backup per un massimo di 50 database in un'unica operazione; Questa restrizione consente di ottimizzare carichi di backup.
- È possibile proteggere i database con più di 1000 file.
- Dimensione database consigliato per garantire prestazioni migliori è 2TB.
- Consente di proteggere fino a 300 database per server, se si dispone di backup del log configurati per ogni 15 minuti. Il numero di database possa aumentare se la frequenza di backup è meno frequente. Si condividerà di calculatethis a breve in modo dettagliato.


## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree geografiche supportate** | Australia sud-orientale (ASE), Australia orientale (AE) <br> Brasile meridionale (BRS)<br> Canada Central (CNC), Canada East (CE)<br> Asia sud-orientale (SEA), Asia orientale (EA) <br> Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Stati Uniti centro occidentali (WCUS), Stati Uniti occidentali (WUS); Stati Uniti occidentali 2 (WUS 2) North Central US (NCUS) degli Stati Uniti centrali (CUS) meridionali (SCUS) <br> India centrale (INC), India meridionale (INS) <br> Giappone orientale (JPE), Giappone occidentale (JPW) <br> Corea centrale (KRC), Corea meridionale (KRS) <br> Europa settentrionale (NE), Europa occidentale <br> Sudafrica settentrionale (SAN), Sudafrica occidentale (SAW) <br> Emirati Arabi Uniti centrali (UAC), Emirati Arabi Uniti nord (UAN) <br> Regno Unito meridionale (UKS), Regno Unito occidentale (UKW)
**Sistemi operativi supportati** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versioni di .NET supportate** | .NET framework 4.5.2 e versioni successive installato nella macchina virtuale



## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su](backup-sql-server-database-azure-vms.md) backup dei database di SQL Server.
- [Informazioni](restore-sql-database-azure-vm.md) sul ripristino dei database SQL Server di cui è stato eseguito il backup.
- [Informazioni](manage-monitor-sql-database-backup.md) sulla gestione dei database SQL Server di cui è stato eseguito il backup.
