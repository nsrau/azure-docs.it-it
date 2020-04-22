---
title: Eseguire il backup di database SQL Server in macchine virtuali di Azure
description: In questo articolo viene illustrato come eseguire il backup dei database di SQL Server nelle macchine virtuali di Azure con Backup di Azure.In this article s see how to back up SQL Server databases on Azure virtual machines with Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 887f15deed74330cf132e0574d166c074d2c7cad
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685719"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Eseguire il backup di database SQL Server in macchine virtuali di Azure

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e una conservazione a lungo termine. È possibile eseguire il backup dei database di SQL Server in esecuzione nelle macchine virtuali di Azure usando Backup di [Azure.](backup-overview.md)

Questo articolo illustra come eseguire il backup di un database di SQL Server in esecuzione in una macchina virtuale di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure.This article shows how to back up a SQL Server database that's running on an Azure VM to an Azure Backup Recovery Services vault.

In questo articolo si apprenderà come:

> [!div class="checklist"]
>
> * Creare e configurare un insieme di credenziali.
> * Individua i database e configura i backup.
> * Configurare la protezione automatica per i database.

>[!NOTE]
>**L'eliminazione temporanea per il server SQL nella macchina virtuale di Azure e l'eliminazione temporanea per SAP HANA nei carichi** di lavoro della macchina virtuale di Azure sono ora disponibili in anteprima.<br>
>Per iscriverti all'anteprima, scrivici all'utenteAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup di un database di SQL Server, verificare i criteri seguenti:

