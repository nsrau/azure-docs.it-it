---
title: Eseguire la replica di app con SQL Server e Azure Site Recovery | Microsoft Docs
description: "Questo articolo descrive come eseguire la replica di SQL Server tramite le funzionalità di ripristino di emergenza di SQL Server e Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 9ea73dd91c9637692bbc3d6d2aa97fbed7ae500d
ms.openlocfilehash: 79c110031a47f1bdb78f4acfcadd7bff1e909807
ms.lasthandoff: 02/23/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery

Questo articolo descrive come proteggere il back-end SQL Server di un'applicazione usando una combinazione di [Azure Site Recovery](site-recovery-overview.md) e tecnologie di continuità aziendale e ripristino di emergenza di SQL Server.

Prima di iniziare, verificare di conoscere le funzionalità di ripristino di emergenza di SQL Server, come clustering di failover, gruppi di disponibilità AlwaysOn, mirroring del database e log shipping.


## <a name="sql-server-deployments"></a>Distribuzioni di SQL Server

Molti carichi di lavoro usano come base SQL Server, che può essere integrato con app come SharePoint, Dynamics e SAP per implementare servizi dati.  È possibile distribuire SQL Server in diversi modi.

* **SQL Server autonomo**: SQL Server e tutti i database sono ospitati in un singolo computer (fisico o una macchina virtuale). Se è virtualizzato, il clustering dell'host viene usato per la disponibilità elevata. La disponibilità elevata a livello di guest non viene implementata.
* **Istanze di clustering di failover di SQL Server (AlwaysOn)**: due o più nodi che eseguono istanze di SQL Server con dischi condivisi vengono configurati in un cluster di failover di Windows. Se un nodo è inattivo, il cluster può effettuare il failover di SQL Server in un'altra istanza. Questa configurazione viene in genere usata per implementare la disponibilità elevata in un sito primario. Questa distribuzione non offre la protezione da errori o interruzioni nel livello dell'archiviazione condivisa. Un disco condiviso può essere implementato con iSCSI, Fibre Channel o VHDX condiviso.
* **Gruppi di disponibilità SQL AlwaysOn**: due o più nodi vengono configurati in un cluster non condiviso con i database SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico.

 Per ripristinare i database in un sito remoto, in questo articolo vengono sfruttate le tecnologie di ripristino di emergenza native di SQL riportate di seguito:

* Gruppi di disponibilità SQL AlwaysOn, per supportare il ripristino di emergenza per SQL Server 2012 o 2014 Enterprise Edition.
* Mirroring del database SQL in modalità protezione elevata, per SQL Server Standard Edition (qualsiasi versione) o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Supporto di Site Recovery

### <a name="supported-scenarios"></a>Scenari supportati
Site Recovery può proteggere SQL Server, come riepilogato nella tabella.

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì
**Server fisico** | Sì | Sì

### <a name="supported-sql-server-versions"></a>Versioni di SQL Server supportate
Per gli scenari supportati, sono supportate le versioni di SQL Server seguenti:

* SQL Server 2014 Enterprise e Standard
* SQL Server 2012 Enterprise e Standard
* SQL Server 2008 R2 Enterprise e Standard

### <a name="supported-sql-server-integration"></a>Supporto dell'integrazione con SQL Server

Site Recovery può essere integrato con le tecnologie di continuità aziendale e ripristino di emergenza native di SQL Server riepilogate nella tabella per fornire una soluzione di ripristino di emergenza.

