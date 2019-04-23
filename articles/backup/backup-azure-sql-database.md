---
title: Eseguire il backup dei database di SQL Server in Azure | Microsoft Docs
description: Questo tutorial spiega come eseguire il backup di SQL Server in Azure. L'articolo spiega inoltre il recupero di SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847307"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informazioni sul backup di SQL Server in macchine virtuali di Azure

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di database SQL Server in esecuzione nelle macchine virtuali di Azure usando [Backup di Azure](backup-overview.md).

## <a name="backup-process"></a>Processo di backup

Questa soluzione sfrutta le API native SQL per eseguire i backup dei database SQL.

* Dopo aver specificato la macchina virtuale di SQL Server da proteggere e aver eseguito la query per trovare i database al suo interno, il servizio Backup di Azure installerà un'estensione di backup di carichi di lavoro nella VM denominata `AzureBackupWindowsWorkload` extension.
* Questa estensione è costituita da un coordinatore e da un plug-in SQL. Mentre il coordinatore è responsabile di attivare i flussi di lavoro per varie operazioni, come la configurazione del backup, il backup e il ripristino, il plug-in gestisce il flusso di dati effettivo.
* Per individuare i database in questa VM, Backup di Azure crea l'account  `NT SERVICE\AzureWLBackupPluginSvc`. Questo account viene usato per il backup e il ripristino e richiede le autorizzazioni sysadmin SQL. Backup di Azure sfrutta l'account  `NT AUTHORITY\SYSTEM`  per l'individuazione o l'interrogazione dei database, quindi questo account deve essere un account di accesso pubblico in SQL. Se la VM di SQL Server non è stata creata da Azure Marketplace, si potrebbe ricevere un errore  **UserErrorSQLNoSysadminMembership**. In tal caso  [seguire queste istruzioni](backup-azure-sql-database.md).
* Dopo aver attivato la configurazione della protezione nei database selezionati, il servizio di backup configura il coordinatore con le pianificazioni di backup e altri dettagli sui criteri, che l'estensione memorizza nella cache locale della VM. 
* Nell'orario pianificato il coordinatore comunica con il plug-in, che avvia lo streaming dei dati di backup dal server SQL tramite VDI.  
* Il plug-in invia i dati direttamente all'insieme di credenziali dei servizi di ripristino, eliminando così la necessità di una posizione per la gestione temporanea. I dati vengono crittografati e archiviati dal servizio Backup di Azure negli account di archiviazione.
* Al termine del trasferimento dei dati, il coordinatore conferma il commit con il servizio di backup.

  ![Architettura del backup SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare quanto segue:

1. Assicurarsi che sia in esecuzione un'istanza di SQL Server in Azure. È possibile [creare rapidamente un'istanza di SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) nel Marketplace.
2. Vedere le [considerazioni sulla funzionalità](#feature-consideration-and-limitations) e il [supporto degli scenari](#scenario-support).
3. [Esaminare le domande comuni](faq-backup-sql-server.md) su questo scenario.

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree geografiche supportate** | Australia sud-orientale (ASE), Australia orientale (AE) <br> Brasile meridionale (BRS)<br> Canada centrale (CNC), Canada orientale (CE)<br> Asia sud-orientale (SEA), Asia orientale (EA) <br> Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Stati Uniti centro-occidentali (WCUS), Stati Uniti occidentali (WUS), Stati Uniti occidentali 2 (WUS 2), Stati Uniti centro-settentrionali (NCUS), Stati Uniti centrali (CUS), Stati Uniti centro-meridionali (SCUS) <br> India centrale (INC), India meridionale (INS) <br> Giappone orientale (JPE), Giappone occidentale (JPW) <br> Corea centrale (KRC), Corea meridionale (KRS) <br> Europa settentrionale (NE), Europa occidentale <br> Regno Unito meridionale (UKS), Regno Unito occidentale (UKW)
**Sistemi operativi supportati** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versioni di .NET supportate** | .NET Framework 4.5.2 e versioni successive installato nella VM

## <a name="feature-consideration-and-limitations"></a>Considerazioni e limitazioni della funzionalità

- Il backup di SQL Server può essere configurato nel portale di Azure o in **PowerShell**. L'interfaccia della riga di comando non è supportata.
- La macchina virtuale che esegue SQL Server richiede la connettività Internet per accedere agli indirizzi IP pubblici di Azure.
- L'**Istanza del cluster di failover** di SQL Server e l'istanza del cluster di failover AlwaysOn di SQL Server.
- Le operazioni di backup e ripristino per i database mirror e gli snapshot di database non sono supportate.
- L'uso di più soluzioni per eseguire il backup dell'istanza di SQL Server autonoma o del gruppo di disponibilità Always On di SQL potrebbe generare un errore di backup. Evitare questo approccio.
- Anche il backup di due nodi di un gruppo di disponibilità singolarmente con soluzioni uguali o differenti potrebbe generare errori. Backup di Azure è in grado di rilevare e proteggere tutti i nodi che si trovano nella stessa area dell'insieme di credenziali. Se il gruppo di disponibilità Always On di SQL Server si estende in più aree di Azure, configurare il backup dall'area che include il nodo primario. Backup di Azure è in grado di rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup.  
- Backup di Azure supporta solo i tipi di backup completo e completo solo copia per i database di **sola lettura**
- Non è possibile proteggere i database con un numero elevato di file. Il numero massimo di file supportato è **~1000**.  
- È possibile eseguire il backup di un totale di **~2000** database SQL Server in un insieme di credenziali. Nel caso di un numero elevato di database, è possibile creare più insiemi di credenziali.
- È possibile configurare il backup per un totale di **50** database alla volta. Questa restrizione contribuisce a ottimizzare i carichi di backup.
- Sono supportati database di dimensioni fino a **2 TB**. Per dimensioni maggiori, potrebbero verificarsi problemi di prestazioni.
- Per avere un'idea della quantità di database che è possibile proteggere per ogni server, è necessario considerare fattori come la larghezza di banda, le dimensioni delle VM, la frequenza dei backup, le dimensioni dei database e così via. È in fase di sviluppo uno strumento di pianificazione che risulterà utile per calcolare autonomamente questo numero. Verrà pubblicato prossimamente.
- Nel caso dei gruppi di disponibilità, i backup vengono eseguiti da diversi nodi in base ad alcuni fattori. Il comportamento di backup per un gruppo di disponibilità è riepilogato di seguito.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Comportamento di backup nel caso di gruppi di disponibilità Always On

In base alle preferenze e ai tipi di backup (completo/differenziale/log/completo solo copia), i backup vengono eseguiti da uno specifico nodo (primario o secondario).

- **Preferenza di backup: Primario**

**Tipo di backup** | **Node**
    --- | ---
    Completa | Primaria
    Differenziale | Primaria
    Log |  Primaria
    Completo solo copia |  Primaria

- **Preferenza di backup: Solo secondario**

**Tipo di backup** | **Node**
--- | ---
Completa | Primaria
Differenziale | Primaria
Log |  Secondario
Completo solo copia |  Secondario

- **Preferenza di backup: Secondario**

**Tipo di backup** | **Node**
--- | ---
Completa | Primaria
Differenziale | Primaria
Log |  Secondario
Completo solo copia |  Secondario

- **Nessuna preferenza di backup**

**Tipo di backup** | **Node**
--- | ---
Completa | Primaria
Differenziale | Primaria
Log |  Secondario
Completo solo copia |  Secondario

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

- [Informazioni](backup-sql-server-database-azure-vms.md) sul backup dei database di SQL Server.
- [Informazioni](restore-sql-database-azure-vm.md) sul ripristino dei database SQL Server di cui è stato eseguito il backup.
- [Informazioni](manage-monitor-sql-database-backup.md) sulla gestione dei database SQL Server di cui è stato eseguito il backup.
