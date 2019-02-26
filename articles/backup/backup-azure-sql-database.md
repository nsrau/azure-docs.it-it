---
title: Eseguire il backup dei database di SQL Server in Azure | Microsoft Docs
description: Questo tutorial spiega come eseguire il backup di SQL Server in Azure. L'articolo spiega inoltre il recupero di SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 17ec7723044cec391ebe390bbcfba3aa6f2f29ca
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446852"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Eseguire il backup di database SQL Server in macchine virtuali di Azure 

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di database SQL Server in esecuzione nelle macchine virtuali di Azure usando [Backup di Azure](backup-overview.md). 

Questo articolo illustra come eseguire il backup di un database SQL Server in esecuzione in una macchina virtuale di Azure in un insieme di credenziali dei Servizi di ripristino di Backup di Azure. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Verificare i prerequisiti per il backup di un'istanza di SQL Server.
> * Creare e configurare un insieme di credenziali.
> * Individuare i database e configurare i backup.
> * Configurare la protezione automatica per i database.

> [!NOTE]
> Questa funzionalità è attualmente in anteprima pubblica.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare quanto segue:

1. Assicurarsi che sia in esecuzione un'istanza di SQL Server in Azure. È possibile [creare rapidamente un'istanza di SQL Server](../sql-database/sql-database-get-started-portal.md) nel Marketplace.
2. Esaminare le limitazioni seguenti dell'anteprima pubblica.
3. Esaminare il supporto degli scenari.
4. [Esaminare le domande comuni](faq-backup-sql-server.md) su questo scenario.


## <a name="preview-limitations"></a>Limiti di anteprima

Questa anteprima pubblica ha una serie di limitazioni.

- La macchina virtuale che esegue SQL Server richiede la connettività Internet per accedere agli indirizzi IP pubblici di Azure. 
- È possibile eseguire il backup fino a un massimo di 2000 database SQL Server in un insieme di credenziali. Se sono presenti altri database, creare un altro insieme di credenziali. 
- I backup di [gruppi di disponibilità distribuiti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) non funzionano completamente.
- Le istanze del cluster di failover di SQL Server Always On non sono supportate per il backup.
- Il backup di SQL Server deve essere configurato nel portale. Al momento non è possibile configurare il backup con Azure PowerShell, l'interfaccia della riga di comando o le API REST.
- Le operazioni di backup/ripristino per gli snapshot del database, i database e i database mirror di istanze del cluster di failover non sono supportate.
- Non è possibile proteggere i database con un numero elevato di file. Il numero massimo di file supportati non è deterministico. Non dipende solo dal numero di file, ma anche dalla lunghezza del loro percorso. 

Per ulteriori dettagli sugli scenari supportati e non supportati, vedere la [sezione delle domande frequenti](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq).

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree geografiche supportate** | Australia sud-orientale (ASE); Brasile meridionale (BRS); Canada centrale (CNC); Canada orientale (CE); Stati Uniti centrali (CUS); Asia orientale (EA); Australia orientale (AE); Stati Uniti orientali (EUS); Stati Uniti orientali 2 (EUS2); India centrale (INC); India meridionale (INS); Giappone orientale (JPE); Giappone occidentale (JPW); Corea centrale (KRC); Corea meridionale (KRS); Stati Uniti centro-settentrionali (NCUS); Europa settentrionale (NE); Stati Uniti centro-meridionali (SCUS); Sud-est asiatico (SEA); Regno Unito meridionale (UKS); Regno Unito occidentale (UKW); Stati Uniti centro-occidentali (WCUS); Europa occidentale (WE); Stati Uniti occidentali (WUS); Stati Uniti occidentali 2 (WUS 2)
**Sistemi operativi supportati** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.


## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup del database di SQL Server, verificare le condizioni seguenti:

