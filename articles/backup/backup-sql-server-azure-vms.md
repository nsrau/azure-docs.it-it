---
title: Informazioni su SQL Server macchina virtuale di Azure con Backup di Azure | Microsoft Docs
description: Informazioni sui database SQL Server in macchine Virtuali di Azure e funzionalità tenendo in considerazione questa funzionalità.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: 36a3a50ffaf3f8dab068f067ec0440149e7a58a1
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226641"
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
2. Rivedere le [considerazione delle funzionalità](#feature-consideration-and-limitations) e [supporto dello scenario](#scenario_support).
3. [Esaminare le domande comuni](faq-backup-sql-server.md) su questo scenario.

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree geografiche supportate** | Australia sud-orientale (ASE), Australia orientale (AE) <br> Brasile meridionale (BRS)<br> Canada Central (CNC), Canada East (CE)<br> Asia sud-orientale (SEA), Asia orientale (EA) <br> Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Stati Uniti centro occidentali (WCUS), Stati Uniti occidentali (WUS); Stati Uniti occidentali 2 (WUS 2) North Central US (NCUS) degli Stati Uniti centrali (CUS) meridionali (SCUS) <br> India centrale (INC), India meridionale (INS) <br> Giappone orientale (JPE), Giappone occidentale (JPW) <br> Corea centrale (KRC), Corea meridionale (KRS) <br> Europa settentrionale (NE), Europa occidentale <br> Regno Unito meridionale (UKS), Regno Unito occidentale (UKW)
**Sistemi operativi supportati** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versioni di .NET supportate** | .NET framework 4.5.2 e versioni successive installato nella macchina virtuale

## <a name="feature-consideration-and-limitations"></a>Considerazioni sulla funzionalità e limitazioni

- Backup di SQL Server può essere configurato nel portale di Azure oppure **PowerShell**. Non Supportiamo CLI.
- Macchina virtuale che esegue SQL Server richiede la connettività internet per accedere a indirizzi IP pubblici di Azure.
- SQL Server AlwaysOn **istanze del Cluster di Failover (FCI)** non sono supportati.
- Non sono supportate le operazioni di backup e ripristino per i database di mirroring e snapshot del database.
- Utilizzando più soluzioni di backup per eseguire il backup di SQL Server autonomo istanza o sempre SQL nel gruppo di disponibilità potrebbe comporta errori di backup; evitare di questa operazione.
- Backup di due nodi di un gruppo di disponibilità singolarmente con soluzioni uguali o diverse, possono causare errori di backup. Backup di Azure può rilevare e proteggere tutti i nodi che si trovano nella stessa area dell'insieme di credenziali. Se l'AlwaysOn di SQL Server nel gruppo di disponibilità si estende su più aree di Azure, configurare il backup dall'area che contiene il nodo primario. Backup di Azure può rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup.  
- Backup di Azure supporta solo completo e i tipi di sola copia backup completo per **Read-only** database
- Non è possibile proteggere i database con un numero elevato di file. È il numero massimo di file supportato **~ 1000**.  
- È possibile eseguire il backup **~ 2000** i database di SQL Server in un insieme di credenziali. È possibile creare più insiemi di credenziali nel caso in cui si dispone di un maggior numero di database.
- È possibile configurare un massimo di backup per **50** dei database in uno da inserire; questa restrizione consente di ottimizzare carichi di backup.
- Supportiamo i database fino a **2TB** dimensioni; per maggiore di quello delle dimensioni, i problemi di prestazioni potrebbero avviarsi.
- Per avere un'idea per quanto riguarda il numero di database può essere protetti per ogni server, è necessario prendere in considerazione fattori quali la larghezza di banda, le dimensioni della macchina virtuale, frequenza di backup, dimensioni del database e così via. Microsoft sta lavorando a una pianificazione che può essere utile per calcolare questi numero su possedute. Si verrà eseguita la pubblicazione, a breve.
- In caso di gruppi di disponibilità, i backup vengono eseguiti da nodi diversi in base a una serie di fattori. Di seguito viene riepilogato il comportamento di backup per un gruppo di disponibilità.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Comportamento di backup in caso di sempre i gruppi di disponibilità

A seconda della preferenza di backup e i tipi di backup (completa/differenziale/log/solo copia completi), i backup vengono eseguiti da un nodo specifico (principale/secondario).

- **Preferenze di backup: Primario**

**Tipo di backup** | **Node**
    --- | ---
    Completa | Primaria
    Backup differenziale | Primaria
    Log |  Primaria
    Completo di sola copia |  Primaria

- **Preferenze di backup: Solo secondario**

**Tipo di backup** | **Node**
--- | ---
Completa | Primaria
Backup differenziale | Primaria
Log |  Secondario
Completo di sola copia |  Secondario

- **Preferenze di backup: Database secondario**

**Tipo di backup** | **Node**
--- | ---
Completa | Primaria
Backup differenziale | Primaria
Log |  Secondario
Completo di sola copia |  Secondario

- **Nessuna preferenza di Backup**

**Tipo di backup** | **Node**
--- | ---
Completa | Primaria
Backup differenziale | Primaria
Log |  Secondario
Completo di sola copia |  Secondario

## <a name="fix-sql-sysadmin-permissions"></a>Correzione delle autorizzazioni sysadmin SQL

  Se è necessario correggere le autorizzazioni a causa di un errore **UserErrorSQLNoSysadminMembership**, seguire questa procedura:

  1. Usare un account con le autorizzazioni sysadmin SQL Server per accedere a SQL Server Management Studio (SSMS). A meno che non siano necessarie autorizzazioni speciali, l'autenticazione di Windows dovrebbe funzionare.
  2. In SQL Server aprire la cartella **Sicurezza/Account di accesso**.

      ![Aprire la cartella Sicurezza/Account di accesso per vedere gli account](./media/backup-azure-sql-database/security-login-list.png)

  3. Fare clic con il pulsante destro del mouse sulla cartella **Account di accesso** e scegliere **Nuovo account di accesso**. In **Account di accesso - Nuovo** selezionare **Cerca**.

      ![Nella finestra di dialogo Account di accesso - Nuovo selezionare Cerca](./media/backup-azure-sql-database/new-login-search.png)

  4. L'account di servizio virtuale di Windows **NT SERVICE\AzureWLBackupPluginSvc** è stato creato durante la fase di registrazione della macchina virtuale e di individuazione SQL. Immettere il nome dell'account come mostrato nella casella **Immettere il nome dell'oggetto da selezionare**. Selezionare **Controlla nomi** per risolvere il nome. Fare clic su **OK**.

      ![Selezionare Controlla nomi per risolvere il nome del servizio sconosciuto](./media/backup-azure-sql-database/check-name.png)

  5. In **Ruoli server** assicurarsi che sia selezionato il ruolo **sysadmin**. Fare clic su **OK**. Le autorizzazioni necessarie a questo punto devono essere presenti.

      ![Verificare che sia selezionato il ruolo del server sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Associare ora il database all'insieme di credenziali di Servizi di ripristino. Nell'elenco dei **server protetti** del portale di Azure fare clic con il pulsante destro del mouse sul server in stato di errore e scegliere **Individua di nuovo i database**.

      ![Verificare che il server abbia le autorizzazioni appropriate](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verificare l'avanzamento nell'area **Notifiche**. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

      ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su](backup-sql-server-database-azure-vms.md) backup dei database di SQL Server.
- [Informazioni](restore-sql-database-azure-vm.md) sul ripristino dei database SQL Server di cui è stato eseguito il backup.
- [Informazioni](manage-monitor-sql-database-backup.md) sulla gestione dei database SQL Server di cui è stato eseguito il backup.
