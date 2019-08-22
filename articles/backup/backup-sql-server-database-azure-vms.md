---
title: Eseguire il backup di database SQL Server nelle VM di Azure | Microsoft Docs
description: Informazioni su come eseguire il backup di database di SQL Server in macchine virtuali di Azure
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 3c16d8b5f1611c6c05e60d65551f73eb2d395668
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872899"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Eseguire il backup di database SQL Server in macchine virtuali di Azure

SQL Server database sono carichi di lavoro di importanza critica che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di SQL Server database in esecuzione in macchine virtuali di Azure con [backup di Azure](backup-overview.md).

Questo articolo illustra come eseguire il backup di un database di SQL Server in esecuzione in una macchina virtuale di Azure in un insieme di credenziali di servizi di ripristino di backup di Azure.

L'articolo spiega come:

> [!div class="checklist"]
> * Creare e configurare un insieme di credenziali.
> * Individuare i database e configurare i backup.
> * Configurare la protezione automatica per i database.


## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup di un database di SQL Server, verificare i criteri seguenti:

1. Identificare o creare un insieme di credenziali di [servizi di ripristino](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) nella stessa area o nelle stesse impostazioni locali della VM che ospita l'istanza di SQL Server.
2. Verificare che la macchina virtuale disponga di [connettività di rete](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Assicurarsi che i database di SQL Server seguano le [linee guida per la denominazione dei database per backup di Azure](#database-naming-guidelines-for-azure-backup).
4. In particolare per SQL 2008 e 2008 R2, [aggiungere la chiave del registro di sistema](#add-registry-key-to-enable-registration) per abilitare la registrazione del server. Questo passaggio non sarà necessario quando la funzionalità è disponibile a livello generale.
5. Verificare che non siano presenti altre soluzioni di backup abilitate per il database. Disabilitare tutti gli altri backup SQL Server prima di eseguire il backup del database.

> [!NOTE]
> È possibile abilitare backup di Azure per una macchina virtuale di Azure e anche per un database SQL Server in esecuzione nella macchina virtuale senza conflitti.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, una macchina virtuale SQL Server richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale (individuazione del database, configurazione dei backup, pianificazione dei backup, ripristino dei punti di ripristino e così via) hanno esito negativo senza connettività agli indirizzi IP pubblici di Azure.

Stabilire la connettività usando una delle opzioni seguenti:

- **Consentire gli intervalli IP del Data Center di Azure**. Questa opzione consente di scaricare gli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653) . Per accedere a un gruppo di sicurezza di rete (NSG), usare il cmdlet Set-AzureNetworkSecurityRule. Se si dispone di un elenco di destinatari sicuri solo per gli indirizzi IP specifici dell'area, sarà necessario aggiornare anche l'Azure AD Azure Active Directory elenco dei destinatari sicuri per abilitare l'autenticazione.

- **Consente l'accesso tramite tag NSG**.  Se si usa NSG per limitare la connettività, è necessario usare il tag del servizio AzureBackup per consentire l'accesso in uscita a backup di Azure. Inoltre, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati usando [le regole](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per Azure ad e archiviazione di Azure. Questa operazione può essere eseguita dal portale o da PowerShell.

    Per creare una regola tramite il portale:
    
    - In **tutti i servizi**, passare a **gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.
    - Selezionare **regole di sicurezza in uscita** in **Impostazioni**.
    - Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto in [impostazioni delle regole di sicurezza](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Verificare che l'opzione **destinazione** sia impostata su **tag servizio** e il **tag servizio di destinazione** sia impostato su **AzureBackup**.
    - Fare clic su **Aggiungi**per salvare la regola di sicurezza in uscita appena creata.
    
   Per creare una regola usando PowerShell:

   - Aggiungere le credenziali dell'account Azure e aggiornare i cloud nazionali<br/>
    ``Add-AzureRmAccount``
  - Selezionare la sottoscrizione di NSG<br/>
    ``Select-AzureRmSubscription "<Subscription Id>"``
  - Selezionare il NSG<br/>
    ```$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"```
  - Aggiungi regola Consenti connessioni in uscita per il servizio backup di Azure<br/>
   ```Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"```
  - Salva NSG<br/>
    ```Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg```

   
- **Consentire l'accesso usando i tag del firewall di Azure**. Se si usa il firewall di Azure, creare una regola dell'applicazione usando il [tag FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)AzureBackup. Questo consente l'accesso in uscita a backup di Azure.
- **Distribuire un server proxy HTTP per**instradare il traffico. Quando si esegue il backup di un database di SQL Server in una macchina virtuale di Azure, l'estensione di backup nella VM usa le API HTTPS per inviare i comandi di gestione a backup e dati di Azure in archiviazione di Azure. L'estensione per il backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Le estensioni sono l'unico componente configurato per l'accesso a Internet pubblico.

Le opzioni di connettività includono i vantaggi e gli svantaggi seguenti:

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Nessun costo aggiuntivo | Complesso da gestire perché gli intervalli di indirizzi IP cambiano nel tempo <br/><br/> Consente di accedere all'intero Azure, non solo all'archiviazione di Azure
Usare i tag del servizio NSG | Più facile da gestire quando le modifiche all'intervallo vengono unite automaticamente <br/><br/> Nessun costo aggiuntivo <br/><br/> | Può essere usato solo con gruppi <br/><br/> Consente di accedere all'intero servizio
Usare i tag FQDN del firewall di Azure | Facile da gestire perché i nomi di dominio completi necessari vengono gestiti automaticamente | Può essere usato solo con il firewall di Azure
Usare un proxy HTTP | È consentito il controllo granulare nel proxy sugli URL di archiviazione <br/><br/> Singolo punto di accesso Internet alle macchine virtuali <br/><br/> Non soggetto alle modifiche degli indirizzi IP di Azure | Costi aggiuntivi per l'esecuzione di una macchina virtuale con il software proxy

### <a name="database-naming-guidelines-for-azure-backup"></a>Linee guida per la denominazione di database per backup di Azure

Evitare di utilizzare i seguenti elementi nei nomi di database:

  * Spazi finali e iniziali
  * Punti esclamativi finali (!)
  * Parentesi quadre chiuse (])
  * Punto e virgola ';'
  * Barra '/'

L'aliasing è disponibile per i caratteri non supportati, ma è consigliabile evitarli. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

### <a name="add-registry-key-to-enable-registration"></a>Aggiungere la chiave del registro di sistema per abilitare la registrazione

1. Apri regedit
2. Creare il percorso della directory del registro di sistema: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (sarà necessario creare il TestHook ' Key ' in WorkloadBackup, che a sua volta deve essere creato in Microsoft).
3. Nel percorso della directory del registro di sistema creare un nuovo valore stringa con il nome stringa **AzureBackupEnableWin2K8R2SP1** e il valore: **True**

    ![RegEdit per l'abilitazione della registrazione](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

In alternativa, è possibile automatizzare questo passaggio eseguendo il file. reg con il comando seguente:

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Come individuare i database in esecuzione in una macchina virtuale:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per il backup del database.

2. Nel dashboard dell'insieme di credenziali di **servizi di ripristino** selezionare **backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **obiettivo di backup**impostare **il percorso in cui è in esecuzione il carico di lavoro?** in **Azure**.

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Obiettivo di backup** > **Individuare i database nelle macchine virtuali** selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Questa ricerca può richiedere un po' di tempo, a seconda del numero di macchine virtuali non protette nella sottoscrizione.

   - Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
   - Se una macchina virtuale non è elencata come previsto, vedere se è già stato eseguito il backup in un insieme di credenziali.
   - Più macchine virtuali possono avere lo stesso nome, ma appartengono a gruppi di risorse diversi.

     ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Rilevare l'individuazione del database nelle **notifiche**. Il tempo necessario per questa azione dipende dal numero di database VM. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione, in background si verificano gli elementi seguenti:

    - Backup di Azure registra la VM con l'insieme di credenziali per il backup del carico di lavoro. È possibile eseguire il backup di tutti i database della VM registrata solo in questo insieme di credenziali.
    - Backup di Azure installa l'estensione AzureBackupWindowsWorkload nella macchina virtuale. Nessun agente è installato in un database SQL.
    - Backup di Azure crea l'account di servizio NT Service\AzureWLBackupPluginSvc nella macchina virtuale.
      - Tutte le operazioni di backup e ripristino usano l'account del servizio.
      - NT Service\AzureWLBackupPluginSvc richiede le autorizzazioni sysadmin di SQL. Tutte le macchine virtuali SQL Server create nel Marketplace vengono installate con SqlIaaSExtension. L'estensione AzureBackupWindowsWorkload USA SQLIaaSExtension per ottenere automaticamente le autorizzazioni necessarie.
    - Se la macchina virtuale non è stata creata dal Marketplace o se si utilizza SQL 2008 e 2008 R2, è possibile che nella macchina virtuale non sia installato SqlIaaSExtension e che l'operazione di individuazione abbia esito negativo con il messaggio di errore UserErrorSQLNoSysAdminMembership. Per risolvere questo problema, seguire le istruzioni in [impostare le autorizzazioni per le macchine virtuali](backup-azure-sql-database.md#set-vm-permissions).

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurare il backup  

1. In > obiettivo**di backup passaggio 2: Configurare il**backup e selezionare **Configura backup**.

   ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. In **Seleziona elementi di cui eseguire il backup**vengono visualizzati tutti i gruppi di disponibilità registrati e le istanze SQL Server autonome. Selezionare la freccia a sinistra di una riga per espandere l'elenco di tutti i database non protetti in tale istanza o Always On gruppo di disponibilità.  

    ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Scegliere tutti i database che si desidera proteggere e quindi fare clic su **OK**.

   ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

   Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

     * Per proteggere più di 50 database, configurare più backup.
     * Per [abilitare](#enable-auto-protection) l'intera istanza o il gruppo di disponibilità always on, nell' elenco a discesa Proteggi AutoProtect selezionare **on**, quindi fare clic su **OK**.

    > [!NOTE]
    > La funzionalità di [protezione automatica](#enable-auto-protection) non solo Abilita la protezione in tutti i database esistenti in una sola volta, ma protegge automaticamente anche tutti i nuovi database aggiunti a tale istanza o al gruppo di disponibilità.  

4. Selezionare **OK** per aprire **criteri di backup**.

    ![Abilitare la protezione automatica per il gruppo di disponibilità Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. In **criterio di backup**scegliere un criterio e quindi fare clic su **OK**.

   - Selezionare i criteri predefiniti come HourlyLogBackup.
   - Scegliere un criterio di backup creato in precedenza per SQL.
   - Definire un nuovo criterio basato sull'obiettivo del punto di ripristino (RPO) e sull'intervallo di conservazione.

     ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. In **Backup** selezionare **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

7. È possibile monitorare l'avanzamento della configurazione nell'area delle **notifiche** del portale.

    ![Area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

- Un criterio viene creato a livello di insieme di credenziali.
- Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.
- Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita.
- È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo.
- Informazioni sui [diversi tipi di criteri di backup](backup-architecture.md#sql-server-backup-types).

Per creare un criterio di backup:

1. Nell'insieme di credenziali selezionare **criteri** > di backup**Aggiungi**.
2. In **Aggiungi**selezionare **SQL Server nella macchina virtuale di Azure** per definire il tipo di criteri.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

3. In **Nome criterio** immettere un nome per il nuovo criterio.
4. In **criterio backup completo**selezionare una **frequenza di backup**. Scegliere **giornaliera** o **settimanale**.

   - Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup.
   - Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.
   - Eseguire un backup completo, perché non è possibile disattivare l'opzione **backup completo** .
   - Selezionare **backup completo** per visualizzare i criteri.
   - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.

     ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. In periodo di **mantenimento**dati, tutte le opzioni sono selezionate per impostazione predefinita. Deselezionare gli eventuali limiti del periodo di mantenimento dati non desiderati, quindi impostare gli intervalli da utilizzare.

    - Il periodo di memorizzazione minimo per qualsiasi tipo di backup (completo, differenziale e log) è di sette giorni.
    - I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    - Il backup di un giorno specifico viene contrassegnato e mantenuto in base al periodo di mantenimento dati settimanale e all'impostazione di conservazione settimanale.
    - Gli intervalli di conservazione mensili e annuali si comportano in modo simile.

       ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

6. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.
7. Per aggiungere un criterio di backup differenziale, selezionare **Backup differenziale**.

   ![Impostazioni dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Aprire il menu del criterio di backup differenziale](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.

    - È possibile attivare un solo backup differenziale al giorno.
    - I backup differenziali possono essere conservati al massimo per 180 giorni. Per un periodo di conservazione più lungo, utilizzare backup completi.

9. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

10. Per aggiungere un criterio di backup del log transazionale, selezionare **Backup del log**.
11. In **Backup del log** selezionare **Abilita** e quindi impostare le opzioni di frequenza e conservazione. I backup del log possono essere eseguiti ogni 15 minuti e possono essere conservati per un massimo di 35 giorni.
12. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

    ![Modificare i criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Nel menu **Criterio di backup**, scegliere se abilitare la **compressione dei backup SQL**.
    - La compressione è disabilitata per impostazione predefinita.
    - Nel back-end Backup di Azure usa la compressione di backup nativa di SQL.

14. Dopo aver completato le modifiche ai criteri di backup, selezionare **OK**.


### <a name="modify-policy"></a>Modificare i criteri
Modificare i criteri per modificare la frequenza di backup o il periodo di mantenimento dati.

> [!NOTE]
> Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retrospettivo a tutti i punti di ripristino meno recenti oltre a quelli nuovi.

Nel dashboard dell'insieme di credenziali passare > a Gestisci**criteri di backup** e scegliere il criterio che si vuole modificare.

  ![Gestire i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Abilitare la protezione automatica  

È possibile abilitare la protezione automatica per eseguire automaticamente il backup di tutti i database esistenti e futuri in un'istanza di SQL Server autonoma o in un gruppo di disponibilità Always On.

- Non esiste alcun limite al numero di database che è possibile selezionare per la protezione automatica in una sola volta.
- Non è possibile proteggere in modo selettivo o escludere i database dalla protezione in un'istanza nel momento in cui si Abilita la protezione automatica.
- Se l'istanza include già alcuni database protetti, questi rimarranno protetti in base ai rispettivi criteri anche dopo l'attivazione della protezione automatica. Tutti i database non protetti aggiunti in un secondo momento avranno un solo criterio definito al momento dell'abilitazione della protezione automatica, elencati in **configurare il backup**. Tuttavia, è possibile modificare i criteri associati a un database protetto automaticamente in un secondo momento.  

Per abilitare la protezione automatica:

  1. In **Elementi per backup** selezionare l'istanza per la quale si vuole abilitare la protezione automatica.
  2. Selezionare l'elenco a discesa in **AutoProtect**, scegliere Sì e quindi fare clic **su** **OK**.

      ![Abilitare la protezione automatica nel gruppo di disponibilità](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Il backup è configurato per tutti i database insieme e può essere monitorato in **Processi di backup**.

Se è necessario disabilitare la protezione automatica, selezionare il nome dell'istanza in **Configura backup**, quindi selezionare **Disattiva protezione** automatica per l'istanza. Tutti i database continueranno a essere sottoposti a backup, ma i database futuri non verranno protetti automaticamente.

![Disabilitare la protezione automatica in tale istanza](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Passaggi successivi

È possibile passare agli argomenti seguenti:

- [Ripristinare i database SQL Server sottoposti a backup](restore-sql-database-azure-vm.md)
- [Gestire i database SQL Server sottoposti a backup](manage-monitor-sql-database-backup.md)