1. Identificare o [creare](backup-azure-sql-database.md#create-a-recovery-services-vault) un insieme di credenziali di Servizi di ripristino nella stessa area o nelle stesse impostazioni locali della macchina virtuale che ospita l'istanza di SQL Server.
2. [Controllare le autorizzazioni VM](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) necessarie per eseguire il backup dei database SQL.
3. Verificare che la VM abbia [connettività di rete](backup-azure-sql-database.md#establish-network-connectivity).
4. Verificare che i database di SQL Server siano denominati in base alle [linee guida per la denominazione](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) per Backup di Backup.
5. Verificare che non siano abilitate altre soluzioni di backup per il database. Disabilitare tutti gli altri backup di SQL Server prima di configurare questo scenario. È possibile abilitare Backup di Azure per una VM di Azure e Backup di Azure per un database SQL Server in esecuzione nella VM senza alcun conflitto.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, la macchina virtuale SQL Server richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale, ad esempio individuazione dei database, configurazione e pianificazione dei backup, ripristino dei punti di ripristino e così via, non riescono in assenza di connettività agli indirizzi IP pubblici. Stabilire la connettività con una di queste opzioni:

- **Consentire gli intervalli IP del data center di Azure**: consentire gli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653) nel download. Per l'accesso in un gruppo di sicurezza di rete usare il cmdlet **Set-AzureNetworkSecurityRule**.
- **Distribuire un server proxy HTTP per il routing del traffico:** quando si esegue il backup di un database SQL Server in una macchina virtuale di Azure, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure Active Directory (Azure AD) per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. L'unico componente configurato per l'accesso a Internet pubblico è l'estensione.

Ogni opzione presenta vantaggi e svantaggi

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Senza costi aggiuntivi. | Complessità di gestione a causa della variazione degli intervalli IP nel tempo. <br/><br/> Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di Archiviazione di Azure.
Usare un proxy HTTP   | Possibilità di controllo granulare nel proxy sugli URL di archiviazione. <br/><br/> Singolo punto di accesso Internet alle VM. <br/><br/> Non è soggetto alle modifiche degli indirizzi IP di Azure. | Costi aggiuntivi per l'esecuzione di una macchina virtuale con il software proxy. 

### <a name="set-vm-permissions"></a>Impostare le autorizzazioni della VM

Backup di Azure esegue una serie di operazioni quando si configura il backup per un database SQL Server:

- Aggiunge l'estensione **AzureBackupWindowsWorkload**.
- Per individuare i database nella macchina virtuale, Backup di Azure crea l'account **NT SERVICE\AzureWLBackupPluginSvc**. Questo account viene usato per il backup e il ripristino e richiede le autorizzazioni sysadmin SQL.
- Backup di Azure sfrutta l'account **NT AUTHORITY\SYSTEM** per l'individuazione o l'interrogazione dei database, quindi questo account deve essere un account di accesso pubblico in SQL.

Se la VM SQL Server non è stata creata da Azure Marketplace, è possibile ricevere un errore **UserErrorSQLNoSysadminMembership**. In tal caso [seguire queste istruzioni](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificare le linee guida per la denominazione dei database per Backup di Azure

Evitare quanto segue per i nomi dei database:

  * Spazi finali/iniziali
  * Punto esclamativo (!) finale
  * Parentesi quadra chiusa ']'

È previsto l'uso di alias per i caratteri non supportati dalle tabelle di Azure, ma è consigliabile evitare tali caratteri. [Altre informazioni](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Individuare i database in esecuzione nella macchina virtuale. 
1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per il backup del database.

2. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Backup**. 

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** su **Azure** (valore predefinito).

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


5. In **Obiettivo di backup** > **Individuare i database nelle macchine virtuali** selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Questa operazione può richiedere del tempo, a seconda del numero di macchine virtuali non protette nella sottoscrizione.

    ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Tenere traccia dell'individuazione dei database nell'area **Notifiche**. A seconda del numero di database presenti nella macchina virtuale, il completamento del processo può richiedere del tempo. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
    - Se una VM non è elencata come previsto, verificare se ne è già stato eseguito il backup in un insieme di credenziali.
    - Più macchine virtuali possono avere lo stesso nome, ma in questo caso appartengono a gruppi di risorse differenti. 

9. Selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**. 
10. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione vengono eseguite queste operazioni in background:

    - Backup di Azure registra la VM con l'insieme di credenziali per il backup del carico di lavoro. Tutti i database presenti nella VM registrata possono essere sottoposti a backup solo in questo insieme di credenziali.
    - Backup di Azure installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. Nel database SQL non viene installato alcun agente.
    - Backup di Azure crea l'account del servizio **NT Service\AzureWLBackupPluginSvc** nella macchina virtuale.
      - Tutte le operazioni di backup e ripristino usano l'account del servizio.
      - Per **NT Service\AzureWLBackupPluginSvc** sono necessarie le autorizzazioni sysadmin SQL. Tutte le macchine virtuali SQL Server create in Azure Marketplace vengono fornite con **SqlIaaSExtension** installato. L'estensione **AzureBackupWindowsWorkload** utilizza l'estensione **SQLIaaSExtension** per ottenere automaticamente le autorizzazioni richieste.
    - Se la VM non è stata creata dal Marketplace, la VM non ha **SqlIaaSExtension** installata e l'operazione di individuazione ha esito negativo con il messaggio di errore **UserErrorSQLNoSysAdminMembership**. Seguire le istruzioni riportate in [#fix-sql-sysadmin-permissions] per risolvere questo problema.

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup 

Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

- Per proteggere più di 50 database, configurare più backup.
- In alternativa è possibile abilitare la protezione automatica. La protezione automatica protegge tutti database esistenti contemporaneamente e protegge automaticamente i nuovi database aggiunti all'istanza del gruppo di disponibilità.


Configurare il backup come segue:

1. Nel dashboard dell'insieme di credenziali selezionare **Backup**. 

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** su **Azure**.

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. In **Obiettivo di backup** selezionare **Configura backup**.

    ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Selezionare tutti i database da proteggere e fare clic su **OK**.

    ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Verranno visualizzate tutte le istanze di SQL Server (gruppi autonomi e di disponibilità).
    - Selezionare la freccia verso il basso a sinistra del nome dell'istanza/del gruppo di disponibilità da filtrare.

7. Nel menu **Backup** selezionare **Criteri di backup**. 

    ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

8. In **Scegliere i criteri di backup** selezionare un criterio e quindi fare clic su **OK**.

    - Selezionare i criteri predefiniti: **HourlyLogBackup**.
    - Scegliere un criterio di backup creato in precedenza per SQL.
    - [Definire un nuovo criterio](backup-azure-sql-database.md#define-a-backup-policy) basato sull'obiettivo del punto di recupero (RPO) e sull'intervallo di conservazione.
    - Durante l'anteprima non è possibile modificare un criterio di backup esistente.
    
9. Nel **menu Backup** selezionare **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

10. È possibile monitorare l'avanzamento della configurazione nell'area delle **notifiche** del portale.

    ![Area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati. 

- Un criterio viene creato a livello di insieme di credenziali.
- Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.
- Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita.
- È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo.
- [Informazioni](backup-architecture.md#sql-server-backup-types) sui vari tipi criteri di backup.


Per creare un criterio di backup:

1. Nell'insieme di credenziali fare clic su **Criteri di Backup** > **Aggiungi**.
2. Nel menu **Aggiungi** fare clic su **SQL Server nella macchina virtuale di Azure**. Ciò definisce il tipo di criterio.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

3. In **Nome criterio** immettere un nome per il nuovo criterio. 
4. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.

    - Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup.
    - È necessario eseguire un backup completo. Non è possibile disattivare l'opzione **Backup completo**.
    - Fare clic su **Backup completo** per visualizzare il criterio. 
    - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
    - Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.

    ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Per **Intervallo di conservazione**, per impostazione predefinita sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti di intervallo di conservazione che non si vogliono usare e impostare gli intervalli da usare. 

    - I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    - Il backup del giorno specifico viene contrassegnato e conservato in base all'intervallo di conservazione settimanale e alla conservazione settimanale.
    - L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

   ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

    

6. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.
7. Per aggiungere un criterio di backup differenziale, selezionare **Backup differenziale**. 

   ![Impostazioni dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Aprire il menu del criterio di backup differenziale](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione. 

    - Al massimo, è possibile attivare un backup differenziale al giorno.
    - I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

9. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

10. Per aggiungere un criterio di backup del log transazionale, selezionare **Backup del log**.
11. In **Backup del log** selezionare **Abilita** e quindi impostare le opzioni di frequenza e conservazione. I backup del log possono verificarsi ogni 15 minuti ed essere conservati per un massimo di 35 giorni.
12. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

   ![Modificare i criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Nel menu **Criterio di backup**, scegliere se abilitare la **compressione dei backup SQL**.
    - La compressione è disabilitata per impostazione predefinita.
    - Nel back-end Backup di Azure usa la compressione di backup nativa di SQL.

14. Dopo aver completato le modifiche ai criteri di backup, selezionare **OK**.



## <a name="enable-auto-protection"></a>Abilitare la protezione automatica  

Abilitare la protezione automatica per eseguire automaticamente il backup di tutti i database esistenti e di quelli che verranno aggiunti in futuro a un'istanza autonoma di SQL Server o a un gruppo di disponibilità AlwaysOn di SQL Server. 

- Quando si attiva la protezione automatica e si seleziona un criterio, i database protetti esistenti continueranno a usare il criterio precedente.
- Non c'è limite al numero di database che si possono selezionare contemporaneamente per la protezione automatica.

Abilitare la protezione automatica come segue:

1. In **Elementi per backup** selezionare l'istanza per la quale si vuole abilitare la protezione automatica.
2. Selezionare il menu a discesa in **Protezione automatica** e scegliere **Sì**. Fare quindi clic su **OK**.

    ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

3. Il backup è configurato per tutti i database insieme e può essere monitorato in **Processi di backup**.


Se è necessario disabilitare la protezione automatica, fare clic sul nome dell'istanza in **Configura backup** e selezionare **Disabilita protezione automatica** per l'istanza. Tutti i database continueranno a essere sottoposti a backup. I database futuri non verranno tuttavia protetti automaticamente.

![Disabilitare la protezione automatica per l'istanza](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Correzione delle autorizzazioni sysadmin SQL

Se è necessario correggere le autorizzazioni a causa di un errore **UserErrorSQLNoSysadminMembership**, seguire questa procedura:

1. Usare un account con le autorizzazioni sysadmin SQL Server per accedere a SQL Server Management Studio (SSMS). A meno che non siano necessarie autorizzazioni speciali, l'autenticazione di Windows dovrebbe funzionare.
2. In SQL Server aprire la cartella **Sicurezza/Account di accesso**.

    ![Aprire la cartella Sicurezza/Account di accesso per vedere gli account](./media/backup-azure-sql-database/security-login-list.png)

3. Fare clic con il pulsante destro del mouse sulla cartella **Account di accesso** e scegliere **Nuovo account di accesso**. In **Account di accesso - Nuovo** selezionare **Cerca**.

    ![Nella finestra di dialogo Account di accesso - Nuovo selezionare Cerca](./media/backup-azure-sql-database/new-login-search.png)

3. L'account di servizio virtuale di Windows **NT SERVICE\AzureWLBackupPluginSvc** è stato creato durante la fase di registrazione della macchina virtuale e di individuazione SQL. Immettere il nome dell'account come mostrato nella casella **Immettere il nome dell'oggetto da selezionare**. Selezionare **Controlla nomi** per risolvere il nome. Fare clic su **OK**.

    ![Selezionare Controlla nomi per risolvere il nome del servizio sconosciuto](./media/backup-azure-sql-database/check-name.png)


4. In **Ruoli server** assicurarsi che sia selezionato il ruolo **sysadmin**. Fare clic su **OK**. Le autorizzazioni necessarie a questo punto devono essere presenti.

    ![Verificare che sia selezionato il ruolo del server sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. Associare ora il database all'insieme di credenziali di Servizi di ripristino. Nell'elenco dei **server protetti** del portale di Azure fare clic con il pulsante destro del mouse sul server in stato di errore e scegliere **Individua di nuovo i database**.

    ![Verificare che il server abbia le autorizzazioni appropriate](./media/backup-azure-sql-database/check-erroneous-server.png)

6. Verificare l'avanzamento nell'area **Notifiche**. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

In alternativa, è possibile abilitare la [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) sull'intera istanza o sul gruppo di disponibilità AlwaysOn selezionando l'opzione **ON** nell'elenco a discesa corrispondente nella colonna **AUTOPROTECT**. La funzionalità di [protezione automatica](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) non solo abilita la protezione per tutti i database esistenti in un'unica operazione, ma protegge anche automaticamente i nuovi database che verranno aggiunti in futuro a tale istanza o gruppo di disponibilità.  

   ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](restore-sql-database-azure-vm.md) sul ripristino dei database SQL Server di cui è stato eseguito il backup.
- [Informazioni](manage-monitor-sql-database-backup.md) sulla gestione dei database SQL Server di cui è stato eseguito il backup.