1. Identificare o creare un insieme di [credenziali di Servizi di ripristino](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) nella stessa area e sottoscrizione della macchina virtuale che ospita l'istanza di SQL Server.Identify or create a Recovery Services vault in the same region and subscription as the VM hosting the SQL Server instance.
2. Verificare che la macchina virtuale disponga di [connettività](backup-sql-server-database-azure-vms.md#establish-network-connectivity)di rete .
3. Assicurarsi che i database di SQL Server seguano le linee guida per la denominazione dei [database per Backup di Azure.](#database-naming-guidelines-for-azure-backup)
4. Verificare che per il database non siano abilitate altre soluzioni di backup. Disabilitare tutti gli altri backup di SQL Server prima di eseguire il backup del database.

> [!NOTE]
> È possibile abilitare Backup di Azure per una macchina virtuale di Azure e anche per un database di SQL Server in esecuzione nella macchina virtuale senza conflitti.

### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, una macchina virtuale di SQL Server richiede la connettività agli indirizzi IP pubblici di Azure.For all operations, a SQL Server VM requires connectivity to Azure public IP addresses. Le operazioni della macchina virtuale, ad esempio individuazione dei database, configurazione e pianificazione dei backup, ripristino dei punti di ripristino e così via, non riescono in assenza di connettività agli indirizzi IP pubblici di Azure.

Stabilire la connettività usando una delle opzioni seguenti:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Consentire gli intervalli IP del data center di Azure

Questa opzione autorizza gli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653) nel file scaricato. Per accedere a un gruppo di sicurezza di rete, usare il cmdlet Set-AzureNetworkSecurityRule. Se l'elenco dei destinatari attendibili include solo indirizzi IP specifici dell'area, sarà anche necessario aggiornarlo con il tag del servizio Azure Active Directory (Azure AD) per abilitare l'autenticazione.

#### <a name="allow-access-using-nsg-tags"></a>Consentire l'accesso con tag del gruppo di sicurezza di rete

Se si usa un gruppo di sicurezza di rete per limitare la connettività, è necessario usare il tag del servizio AzureBackup per consentire l'accesso in uscita a Backup di Azure. Inoltre, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati usando [regole](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per Azure AD e Archiviazione di Azure. Questa operazione può essere eseguita dal portale di Azure o tramite PowerShell.

Per creare una regola tramite il portale:

  1. In **Tutti i servizi**, passare a **Gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.
  2. In **Impostazioni** selezionare **Regole di sicurezza in uscita**.
  3. Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto nelle [impostazioni delle regole di sicurezza](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Assicurarsi che l'opzione **Destinazione** sia impostata su **Tag del servizio** e che l'opzione **Tag del servizio di destinazione** sia impostata su **AzureBackup**.
  4. Fare clic su **Aggiungi** per salvare la regola di sicurezza in uscita appena creata.

Per creare una regola tramite PowerShell:

 1. Aggiungere le credenziali dell'account Azure e aggiornare i cloud nazionali<br/>
      `Add-AzureRmAccount`<br/>

 2. Selezionare la sottoscrizione del gruppo di sicurezza di rete<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selezionare il gruppo di sicurezza di rete<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Aggiungere la regola per consentire l'accesso in uscita per il tag del servizio Backup di Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Aggiungere la regola per consentire l'accesso in uscita per il tag del servizio Archiviazione<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Aggiungere la regola per consentire l'accesso in uscita per il tag del servizio AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salvare il gruppo di sicurezza di rete<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Consentire l'accesso usando i tag di Firewall di Azure**. Se si usa Firewall di Azure, creare una regola dell'applicazione usando il [tag FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags) AzureBackup. In questo modo si consente l'accesso in uscita a Backup di Azure.

**Distribuire un server proxy HTTP per instradare il traffico.** Quando si esegue il backup di un database di SQL Server in una macchina virtuale di Azure, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare comandi di gestione a Backup di Azure e dati ad Archiviazione di Azure.When you back up a SQL Server database on an Azure VM, the backup extension on the VM uses the HTTPS APIs to send management commands to Azure Backup and data to Azure Storage. L'estensione di backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Non esistono domini con caratteri jolly in uso con Backup di Azure da aggiungere all'elenco Consenti per le regole proxy. È necessario usare gli intervalli IP pubblici per questi servizi forniti da Azure.You will need to use the public IP ranges for these services provided by Azure. Le estensioni sono l'unico componente configurato per l'accesso a Internet pubblico.

Le opzioni di connettività presentano i vantaggi e gli svantaggi seguenti:

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Nessun costo aggiuntivo | Complessità di gestione a causa della variazione degli intervalli IP nel tempo <br/><br/> Accesso consentito a tutto l'ambiente di Azure, non solo ad Archiviazione di Azure
Usare i tag del servizio del gruppo di sicurezza di rete | Gestione semplificata perché le modifiche degli intervalli vengono unite automaticamente <br/><br/> Nessun costo aggiuntivo <br/><br/> | Utilizzabile solo con i gruppi di sicurezza di rete <br/><br/> Accesso consentito all'intero servizio
Usare i tag FQDN di Firewall di Azure | Gestione semplificata perché i FQDN necessari vengono gestiti automaticamente | Utilizzabile solo con Firewall di Azure
Usare un proxy HTTP | Singolo punto di accesso Internet alle VM <br/> | Costi aggiuntivi per l'esecuzione di una VM con il software proxy <br/> Nessun indirizzo FQDN pubblicato, le regole consentite saranno soggette a modifiche degli indirizzi IP di AzureNo published FQDN addresses, allow rules will be subject to Azure IP address changes

#### <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Database naming guidelines for Azure Backup

Evitare di utilizzare i seguenti elementi nei nomi di database:

* Spazi finali e iniziali
* Punti esclamativi finali (!)
* Parentesi quadre di chiusura (])
* Punto e virgola ';'
* Barra avanti '/'

L'aliasing è disponibile per i caratteri non supportati, ma ti consigliamo di evitarli. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>L'operazione **di configurazione della protezione** per i database con caratteri speciali, ad esempio il nome di "" o "&". È possibile modificare il nome del database o abilitare la **protezione automatica**, che consente di proteggere correttamente questi database.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Come individuare i database in esecuzione in una macchina virtuale:How to discover databases running on a VM:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per il backup del database.

2. Nel dashboard **dell'insieme di credenziali di Servizi di ripristino** selezionare **Backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Obiettivo di backup**impostare Dove è in esecuzione il carico di lavoro **su** **Azure**.

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Backup Goal** > **Individua i database nelle macchine virtuali**selezionare Avvia **individuazione** per cercare macchine virtuali non protette nella sottoscrizione. Questa ricerca può richiedere del tempo, a seconda del numero di macchine virtuali non protette nella sottoscrizione.

   * Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
   * Se una macchina virtuale non è elencata come previsto, verificare se ne è già stato eseguito il backup in un insieme di credenziali.
   * Più macchine virtuali possono avere lo stesso nome, ma apparterranno a gruppi di risorse diversi.

     ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Tenere traccia dell'individuazione dei database in **Notifiche**. Il tempo necessario per questa azione dipende dal numero di database delle macchine virtuali. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione, in background si verificano i seguenti elementi:

    * Backup di Azure registra la macchina virtuale con l'insieme di credenziali per il backup del carico di lavoro. È possibile eseguire il backup di tutti i database nella macchina virtuale registrata solo in questo insieme di credenziali.
    * Backup di Azure installa l'estensione AzureBackupWindowsWorkload nella macchina virtuale. Nessun agente è installato in un database SQL.
    * Backup di Azure crea l'account del servizio NT Service\AzureWLBackupPluginSvc nella macchina virtuale.
      * Tutte le operazioni di backup e ripristino usano l'account del servizio.
      * Nt Service: AzureWLBackupPluginSvc richiede autorizzazioni sysadmin SQL. Tutte le macchine virtuali di SQL Server create nel Marketplace vengono associate con SqlIaaSExtension installato. L'estensione AzureBackupWindowsWorkload utilizza l'estensione SQLIaaSExtension per ottenere automaticamente le autorizzazioni richieste.
    * Se la macchina virtuale non è stata creata dal Marketplace o se si utilizza SQL 2008 e 2008 R2, è possibile che la macchina virtuale non sia installata e l'operazione di individuazione non riesce con il messaggio di errore UserErrorSQLNoSysAdminMembership. Per risolvere questo problema, seguire le istruzioni in [Impostare le autorizzazioni della macchina virtuale](backup-azure-sql-database.md#set-vm-permissions).

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurare il backup  

1. In Backup **Goal** > **Step 2: Configure Backup**(Configurazione del backup) selezionare Configure Backup **(Configura backup).**

   ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. In **Selezionare gli elementi di cui eseguire il backup**vengono visualizzati tutti i gruppi di disponibilità registrati e le istanze autonome di SQL Server. Selezionare la freccia a sinistra di una riga per espandere l'elenco di tutti i database non protetti in tale istanza o Gruppo di disponibilità AlwaysOnAlways On availability group.  

    ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Scegliere tutti i database da proteggere e quindi scegliere **OK**.

   ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

   Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

     * Per proteggere più di 50 database, configurare più backup.
     * Per [abilitare](#enable-auto-protection) l'intera istanza o il gruppo di disponibilità AlwaysOnAlways On availability group, nell'elenco a discesa **AUTOPROTECT** selezionare **ON**, quindi **scegliere OK**.

    > [!NOTE]
    > La [funzionalità di protezione automatica](#enable-auto-protection) non solo consente la protezione in tutti i database esistenti contemporaneamente, ma protegge anche automaticamente tutti i nuovi database aggiunti a tale istanza o al gruppo di disponibilità.  

4. Selezionare **OK** per aprire **Criterio di backup**.

    ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOnEnable auto-protection for the Always On availability group](./media/backup-azure-sql-database/enable-auto-protection.png)

5. In **Criteri di backup**scegliere un criterio e quindi scegliere **OK**.

   * Selezionare il criterio predefinito come HourlyLogBackup.Select the default policy as HourlyLogBackup.
   * Scegliere un criterio di backup creato in precedenza per SQL.
   * Definire un nuovo criterio basato sull'obiettivo del punto di ripristino (RPO) e sull'intervallo di conservazione.

     ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. In **Backup** selezionare **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

7. È possibile monitorare l'avanzamento della configurazione nell'area delle **notifiche** del portale.

    ![Area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

* Un criterio viene creato a livello di insieme di credenziali.
* Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.
* Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita.
* È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo.
* Informazioni sui [diversi tipi di criteri di backup.](backup-architecture.md#sql-server-backup-types)

Per creare un criterio di backup:

1. Nell'insieme di credenziali selezionare **Criteri di** > backup**Aggiungi**.
2. In **Aggiungi**selezionare **SQL Server nella macchina virtuale di Azure** per definire il tipo di criterio.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

3. In **Nome criterio** immettere un nome per il nuovo criterio.
4. In **Criteri di backup completo** selezionare un valore per **Frequenza di backup**. Scegliere **Giornaliero** o **Settimanale**.

   * Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup.
   * Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.
   * Eseguire un backup completo, perché non è possibile disattivare l'opzione **Backup completo.**
   * Selezionare **Backup completo** per visualizzare il criterio.
   * Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.

     ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. In **RETENTION RANGE,** tutte le opzioni sono selezionate per impostazione predefinita. Cancellare i limiti dell'intervallo di conservazione non desiderati e quindi impostare gli intervalli da utilizzare.

    * Il periodo di conservazione minimo per qualsiasi tipo di backup (completo, differenziale e log) è di sette giorni.
    * I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    * Il backup per un giorno specifico viene contrassegnato e mantenuto in base all'intervallo di conservazione settimanale e all'impostazione di conservazione settimanale.
    * Gli intervalli di conservazione mensili e annuali si comportano in modo simile.

       ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

6. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.
7. Per aggiungere un criterio di backup differenziale, selezionare **Backup differenziale**.

   ![Impostazioni dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Aprire il menu del criterio di backup differenziale](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.

    * È possibile attivare un solo backup differenziale al giorno.
    * I backup differenziali possono essere conservati al massimo per 180 giorni. Per una conservazione più lunga, utilizzare backup completi.

9. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

10. Per aggiungere un criterio di backup del log transazionale, selezionare **Backup del log**.
11. In **Backup del log** selezionare **Abilita** e quindi impostare le opzioni di frequenza e conservazione. I backup del log possono essere eseguiti ogni 15 minuti e possono essere conservati per un massimo di 35 giorni.
12. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

    ![Modificare i criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Nel menu Criteri di **backup** scegliere se abilitare o meno la compressione del **backup SQL.** L'opzione è disabilitata per impostazione predefinita. Se abilitata, SQL Server invierà un flusso di backup compresso all'unità VDI.  Si noti che Backup di Azure sostituisce le impostazioni predefinite a livello di istanza con la clausola COMPRESSION / NO_COMPRESSION a seconda del valore di questo controllo.

14. Dopo aver completato le modifiche ai criteri di backup, selezionare **OK**.

> [!NOTE]
> Ogni backup del log viene concatenato al backup completo precedente per formare una catena di ripristino. Questo backup completo verrà mantenuto fino alla scadenza della conservazione dell'ultimo backup del log. Ciò potrebbe significare che il backup completo viene mantenuto per un periodo aggiuntivo per assicurarsi che tutti i registri possono essere recuperati. Supponiamo che l'utente abbia un backup completo settimanale, differenziale giornaliero e registri di 2 ore. Tutti loro sono conservati per 30 giorni. Ma, il settimanale pieno può essere davvero pulito / eliminato solo dopo il prossimo backup completo è disponibile cioè, dopo 30 x 7 giorni. Ad esempio, un backup completo settimanale avviene il 16 novembre. In base al criterio di conservazione, deve essere conservato fino al 16 dicembre. L'ultimo backup del log per questo pieno viene eseguito prima del successivo pianificato completo, il 22 novembre. Fino a quando questo registro non sarà disponibile fino al 22 dicembre, il 16 novembre non può essere eliminato. Così, il 16 novembre pieno viene mantenuto fino al 22 dicembre.

## <a name="enable-auto-protection"></a>Abilitare la protezione automatica  

È possibile abilitare la protezione automatica per eseguire automaticamente il backup di tutti i database esistenti e futuri in un'istanza autonoma di SQL ServerSQL Server o in un gruppo di disponibilità AlwaysOnAlways On availability group.

* Non esiste alcun limite al numero di database che è possibile selezionare per la protezione automatica contemporaneamente.
* Non è possibile proteggere o escludere selettivamente i database dalla protezione in un'istanza nel momento in cui si abilita la protezione automatica.
* Se l'istanza include già alcuni database protetti, questi rimarranno protetti ai relativi criteri anche dopo l'attivazione della protezione automatica. Tutti i database non protetti aggiunti successivamente disfaranno di un solo criterio definito al momento dell'abilitazione della protezione automatica, elencati in **Configura backup**. Tuttavia, è possibile modificare i criteri associati a un database protetto automaticamente in un secondo momento.  

Per abilitare la protezione automatica:

  1. In **Elementi per backup** selezionare l'istanza per la quale si vuole abilitare la protezione automatica.
  2. Selezionare l'elenco a discesa in **AUTOPROTECT**, scegliere **ON**, quindi **scegliere OK**.

      ![Abilitare la protezione automatica nel gruppo di disponibilitàEnable auto-protection on the availability group](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Il backup è configurato per tutti i database insieme e può essere monitorato in **Processi di backup**.

Se è necessario disabilitare la protezione automatica, selezionare il nome dell'istanza in **Configura backup**e quindi **selezionare Disattiva protezione automatica** per l'istanza. Il backup di tutti i database continuerà a essere eseguito, ma i database futuri non verranno protetti automaticamente.

![Disabilitare la protezione automatica in tale istanzaDisable auto-protection on that instance](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:

* [Ripristinare i database di SQL Server di cui è stato eseguito il backup](restore-sql-database-azure-vm.md)
* [Gestire i database di SQL Server di cui è stato eseguito il backup](manage-monitor-sql-database-backup.md)
