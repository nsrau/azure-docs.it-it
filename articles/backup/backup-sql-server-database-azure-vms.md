---
title: Backup dei database di SQL Server in macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come eseguire il backup dei database di SQL Server in macchine virtuali di Azure
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 2fba8b0056c80a62837682a6820b68f71fba9ea8
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952945"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Eseguire il backup di database SQL Server in macchine virtuali di Azure

Database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo minimo del punto di ripristino (RPO) e conservazione a lungo termine. È possibile eseguire il backup dei database di SQL Server in esecuzione su macchine virtuali di Azure (VM) usando [Backup di Azure](backup-overview.md).

Questo articolo illustra come eseguire il backup di un database di SQL Server in esecuzione in una VM di Azure a un insieme di credenziali di servizi di ripristino di Backup di Azure.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Creare e configurare un insieme di credenziali.
> * Individuazione di database e configurare i backup.
> * Configurare la protezione automatica per i database.


## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup di un database di SQL Server, controllare i criteri seguenti:

1. Identificare o creare un [insieme di credenziali dei servizi di ripristino](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) nella stessa area o delle impostazioni locali della macchina virtuale che ospita l'istanza di SQL Server.
2. Verificare i [autorizzazioni di VM necessarie](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) per eseguire il backup dei database SQL.
3. Verificare che la VM abbia [la connettività di rete](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Assicurarsi che i database di SQL Server seguano le [convenzioni di denominazione di database per il Backup di Azure](#database-naming-guidelines-for-azure-backup).
5. Verificare che non si dispone di qualsiasi altra soluzione di backup abilitata per il database. Disabilitare tutti gli altri backup di SQL Server prima di eseguire il backup del database.

> [!NOTE]
> È possibile abilitare il Backup di Azure per una macchina virtuale di Azure e anche per un database di SQL Server in esecuzione nella macchina virtuale senza conflitti.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, una macchina virtuale di SQL Server richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni di macchina virtuale (individuazione del database, configurare i backup, pianificare i backup, ripristinare i punti di ripristino e così via) non senza connettività a indirizzi IP pubblici di Azure.

Stabilire la connessione usando una delle opzioni seguenti:

- **Consentire gli intervalli di IP del Data Center di Azure**. Questa opzione consente [intervalli di indirizzi IP](https://www.microsoft.com/download/details.aspx?id=41653) nel download. Per accedere a un gruppo di sicurezza di rete (NSG), usare il cmdlet Set-AzureNetworkSecurityRule. Se si è specifico dell'area solo nell'elenco elementi consentiti gli indirizzi IP, si sarà anche necessario all'elenco elementi consentiti di Azure Active Directory (Azure AD) tag del servizio per abilitare l'autenticazione.

- **Consentire l'accesso usando i tag NSG**. Se si usano gli Nsg per limitare la connettività, questa opzione aggiunge una regola per il NSG che consenta l'accesso in uscita con Backup di Azure tramite il tag AzureBackup. Oltre a questo tag, è necessario anche corrispondente [regole](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per Azure AD e l'archiviazione di Azure per consentire la connettività per il trasferimento di dati e l'autenticazione. Il tag AzureBackup è attualmente disponibile in PowerShell solo. Per creare una regola utilizzando il tag AzureBackup:

    - Aggiungere le credenziali dell'account Azure e aggiornare i cloud nazionali<br/>
    `Add-AzureRmAccount`

    - Selezionare la sottoscrizione di NSG<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Selezionare il gruppo di sicurezza<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Aggiungere Consenti regola in uscita per il tag di servizio di Backup di Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Salvare il gruppo di sicurezza<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Consentire l'accesso tramite Firewall Azure tag**. Se si usa il Firewall di Azure, creare una regola dell'applicazione usando il AzureBackup [tag FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). In questo modo l'accesso in uscita con Backup di Azure.
- **Distribuire un server proxy HTTP per indirizzare il traffico**. Quando si esegue il backup di un database di SQL Server in una macchina virtuale di Azure, l'estensione di backup nella macchina virtuale Usa le API di HTTPS per inviare comandi di gestione di Backup di Azure e i dati in archiviazione di Azure. L'estensione di backup Usa Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Le estensioni sono l'unico componente configurato per l'accesso alla rete internet pubblica.

Opzioni di connettività includono i seguenti vantaggi e svantaggi:

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Nessun costo aggiuntivo | Complesso da gestire perché gli intervalli di indirizzi IP cambiano nel tempo <br/><br/> Fornisce l'accesso a tutto l'ambiente di Azure, non solo l'archiviazione di Azure
Usare tag di servizio di sicurezza di rete | Facile da gestire con modifiche all'intervallo vengono unite automaticamente <br/><br/> Nessun costo aggiuntivo <br/><br/> | Può essere utilizzato solo con Nsg <br/><br/> Fornisce l'accesso all'intero servizio
Usare i tag di Azure FQDN del Firewall | Facile da gestire con i nomi di dominio completi necessari vengono gestiti automaticamente | Può essere utilizzato solo con Firewall di Azure
Usare un proxy HTTP | Un controllo granulare nel proxy lo spazio di archiviazione è consentita l'URL <br/><br/> Singolo punto di accesso internet alle macchine virtuali <br/><br/> Non è soggetta a modifiche all'indirizzo IP di Azure | Costi aggiuntivi per l'esecuzione di una macchina virtuale con il software del proxy

### <a name="set-vm-permissions"></a>Impostare le autorizzazioni della VM

Quando si configura una copia di backup per un database di SQL Server, Backup di Azure esegue le operazioni seguenti:

- Aggiunge l'estensione AzureBackupWindowsWorkload.
- Crea un account SERVICE\AzureWLBackupPluginSvc NT per individuare i database nella macchina virtuale. Questo account viene usato per il backup e ripristino e richiede autorizzazioni sysadmin SQL.
- Consente di individuare i database che sono in esecuzione in una VM, Backup di Azure Usa l'account NT AUTHORITY\SYSTEM. Questo account deve essere un pubblico accesso in SQL.

Se è stato creato la VM SQL Server in Azure Marketplace, si potrebbe ricevere un errore UserErrorSQLNoSysadminMembership. Per altre informazioni, vedere la sezione Considerazioni e limitazioni di funzionalità disponibili in [sui Backup di SQL Server in macchine virtuali di Azure](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="database-naming-guidelines-for-azure-backup"></a>Database convenzioni di denominazione per il Backup di Azure

Evitare di utilizzare i seguenti elementi nei nomi di database:

  * Gli spazi iniziali e finali
  * Finali punti esclamativi (!)
  * Parentesi quadre (])
  * A partire da F:\

Alias è disponibile per i caratteri non supportati, ma è consigliabile evitarli. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Come individuare i database in esecuzione in una macchina virtuale:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per il backup del database.

2. Nel **insieme di credenziali dei servizi di ripristino** dashboard, selezionare **Backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nelle **obiettivo del Backup**, impostare **in cui viene eseguito il carico di lavoro?** al **Azure**.

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Obiettivo di backup** > **Individuare i database nelle macchine virtuali** selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Questa ricerca può richiedere alcuni minuti, a seconda del numero di macchine virtuali non protette nella sottoscrizione.

   - Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
   - Se una macchina virtuale non è elencata come previsto, visualizzata se si è già eseguito il backup in un insieme di credenziali.
   - Più macchine virtuali possono avere lo stesso nome, ma appartengono a gruppi di risorse diversi.

     ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Individuazione database di rilevamento nel **notifiche**. Il tempo necessario per questa azione dipende dal numero di database della macchina virtuale. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione degli elementi seguenti in background:

    - Backup di Azure regista la VM con l'insieme di credenziali per il backup del carico di lavoro. Tutti i database nella macchina virtuale registrato possono eseguire il backup nell'insieme di credenziali solo.
    - Backup di Azure installa l'estensione AzureBackupWindowsWorkload nella macchina virtuale. Nessun agente installato in un database SQL.
    - Backup di Azure crea l'account del servizio NT Service\AzureWLBackupPluginSvc nella macchina virtuale.
      - Tutte le operazioni di backup e ripristino usano l'account del servizio.
      - NT Service\AzureWLBackupPluginSvc richiede autorizzazioni sysadmin SQL. Tutte le macchine virtuali SQL Server creata nel Marketplace sono dotate di SqlIaaSExtension installato. L'estensione AzureBackupWindowsWorkload utilizza il SQLIaaSExtension per ottenere automaticamente le autorizzazioni necessarie.
    - Se è stato creato la macchina virtuale dal Marketplace, la macchina virtuale non sarà necessario il SqlIaaSExtension installato e l'operazione di individuazione ha esito negativo con messaggio di errore UserErrorSQLNoSysAdminMembership. Per risolvere questo problema, seguire le [istruzioni](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurare il backup  

1. Nelle **obiettivo di Backup** > **passaggio 2: Configurare il Backup**, selezionare **Configura Backup**.

   ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Nelle **Seleziona elementi per backup**, noterete che tutti i gruppi di disponibilità registrato e istanze autonome di SQL Server. Selezionare la freccia a sinistra di una riga per espandere l'elenco di tutti i database non protetti in tale istanza o gruppo di disponibilità Always On.  

    ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Scegliere tutti i database di cui si vuole proteggere e quindi selezionare **OK**.

   ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

   Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

     * Per proteggere più di 50 database, configurare più backup.
     * Per abilitare [ ](#enable-auto-protection) l'intera istanza o il gruppo di disponibilità Always On. Nel **AUTOPROTECT** elenco a discesa, seleziona **via**, quindi selezionare **OK**.
     
    > [!NOTE]
    > Il [la protezione automatica](#enable-auto-protection) funzionalità non solo Abilita protezione in tutti i database esistenti in una sola volta, ma consente di proteggere automaticamente anche tutti i nuovi database aggiunti a quell'istanza o il gruppo di disponibilità.  

4. Selezionare **OK** per aprire **criteri di Backup**.

    ![Abilitare la protezione automatica per il gruppo di disponibilità Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Nelle **criteri di Backup**, scegliere un criterio e quindi selezionare **OK**.

   - Selezionare i criteri predefiniti come HourlyLogBackup.
   - Scegliere un criterio di backup creato in precedenza per SQL.
   - Definire un nuovo criterio basato sull'intervallo di conservazione e RPO.

     ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Nelle **Backup**, selezionare **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

7. Tenere traccia dello stato di configurazione nel **notifiche** area del portale.

    ![Area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

- Un criterio viene creato a livello di insieme di credenziali.
- Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.
- Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita.
- È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo.
- Scopri [diversi tipi di criteri di backup](backup-architecture.md#sql-server-backup-types).

Per creare un criterio di backup:

1. Nell'insieme di credenziali, selezionare **criteri di Backup** > **Add**.
2. Nelle **Add**, selezionare **SQL Server in macchine Virtuali di Azure** per definire il tipo di criteri.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

3. In **Nome criterio** immettere un nome per il nuovo criterio.
4. Nelle **dei criteri di Backup completo**, selezionare una **frequenza di Backup**. Scegliere uno **giornaliera** oppure **settimanale**.

   - Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup.
   - Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.
   - Eseguire un backup completo, perché non è possibile disattivare la **Full Backup** opzione.
   - Selezionare **Backup completo** per visualizzare i criteri.
   - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.

     ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Nelle **mantenimento**, tutte le opzioni sono selezionate per impostazione predefinita. Cancella qualsiasi periodo di mantenimento limita che non desiderati e quindi impostare gli intervalli da utilizzare.

    - Periodo di memorizzazione minimo per qualsiasi tipo di backup (completi, differenziali e log) è sette giorni.
    - I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    - Il backup per un giorno specifico è contrassegnato e conservato in base l'intervallo di conservazione settimanale e l'impostazione di conservazione settimanale.
    - Mensili e annuali periodi di mantenimento dati si comportano in modo analogo.

       ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

6. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.
7. Per aggiungere un criterio di backup differenziale, selezionare **Backup differenziale**.

   ![Impostazioni dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Aprire il menu del criterio di backup differenziale](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.

    - È possibile attivare solo un backup differenziale al giorno.
    - I backup differenziali possono essere conservati al massimo per 180 giorni. Per un lungo periodo, usare i backup completi.

9. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

10. Per aggiungere un criterio di backup del log transazionale, selezionare **Backup del log**.
11. In **Backup del log** selezionare **Abilita** e quindi impostare le opzioni di frequenza e conservazione. I backup del log possono verificarsi con la frequenza di 15 minuti e possono essere conservati fino a 35 giorni.
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

È possibile abilitare la protezione automatica eseguire automaticamente il backup tutti i database esistenti e futuri in un'istanza di SQL Server autonomo o a un gruppo di disponibilità Always On.

- Non sono previsti limiti al numero di database che è possibile selezionare per la protezione automatica in una sola volta.
- In modo selettivo non è possibile proteggere o escludere i database dalla protezione dati in un'istanza al momento che si abilita la protezione automatica.
- Se l'istanza include già alcuni database protetti, rimangono sarà protetti sotto i rispettivi criteri anche dopo l'attivazione della protezione automatica. Tutti i database protetti aggiunti in seguito saranno necessario solo un singolo criterio che definisce al momento dell'abilitazione della protezione automatica, elencata nella **configurare il Backup**. Tuttavia, è possibile modificare i criteri associati a un database di protezione automatica in un secondo momento.  

Per abilitare la protezione automatica:

  1. In **Elementi per backup** selezionare l'istanza per la quale si vuole abilitare la protezione automatica.
  2. Selezionare l'elenco a discesa sotto **AUTOPROTECT**, scegliere **via**, quindi selezionare **OK**.

      ![Abilitare la protezione automatica nel gruppo di disponibilità](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Il backup è configurato per tutti i database insieme e può essere monitorato in **Processi di backup**.

Se è necessario disabilitare la protezione automatica, selezionare il nome dell'istanza in **configurare il Backup**, quindi selezionare **disabilitare Autoprotect** per l'istanza. Tutti i database continuerà a eseguire il backup, ma non verranno protetti automaticamente database futuri.

![Disabilitare la protezione automatica su quell'istanza](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Passaggi successivi

È possibile passare agli argomenti seguenti:

- [Ripristinare i database di SQL Server di backup](restore-sql-database-azure-vm.md)
- [Gestire i database di SQL Server di backup](manage-monitor-sql-database-backup.md)