**Funzionalità** | **Dettagli** | **SQL Server** |
--- | --- | ---
**Gruppo di disponibilità AlwaysOn** | Ognuna delle istanze autonome multiple di SQL Server viene eseguita in un cluster di failover con più nodi.<br/><br/>I database possono essere raggruppati in gruppi di failover che possono essere copiati (con mirroring) in istanze di SQL Server in modo che non sia necessaria alcuna archiviazione condivisa.<br/><br/>Fornisce il ripristino di emergenza tra un sito primario e uno o più siti secondari. Due nodi possono essere configurati in un cluster non condiviso con i database di SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico. | SQL Server 2014 e 2012 Enterprise
**Clustering di failover (AlwaysOn FCI)** | SQL Server usa il clustering di failover di Windows per l'elevata disponibilità dei carichi di lavoro SQL Server locali.<br/><br/>I nodi che eseguono le istanze di SQL Server con dischi condivisi sono configurati in un cluster di failover. Se un'istanza non è attiva, il cluster esegue il failover in un'altra istanza.<br/><br/>Il cluster non protegge da errori o interruzioni nell'archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, fibre channel o VHDX condivisi. | Edizioni SQL Server Enterprise<br/><br/>Edizione SQL Server Standard (limitata a soli due nodi)
**Mirroring del database (modalità di protezione elevata)** | Protegge un singolo database in una singola copia secondaria. Disponibile nelle modalità di replica a protezione elevata (sincrona) e a prestazione elevata (asincrona). Non richiede un cluster di failover. | SQL Server 2008 R2<br/><br/>Tutte le edizioni di SQL Server Enterprise
**SQL Server autonomo** | SQL Server e il database si trovano in un singolo server (fisico o virtuale). Il clustering dell'host viene usato per la disponibilità elevata se il server è virtuale. Nessuna disponibilità elevata a livello di guest. | Edizione Enterprise o Standard

## <a name="deployment-recommendations"></a>Indicazioni di distribuzione

Nella tabella seguente vengono riepilogate le indicazioni per l'integrazione delle tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server con Site Recovery.

| **Versione** | **Edizione** | **Distribuzione** | **Da locale a locale** | **Da locale ad Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 o 2012 |Enterprise |Istanza del cluster di failover |Gruppi di disponibilità AlwaysOn |Gruppi di disponibilità AlwaysOn |
|| Enterprise |Gruppi di disponibilità AlwaysOn per la disponibilità elevata |Gruppi di disponibilità AlwaysOn |Gruppi di disponibilità AlwaysOn | |
|| Standard |Istanza del cluster di failover (FCI) |Replica di Site Recovery con mirror locale |Replica di Site Recovery con mirror locale | |
|| Enterprise o Standard |Autonoma |Replica di Site Recovery |Replica di Site Recovery | |
| SQL Server 2008 R2 |Enterprise o Standard |Istanza del cluster di failover (FCI) |Replica di Site Recovery con mirror locale |Replica di Site Recovery con mirror locale |
|| Enterprise o Standard |Autonoma |Replica di Site Recovery |Replica di Site Recovery | |
| SQL Server (qualsiasi versione) |Enterprise o Standard |Istanza del cluster di failover: applicazione DTC |Replica di Site Recovery |Non supportato |

## <a name="deployment-prerequisites"></a>Prerequisiti di distribuzione

* Distribuzione di SQL Server locale che esegue una versione supportata di SQL Server. In genere è necessario anche Active Directory per il server SQL.
* Requisiti per lo scenario che si vuole distribuire. Per altre informazioni sui requisiti per il supporto, vedere gli articoli relativi alla [replica in Azure](site-recovery-support-matrix-to-azure.md) e [in locale](site-recovery-support-matrix.md) e ai [prerequisiti per la distribuzione](site-recovery-prereq.md).
* Per configurare il ripristino in Azure, eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) nelle macchine virtuali di SQL Server per verificare che siano compatibili con Azure e Site Recovery.

## <a name="set-up-active-directory"></a>Configurare Active Directory

Per garantire la corretta esecuzione di SQL Server, configurare Active Directory nel sito di ripristino secondario.

* **Organizzazione di piccole dimensioni**: in presenza di un numero ridotto di applicazioni e di un singolo controller di dominio per il sito locale, se si vuole effettuare il failover dell'intero sito è consigliabile usare la replica di Site Recovery per replicare il controller di dominio nel data center secondario o in Azure.
* **Organizzazione di medie o grandi dimensioni**: se si hanno un numero elevato di applicazioni e una foresta Active Directory e si vuole effettuare il failover per applicazione o carico di lavoro, è consigliabile configurare un controller di dominio aggiuntivo nel data center secondario o in Azure. Se si usano gruppi di disponibilità AlwaysOn per il ripristino in un sito remoto, è consigliabile configurare un altro controller di dominio aggiuntivo nel sito secondario o in Azure, da usare per l'istanza di SQL Server ripristinata.

