---
title: Eseguire il backup di un database SAP HANA in Azure con Backup di Azure
description: In questo articolo viene illustrato come eseguire il backup di un database SAP HANA nelle macchine virtuali di Azure con il servizio Backup di Azure.In this article, learn how to back up an SAP HANA database to Azure virtual machines with the Azure Backup service.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248060"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Eseguire il backup di database SAP HANA nelle VM di Azure

I database SAP HANA sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) e una conservazione a lungo termine. È possibile eseguire il backup dei database SAP HANA in esecuzione nelle macchine virtuali di Azure usando Backup di [Azure.](backup-overview.md)

Questo articolo illustra come eseguire il backup dei database SAP HANA in esecuzione nelle macchine virtuali di Azure in un insieme di credenziali di Azure Backup Recovery Services.This article shows how to back up SAP HANA databases that are running on Azure VMs to an Azure Backup Recovery Services vault.

In questo articolo verrà spiegato come:
> [!div class="checklist"]
>
> * Creare e configurare un insieme di credenziali
> * Individuare i database
> * Configurare i backup
> * Eseguire un processo di backup su richiesta

>[!NOTE]
>**L'eliminazione temporanea per il server SQL nella macchina virtuale di Azure e l'eliminazione temporanea per SAP HANA nei carichi** di lavoro della macchina virtuale di Azure sono ora disponibili in anteprima.<br>
>Per iscriverti all'anteprima, scrivici all'utenteAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prerequisiti

