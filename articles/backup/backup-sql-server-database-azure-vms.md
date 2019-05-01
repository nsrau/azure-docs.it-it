---
title: Eseguire il backup dei database di SQL Server in Azure | Microsoft Docs
description: Questo tutorial spiega come eseguire il backup di SQL Server in Azure.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 08eff24dc42f594424d109b82933b01b5c1be454
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690079"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Eseguire il backup di database SQL Server in macchine virtuali di Azure

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di database SQL Server in esecuzione nelle macchine virtuali di Azure usando [Backup di Azure](backup-overview.md).

Questo articolo illustra come eseguire il backup di un database SQL Server in esecuzione in una macchina virtuale di Azure in un insieme di credenziali dei Servizi di ripristino di Backup di Azure. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare e configurare un insieme di credenziali.
> * Individuare i database e configurare i backup.
> * Configurare la protezione automatica per i database.


## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup del database di SQL Server, verificare le condizioni seguenti:

1. Identificare o [creare](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) un insieme di credenziali di servizi di ripristino nella stessa area o delle impostazioni locali della macchina virtuale che ospita l'istanza di SQL Server.
2. [Controllare le autorizzazioni VM](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) necessarie per eseguire il backup dei database SQL.
3. Verificare che la VM abbia [connettività di rete](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Verificare che i database di SQL Server siano denominati in base alle [linee guida per la denominazione](#verify-database-naming-guidelines-for-azure-backup) per Backup di Backup.
5. Verificare che non siano abilitate altre soluzioni di backup per il database. Disabilitare tutti gli altri backup di SQL Server prima di configurare questo scenario. È possibile abilitare Backup di Azure per una VM di Azure e Backup di Azure per un database SQL Server in esecuzione nella VM senza alcun conflitto.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, la macchina virtuale SQL Server richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni di macchina virtuale (individuazione del database, configurare i backup, pianificare i backup, ripristinare i punti di ripristino e così via) non senza la connettività agli indirizzi IP pubblici. Stabilire la connettività con una di queste opzioni:

- **Consentire gli intervalli IP del data center di Azure**: consentire gli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653) nel download. Per accedere a gruppo di sicurezza di rete (NSG), usare il **Set-AzureNetworkSecurityRule** cmdlet.
- **Distribuire un server proxy HTTP per il routing del traffico:** quando si esegue il backup di un database SQL Server in una macchina virtuale di Azure, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure Active Directory (Azure AD) per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. L'estensione è l'unico componente configurato per l'accesso alla rete internet pubblica.

Ciascuna opzione presenta vantaggi e svantaggi

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Senza costi aggiuntivi. | Complessità di gestione a causa della variazione degli intervalli IP nel tempo. <br/><br/> Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di Archiviazione di Azure.
Usare un proxy HTTP   | Possibilità di controllo granulare nel proxy sugli URL di archiviazione. <br/><br/> Singolo punto di accesso Internet alle VM. <br/><br/> Non è soggetto alle modifiche degli indirizzi IP di Azure. | Costi aggiuntivi per l'esecuzione di una macchina virtuale con il software proxy.

### <a name="set-vm-permissions"></a>Impostare le autorizzazioni della VM

Backup di Azure esegue una serie di operazioni quando si configura il backup per un database SQL Server:

- Aggiunge l'estensione **AzureBackupWindowsWorkload**.
- Per individuare i database nella macchina virtuale, Backup di Azure crea l'account **NT SERVICE\AzureWLBackupPluginSvc**. Questo account viene usato per il backup e il ripristino e richiede le autorizzazioni sysadmin SQL.
- Backup di Azure sfrutta l'account **NT AUTHORITY\SYSTEM** per l'individuazione o l'interrogazione dei database, quindi questo account deve essere un account di accesso pubblico in SQL.

Se la VM SQL Server non è stata creata da Azure Marketplace, è possibile ricevere un errore **UserErrorSQLNoSysadminMembership**. In questo caso [seguire questa istruzioni](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificare le linee guida per la denominazione dei database per Backup di Azure

Evitare di seguito per i nomi di database:

  * Spazi finali/iniziali
  * Punto esclamativo (!) finale
  * Parentesi quadra chiusa ']'
  * Nomi di database iniziano con "F:\"

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

   - Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
   - Se una VM non è elencata come previsto, verificare se ne è già stato eseguito il backup in un insieme di credenziali.
   - Più macchine virtuali possono avere lo stesso nome, ma in questo caso appartengono a gruppi di risorse differenti.

     ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Tenere traccia dell'individuazione dei database nell'area **Notifiche**. A seconda del numero di database presenti nella macchina virtuale, il completamento del processo può richiedere del tempo. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione di seguito si verifica in background:

    - Backup di Azure registra la VM con l'insieme di credenziali per il backup del carico di lavoro. Tutti i database presenti nella VM registrata possono essere sottoposti a backup solo in questo insieme di credenziali.
    - Backup di Azure installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. Nel database SQL non viene installato alcun agente.
    - Backup di Azure crea l'account del servizio **NT Service\AzureWLBackupPluginSvc** nella macchina virtuale.
      - Tutte le operazioni di backup e ripristino usano l'account del servizio.
      - Per **NT Service\AzureWLBackupPluginSvc** sono necessarie le autorizzazioni sysadmin SQL. Tutte le macchine virtuali SQL Server create in Azure Marketplace vengono fornite con **SqlIaaSExtension** installato. L'estensione **AzureBackupWindowsWorkload** utilizza l'estensione **SQLIaaSExtension** per ottenere automaticamente le autorizzazioni richieste.
    - Se la VM non è stata creata dal Marketplace, la VM non ha **SqlIaaSExtension** installata e l'operazione di individuazione ha esito negativo con il messaggio di errore **UserErrorSQLNoSysAdminMembership**. Seguire le [istruzioni](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) per risolvere il problema.

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurare il backup  

Configurare il backup come segue:

1. In **Obiettivo di backup** selezionare **Configura backup**.

   ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Fare clic su **configurare Backup**, il **Seleziona elementi per backup** verrà visualizzato il pannello. Elenca tutti i gruppi di disponibilità registrato e istanze di SQL Server autonomo. Espandere la freccia di espansione a sinistra della riga per visualizzare tutti i database non protetti in tale istanza o sempre in gruppo di disponibilità.  

    ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selezionare tutti i database da proteggere e fare clic su **OK**.

   ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

   Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

     * Per proteggere più di 50 database, configurare più backup.
     * In alternativa, è possibile abilitare la [protezione automatica](#enable-auto-protection) sull'intera istanza o sul gruppo di disponibilità AlwaysOn selezionando l'opzione **ON** nell'elenco a discesa corrispondente nella colonna **AUTOPROTECT**. La funzionalità di [protezione automatica](#enable-auto-protection) non solo abilita la protezione per tutti i database esistenti in un'unica operazione, ma protegge anche automaticamente i nuovi database che verranno aggiunti in futuro a tale istanza o gruppo di disponibilità.  

4. Fare clic su **OK** per aprire il **criteri di Backup** pannello.

    ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Nelle **scegliere i criteri di backup**, selezionare un criterio, quindi fare clic su **OK**.

   - Selezionare i criteri predefiniti: HourlyLogBackup.
   - Scegliere un criterio di backup creato in precedenza per SQL.
   - Definire un nuovo criterio basato sull'intervallo di conservazione e RPO.

     ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Sul **Backup** dal menu **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

7. Tenere traccia dello stato di configurazione nel **notifiche** area del portale.

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
2. Nel menu **Aggiungi** fare clic su **SQL Server nella macchina virtuale di Azure**. Per definisce il tipo di criteri.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

3. In **Nome criterio** immettere un nome per il nuovo criterio.
4. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.

   - Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup.
   - È necessario eseguire un backup completo perché non è possibile disattivare la **Full Backup** opzione.
   - Fare clic su **Backup completo** per visualizzare il criterio.
   - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
   - Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.

     ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Per **Intervallo di conservazione**, per impostazione predefinita sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti di intervallo di conservazione che non si vogliono usare e impostare gli intervalli da usare.

    - Periodo di memorizzazione minimo per qualsiasi tipo di backup (completa/differenziale/log) è 7 giorni.
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


### <a name="modify-policy"></a>Modificare i criteri
Modificare i criteri per modificare l'intervallo di frequenza o di conservazione dei backup.

> [!NOTE]
> Qualsiasi modifica al periodo di conservazione verrà applicata a tutti i punti di ripristino meno recenti oltre a quelli nuovi retroattivamente.

Nel dashboard dell'insieme di credenziali, andare al **Manage** > **criteri di Backup** e scegliere il criterio da modificare.

  ![Gestire i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Abilitare la protezione automatica  

Abilitare la protezione automatica eseguire automaticamente il backup di database di tutti i valori esistenti e che verranno aggiunti in futuro a un'istanza di SQL Server autonomo o un AlwaysOn di SQL Server nel gruppo di disponibilità.

- Non sono previsti limiti al numero di database che è possibile selezionare per la protezione automatica in un'unica operazione.
- In modo selettivo non è possibile proteggere o escludere i database dalla protezione dati in un'istanza al momento dell'abilitazione della protezione automatica.
- Se l'istanza include già alcuni database protetti, ma continueranno a essere protette con i rispettivi criteri anche dopo l'attivazione della protezione automatica. Tuttavia, tutti i database protetti e i database che verranno aggiunti in futuro, sarà necessario solo un singolo criterio che al momento dell'abilitazione della protezione automatica, in cui definisce **configurare il Backup**. Tuttavia, è possibile modificare i criteri associati a un database di protezione automatica in un secondo momento.  

Come indicato di seguito sono riportati i passaggi per abilitare la protezione automatica:

  1. In **Elementi per backup** selezionare l'istanza per la quale si vuole abilitare la protezione automatica.
  2. Selezionare il menu a discesa in **Protezione automatica** e scegliere **Sì**. Fare quindi clic su **OK**.

      ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Il backup è configurato per tutti i database insieme e può essere monitorato in **Processi di backup**.

Se è necessario disabilitare la protezione automatica, fare clic sul nome di istanza sotto **configurare il Backup**e selezionare **disattiva protezione automatica** per l'istanza. Tutti i database continuerà a eseguire il backup, ma non verranno protetti automaticamente database futuri.

![Disabilitare la protezione automatica per l'istanza](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](restore-sql-database-azure-vm.md) sul ripristino dei database SQL Server di cui è stato eseguito il backup.
- [Informazioni](manage-monitor-sql-database-backup.md) sulla gestione dei database SQL Server di cui è stato eseguito il backup.