Le istruzioni in questo articolo presuppongono che sia disponibile un controller di dominio nella posizione secondaria. [Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory con Site Recovery.

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>Eseguire l'integrazione con SQL Server AlwaysOn per la replica in Azure (portale classico con server di configurazione/VMM)


Site Recovery supporta in modo nativo SQL AlwaysOn. Se si è creato un gruppo di disponibilità SQL con una macchina virtuale di Azure configurata come posizione secondaria, è possibile usare Site Recovery per gestire il failover dei gruppi di disponibilità.

> [!NOTE]
> Questa funzionalità è attualmente in anteprima. È disponibile quando i server host Hyper-V del sito primario sono gestiti in cloud VMM di System Center o quando è stata configurata la [replica di VMware](site-recovery-vmware-to-azure.md). La funzionalità non è attualmente disponibile nel nuovo portale di Azure. Eseguire la procedura in [questa sezione](site-recovery-sql.md#integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server) se si usa il nuovo portale di Azure.
>
>


#### <a name="before-you-start"></a>Prima di iniziare

Per integrare SQL AlwaysOn con Site Recovery, sono necessari gli elementi seguenti:

* Un'istanza di SQL Server locale (server autonomo o cluster di failover).
* Una o più macchine virtuali di Azure con SQL Server installato.
* Un gruppo di disponibilità SQL Server configurato tra un'istanza locale di SQL Server e SQL Server in esecuzione in Azure.
* Comunicazione remota di PowerShell abilitata nell'istanza locale di SQL Server. Il server VMM o il server di configurazione deve poter effettuare chiamate remote di PowerShell al computer SQL Server.
* Account utente aggiunto al computer SQL Server locale. Eseguire l'aggiunta in un gruppo SQL Server con almeno le autorizzazioni seguenti:
  * ALTER AVAILABILITY GROUP: [qui](https://msdn.microsoft.com/library/hh231018.aspx) e [qui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE: [qui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
* Se si esegue VMM, un account RunAs creato nel server VMM.
- Se si esegue VMware, un account creato nel server di configurazione usando CSPSConfigtool.exe.
* Modulo SQL PS installato nelle istanze di SQL Server eseguite in locale e nelle VM di Azure.
* Agente di macchine virtuali installato nelle VM di Azure.
* NTAUTHORITY\System con le autorizzazioni seguenti nell'istanza di SQL Server in esecuzione nelle VM di Azure.
  * ALTER AVAILABILITY GROUP: [qui](https://msdn.microsoft.com/library/hh231018.aspx) e [qui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE: [qui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

### <a name="add-a-sql-server"></a>Aggiungere un'istanza di SQL Server
1. Fare clic su **Aggiungi applicazione SQL** per aggiungere una nuova istanza del server SQL.

    ![Aggiungi applicazione SQL](./media/site-recovery-sql/add-sql.png)
2. In **Configura impostazioni SQL** > **Nome** specificare un nome descrittivo per l'istanza di SQL Server.
3. In **SQL Server (nome di dominio completo)** specificare il nome di dominio completo (FQDN) dell'istanza di SQL Server di origine da aggiungere. Se SQL Server viene installato in un cluster di failover, specificare il nome di dominio completo del cluster, non dei nodi del cluster.  
4. In **Istanza di SQL Server** scegliere l'istanza predefinita o specificare il nome di un'istanza personalizzata.
5. In **Server di gestione** selezionare un server VMM o un server di configurazione registrato nell'insieme di credenziali. Site Recovery usa questo server per comunicare con SQL Server.
6. In **Account RunAs** specificare il nome dell'account RunAs di VMM o l'account del server di configurazione. Questo account viene usato per accedere a SQL Server e deve avere autorizzazioni di lettura e failover per i gruppi di disponibilità nel computer SQL Server.

    ![Finestra di dialogo Aggiungi SQL](./media/site-recovery-sql/add-sql-dialog.png)

Dopo che è stata aggiunta, l'istanza di SQL Server verrà visualizzata nella scheda **SQL Server**.

![Elenco di server SQL](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>Aggiungere un gruppo di disponibilità SQL

1. Nell'istanza di SQL Server aggiunta fare clic su **Aggiungi gruppo di disponibilità SQL**.

    ![Aggiungi gruppo di disponibilità SQL](./media/site-recovery-sql/add-sqlag.png)
2. La replica del gruppo di disponibilità può essere eseguita in una o più VM di Azure. Quando si aggiunge il gruppo, è necessario specificare il nome e la sottoscrizione della VM di Azure in cui si vuole che Site Recovery effettui il failover del gruppo.

    ![Finestra di dialogo Aggiungi gruppo di disponibilità SQL](./media/site-recovery-sql/add-sqlag-dialog.png)
3. In questo esempio, in caso di failover il gruppo di disponibilità DB1-AG diventerà primario nella macchina virtuale SQLAGVM2 in esecuzione all'interno della sottoscrizione DevTesting2.

> [!NOTE]
> È possibile aggiungere a Site Recovery solo i gruppi di disponibilità che sono primari nell'istanza di SQL Server aggiunta. Se si è impostato un gruppo di disponibilità come primario in SQL Server o si sono aggiunti altri gruppi di disponibilità nell'istanza di SQL Server dopo che è stata aggiunta, aggiornare il gruppo in SQL Server.
>
>

### <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

Creare un piano di ripristino usando sia macchine virtuali che gruppi di disponibilità. Selezionare il server VMM o il server di configurazione come origine e Azure come destinazione.

![Create Recovery Plan](./media/site-recovery-sql/create-rp1.png)

![Create Recovery Plan](./media/site-recovery-sql/create-rp2.png)

Nell'esempio, l'app Sharepoint include tre macchine virtuali che usano un gruppo di disponibilità SQL come back-end. In questo piano di ripristino, si possono selezionare sia il gruppo di disponibilità che le VM dell'app. È possibile personalizzare ulteriormente il piano di ripristino spostando le VM in gruppi di failover diversi per definire la sequenza dell'ordine di failover. Il gruppo di disponibilità viene sempre sottoposto a failover per primo perché viene usato come back-end dell'app.

![Personalizzare il piano di ripristino](./media/site-recovery-sql/customize-rp.png)

### <a name="failover"></a>Failover

Dopo l'aggiunta del gruppo di disponibilità a un piano di ripristino, sono disponibili diverse opzioni di failover.

**Failover** | **Dettagli**
--- | ---
**Failover pianificato** | Il failover pianificato implica un failover senza perdita di dati. A tale scopo, il gruppo di disponibilità SQL viene impostato sulla modalità sincrona e quindi viene attivato un failover per impostare il gruppo di disponibilità come primario nella macchina virtuale specificata durante l'aggiunta del gruppo a Site Recovery. Al termine del failover, la modalità di disponibilità viene impostata sul valore precedente all'attivazione del failover pianificato.
**Failover non pianificato** | Il failover non pianificato può generare perdite di dati. Quando viene attivato un failover non pianificato, la modalità di disponibilità del gruppo non viene modificata. Il gruppo viene impostato come primario nella macchina virtuale specificata durante l'aggiunta del gruppo di disponibilità a Site Recovery. Al termine del failover, quando il server locale che esegue SQL Server è nuovamente disponibile, deve essere attivata la replica inversa nel gruppo di disponibilità. Questa azione è disponibile in **SQL Server** > **gruppo di disponibilità SQL** e non nel piano di ripristino.
**Failover di test** |Il failover di test non è supportato per i gruppi di disponibilità SQL. Se si attiva un failover di test, il failover viene ignorato per il gruppo di disponibilità.

Provare queste opzioni di failover.

**Opzione** | **Dettagli**
--- | ---
**Opzione 1** | 1. Eseguire un failover di test dei livelli applicazione e front-end.<br/><br/>2. Aggiornare il livello applicazione per accedere alla copia di replica in modalità sola lettura ed eseguire un test di sola lettura dell'applicazione.
**Opzione 2** | 1. Creare una copia dell'istanza della macchina virtuale di SQL Server di replica (usando il clone VMM per il backup da sito a sito o Backup di Azure) e aprirla in una rete di test.<br/><br/> 2. Eseguire il failover di test usando il piano di ripristino.

### <a name="fail-back"></a>Effettuare il failback

Se si vuole impostare nuovamente il gruppo di disponibilità come primario nell'istanza locale di SQL Server, è possibile attivare un failover pianificato nel piano di ripristino e selezionare la direzione da Microsoft Azure al server locale.

> [!NOTE]
> Dopo un failover non pianificato, per riprendere la replica è necessario attivare la replica inversa nel gruppo di disponibilità.  La replica rimane sospesa fino a quando non viene eseguita questa operazione.
>
>

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>Eseguire l'integrazione con SQL Server AlwaysOn per la replica in Azure (portale di Azure/portale classico senza server di configurazione/VMM)

Queste istruzioni si applicano in caso di integrazione con gruppi di disponibilità SQL Server nel nuovo portale di Azure o nel portale classico, se non si usa un server VMM o un server di configurazione. In questo scenario, è possibile usare i runbook di automazione di Azure per configurare un failover tramite script dei gruppi di disponibilità SQL.

È necessario eseguire queste operazioni:

1. Creare un file locale per uno script che effettua il failover di un gruppo di disponibilità. Questo script di esempio specifica un percorso per il gruppo di disponibilità nella replica di Azure ed effettua il failover in tale istanza di replica. Lo script viene passato con l'estensione di script personalizzati ed eseguito nella macchina virtuale di replica di SQL Server.

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. Caricare lo script presente in un blob in un account di archiviazione di Azure. Esempio:

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Creare un runbook di automazione di Azure per richiamare gli script nella macchina virtuale di replica di SQL Server in Azure. Per eseguire questa operazione usare questo script di esempio. [ulteriori informazioni](site-recovery-runbook-automation.md) sull'uso di runbook di automazione in piani di ripristino.

4. Quando si crea un piano di ripristino per l'applicazione, aggiungere un passaggio di script "pre-Group 1 boot" che richiami il runbook di automazione per effettuare il failover del gruppo di disponibilità.


5. SQL AlwaysOn non supporta il failover di test in modo nativo. È quindi consigliabile eseguire queste operazioni:
    1. Configurare [Backup di Azure](../backup/backup-azure-vms.md) nella macchina virtuale che ospita la replica del gruppo di disponibilità in Azure.
    1. Prima di attivare il failover di test del piano di ripristino, ripristinare la macchina virtuale dal backup creato nel passaggio precedente.
    1. Effettuare un failover di test del piano di ripristino.


> [!NOTE]
> Lo script seguente presuppone che il gruppo di disponibilità SQL sia ospitato in una VM di Azure della versione classica e che il nome della macchina virtuale ripristinata nel passaggio&2; sia SQLAzureVM-Test. Modificare lo script in base al nome usato per la macchina virtuale ripristinata.
>
>


     ``workflow SQLAvailabilityGroupFailover
     {

         param (
             [Object]$RecoveryPlanContext
         )

         $Cred = Get-AutomationPSCredential -name 'AzureCredential'

         #Connect to Azure
         $AzureAccount = Add-AzureAccount -Credential $Cred
         $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
         Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

         InLineScript
         {
          #Update the script with name of your storage account, key and blob name
          $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
          $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;

          Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;

          if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-alwayson-for-replication-to-a-secondary-on-premises-site"></a>Eseguire l'integrazione con SQL Server AlwaysOn per la replica in un sito secondario locale

Se SQL Server usa gruppi di disponibilità per la disponibilità elevata o un'istanza di clustering di failover, è consigliabile usare gruppi di disponibilità anche nel sito di ripristino. Si noti che ciò si applica alle app che non usano transazioni distribuite.

1. [Configurare i database](https://msdn.microsoft.com/library/hh213078.aspx) in gruppi di disponibilità.
2. Creare una rete virtuale nel sito secondario.
3. Configurare una connessione VPN da sito a sito tra la rete virtuale e il sito primario.
4. Creare una macchina virtuale nel sito di ripristino e installarvi SQL Server.
5. Estendere i gruppi di disponibilità AlwaysOn esistenti alla nuova VM di SQL Server. Configurare l'istanza di SQL Server come una copia di replica asincrona.
6. Creare un listener del gruppo di disponibilità o aggiornare il listener esistente per includere la macchina virtuale di replica asincrona.
7. Verificare che la farm dell'applicazione sia configurata con il listener. Se è configurata con il nome del server di database, aggiornarla per usare il listener in modo che non debba essere riconfigurata dopo il failover.

Per le applicazioni che usano transazioni distribuite, è consigliabile distribuire Site Recovery con la [replica SAN](site-recovery-vmm-san.md) o la [replica da sito a sito di server fisici/VMware](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerazioni sul piano di ripristino
1. Aggiungere questo script di esempio alla libreria VMM nei siti primari e secondari.

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. Quando si crea un piano di ripristino per l'applicazione, aggiungere un passaggio di script "pre-Group 1 boot" che richiami lo script per effettuare il failover dei gruppi di disponibilità.

## <a name="protect-a-standalone-sql-server"></a>Proteggere un'istanza di SQL Server autonoma

In questo scenario, è consigliabile usare la replica di Site Recovery per proteggere il computer SQL Server. La procedura esatta varia a seconda che SQL Server sia configurato come VM o server fisico e che si voglia eseguire la replica in Azure o in un sito secondario locale. Vedere l'articolo relativo agli [scenari di Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Proteggere un cluster SQL Server (Standard Edition/Windows Server 2008 R2)

Per un cluster che esegue SQL Server Standard Edition o SQL Server 2008 R2, è consigliabile usare la replica di Site Recovery per proteggere SQL Server.

### <a name="on-premises-to-on-premises"></a>Da sito locale a sito locale

* Se l'app usa transazioni distribuite, è consigliabile distribuire [Site Recovery con la replica SAN](site-recovery-vmm-san.md) per un ambiente Hyper-V oppure la replica da [server fisico/VMware a VMware](site-recovery-vmware-to-vmware.md) per un ambiente VMware.
* Per le applicazioni non DTC, usare l'approccio precedente per ripristinare il cluster come un server autonomo sfruttando un mirror del database a protezione elevata locale.

### <a name="on-premises-to-azure"></a>Da sito locale ad Azure

Site Recovery non offre il supporto di cluster guest durante la replica in Azure. Inoltre, SQL Server non fornisce una soluzione di ripristino di emergenza a costo contenuto per l'edizione Standard. In questo scenario, è consigliabile proteggere il cluster SQL Server locale in un'istanza di SQL Server autonoma e ripristinarlo in Azure.

1. Configurare un'istanza di SQL Server autonoma aggiuntiva nel sito locale.
2. Configurare l'istanza in modo che funga da mirror per i database da proteggere. Configurare il mirroring in modalità protezione elevata.
3. Configurare Site Recovery nel sito locale, per [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VM VMware/server fisici](site-recovery-vmware-to-azure-classic.md).
4. Usare la replica di Site Recovery per eseguire la replica della nuova istanza di SQL Server in Azure. Trattandosi di una copia mirror a protezione elevata, verrà sincronizzata con il cluster primario, ma verrà replicata in Azure con la replica di Site Recovery.


![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>Considerazioni sul failback

Per i cluster SQL Server Standard, il failback dopo un failover non pianificato richiede un backup e un ripristino di SQL Server dall'istanza del mirror al cluster originale, con ridefinizione del mirror.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](site-recovery-components.md) sull'architettura di Site Recovery.