Fare riferimento alle [sezioni](tutorial-backup-sap-hana-db.md#prerequisites) Quali sono le sezioni [Che cosa fa lo script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) per impostare il database per il backup.

### <a name="set-up-network-connectivity"></a>Configurare la connettività di rete

Per tutte le operazioni, la macchina virtuale SAP HANA richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale, ad esempio individuazione dei database, configurazione e pianificazione dei backup, ripristino dei punti di ripristino e così via, non riescono in assenza di connettività agli indirizzi IP pubblici di Azure.

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

**Distribuire un server proxy HTTP per instradare il traffico.** Quando si esegue il backup di un database SAP HANA in una macchina virtuale di Azure, l'estensione di backup nella VM usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Le estensioni sono l'unico componente configurato per l'accesso a Internet pubblico.

Le opzioni di connettività presentano i vantaggi e gli svantaggi seguenti:

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Nessun costo aggiuntivo | Complessità di gestione a causa della variazione degli intervalli IP nel tempo <br/><br/> Accesso consentito a tutto l'ambiente di Azure, non solo ad Archiviazione di Azure
Usare i tag del servizio del gruppo di sicurezza di rete | Gestione semplificata perché le modifiche degli intervalli vengono unite automaticamente <br/><br/> Nessun costo aggiuntivo <br/><br/> | Utilizzabile solo con i gruppi di sicurezza di rete <br/><br/> Accesso consentito all'intero servizio
Usare i tag FQDN di Firewall di Azure | Gestione semplificata perché i FQDN necessari vengono gestiti automaticamente | Utilizzabile solo con Firewall di Azure
Usare un proxy HTTP | Controllo granulare nel proxy URL di archiviazione <br/><br/> Singolo punto di accesso Internet alle VM <br/><br/> Nessun effetto in caso di modifiche degli indirizzi IP di Azure | Costi aggiuntivi per l'esecuzione di una VM con il software proxy

#### <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Individuare i database

1. Nell'insieme di credenziali, in **Attività iniziali**, fare clic su **Backup**. In **Posizione di esecuzione del carico di lavoro** selezionare **SAP HANA in una macchina virtuale di Azure**.
2. Fare clic su **Avvia individuazione**. Viene avviata l'individuazione delle macchine virtuali Linux non protette nell'area dell'insieme di credenziali.

   * Dopo l'individuazione, le macchine virtuali non protette vengono visualizzate nel portale, elencate per nome e gruppo di risorse.
   * Se una macchina virtuale non è elencata come previsto, verificare se è già stato eseguito il backup in un insieme di credenziali.
   * Più macchine virtuali possono avere lo stesso nome ma appartengono a gruppi di risorse diversi.

3. In **Seleziona macchine virtuali** fare clic sul collegamento per scaricare lo script che fornisce le autorizzazioni per il servizio Backup di Azure per accedere alle macchine virtuali SAP HANA per l'individuazione del database.
4. Eseguire lo script in ogni macchina virtuale che ospita i database SAP HANA di cui si vuole eseguire il backup.
5. Dopo aver eseguito lo script nelle macchine virtuali, in **Seleziona macchine virtuali**selezionare le macchine virtuali. Quindi fare clic su **Individua database**.
6. Backup di Azure individua tutti i database SAP HANA presenti nella macchina virtuale. Durante l'individuazione, Backup di Azure registra la VM con l'insieme di credenziali e installa l'estensione. Nel database non vengono installati agenti.

    ![Scopri i database SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurare il backup  

Ora abilitare il backup.

1. Nel passaggio 2 fare clic su **Configura backup**.

    ![Configurazione di backup](./media/backup-azure-sap-hana-database/configure-backup.png)
2. In **Selezionare gli elementi di cui eseguire il backup**selezionare tutti i database che si desidera proteggere > **OK**.

    ![Seleziona elementi per backup](./media/backup-azure-sap-hana-database/select-items.png)
3. In **Criteri di** > backup Scegliere i**criteri di backup**creare un nuovo criterio di backup per i database in base alle istruzioni riportate di seguito.

    ![Scegliere i criteri di backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Dopo aver creato il criterio, scegliere **Abilita backup**dal menu **Backup** .

    ![Abilita backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Per tenere traccia dello stato di avanzamento della configurazione di backup, vedere l'area **Notifiche** del portale.

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

* Un criterio viene creato a livello di insieme di credenziali.
* Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.

Specificare le impostazioni del criterio come segue:

1. In **Nome criterio** immettere un nome per il nuovo criterio.

   ![Immettere il nome del criterio](./media/backup-azure-sap-hana-database/policy-name.png)
2. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.
   * **Giornaliero:** selezionare l'ora e il fuso orario in cui inizia il processo di backup.
       * È necessario eseguire un backup completo. Non è possibile disattivare questa opzione.
       * Fare clic su **Backup completo** per visualizzare il criterio.
       * Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
   * **Settimanale**: selezionare il giorno della settimana, l'ora e il fuso orario in cui viene eseguito il processo di backup.

   ![Selezionare la frequenza di backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. In **Intervallo conservazione** configurare le impostazioni di conservazione per il backup completo.
    * Per impostazione predefinita, tutte le opzioni sono selezionate. Cancellare i limiti dell'intervallo di conservazione che non si desidera utilizzare e impostarli.
    * Il periodo di conservazione minimo di qualsiasi tipo di backup (completo/differenziale/del log) è di sette giorni.
    * I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    * Il backup di un giorno specifico viene contrassegnato e conservato in base all'intervallo e all'impostazione di conservazione settimanale.
    * L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

4. Nel menu **Criteri di backup completo** fare clic su **OK** per accettare le impostazioni.
5. Selezionare **Backup differenziale** per aggiungere un criterio differenziale.
6. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.
    * Al massimo, è possibile attivare un backup differenziale al giorno.
    * I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

    ![Criteri di backup differenziale](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > I backup incrementali non sono attualmente supportati.

7. Fare clic su **OK** per salvare il criterio e tornare nel menu principale **Criteri di backup**.
8. Selezionare **Backup del log** per aggiungere un criterio per i backup del log delle transazioni.
    * In **Backup log**selezionare **Abilita**.  Questa opzione non può essere disabilitata perché SAP HANA gestisce tutti i backup del log.
    * Impostare i controlli di frequenza e conservazione.

    > [!NOTE]
    > I backup del log iniziano a fluire solo dopo il completamento di un backup completo.

9. Fare clic su **OK** per salvare il criterio e tornare nel menu principale **Criteri di backup**.
10. Dopo aver completato la definizione dei criteri di backup, fare clic su **OK**.

> [!NOTE]
> Ogni backup del log viene concatenato al backup completo precedente per formare una catena di ripristino. Questo backup completo verrà mantenuto fino alla scadenza della conservazione dell'ultimo backup del log. Ciò potrebbe significare che il backup completo viene mantenuto per un periodo aggiuntivo per assicurarsi che tutti i registri possono essere recuperati. Supponiamo che l'utente abbia un backup completo settimanale, differenziale giornaliero e registri di 2 ore. Tutti loro sono conservati per 30 giorni. Ma, il settimanale pieno può essere davvero pulito / eliminato solo dopo il prossimo backup completo è disponibile cioè, dopo 30 x 7 giorni. Ad esempio, un backup completo settimanale avviene il 16 novembre. In base al criterio di conservazione, deve essere conservato fino al 16 dicembre. L'ultimo backup del log per questo pieno viene eseguito prima del successivo pianificato completo, il 22 novembre. Fino a quando questo registro non sarà disponibile fino al 22 dicembre, il 16 novembre non può essere eliminato. Così, il 16 novembre pieno viene mantenuto fino al 22 dicembre.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in base alla pianificazione dei criteri. È possibile eseguire un backup su richiesta come segue:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup**selezionare la macchina virtuale che esegue il database SAP HANA e quindi fare clic su Backup **ora**.
3. In **Backup ora**, utilizzare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
4. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **processi** > di backup**in corso.** A seconda delle dimensioni del database, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire il backup SAP HANA Studio in un database con Backup di Azure abilitatoRun SAP HANA Studio backup on a database with Azure Backup enabled

Se si vuole eseguire un backup locale (tramite HANA Studio) di un database di cui viene eseguito il backup con Backup di Azure, eseguire le operazioni seguenti:

1. Attendere il completamento dei backup completi o del log per il completamento del database. Controllare lo stato in SAP HANA Studio / Cockpit.
2. Disabilitare i backup del log e impostare il catalogo di backup sul file system per il database pertinente.
3. A tale scopo, fare doppio clic su **systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Impostare **enable_auto_log_backup** su **No**.
5. Impostare **log_backup_using_backint** **su False**.
6. Eseguire un backup completo su richiesta del database.
7. Attendere il completamento del backup completo e del backup del catalogo.
8. Ripristinare le impostazioni precedenti a quelle per Azure:Revert the previous settings back to those for Azure:
    * Impostare **enable_auto_log_backup** **su Sì**.
    * Impostare **log_backup_using_backint** su **True**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare i database SAP HANA in esecuzione nelle VM di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Informazioni su come gestire i database SAP HANA di [cui viene eseguito il backup tramite Backup di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
