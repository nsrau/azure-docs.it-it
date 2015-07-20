<properties 
	pageTitle="Ripristino di emergenza con SQL Server e Azure Site Recovery" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino di SQL Server in un sito locale secondario o in Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# Ripristino di emergenza con SQL Server e Azure Site Recovery 

Site Recovery è un servizio di Azure che favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino di macchine virtuali e server fisici. Site Recovery supporta una serie di meccanismi di replica per la protezione, la replica e il failover coerenti delle macchine in Azure o in un centro dati secondario. Per una panoramica di tutti gli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

 In questo articolo viene descritto come proteggere il backend SQL Server di un'applicazione usando una combinazione di Site Recovery e tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server. Prima di distribuire gli scenari descritti in questo articolo, è necessario avere una buona conoscenza di Site Recovery e delle funzionalità di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server (clustering di failover, gruppi di disponibilità AlwaysOn, mirroring del database, log shipping).



## Panoramica

Molti carichi di lavoro usano SQL Server come base. Applicazioni come SharePoint, Dynamics e SAP usano SQL Server per implementare i servizi di dati. Le funzionalità di elevata disponibilità e il ripristino di emergenza di SQL Server si applicano ai gruppi di disponibilità di SQL Server per proteggere e ripristinare i database di SQL Server.

Site Recovery è in grado di proteggere l’esecuzione di SQL Server come macchina virtuale Hyper-V, macchina virtuale VMware o server fisico.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>Server fisico</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Da sito locale a sito locale</td>
		<td>Da sito locale ad Azure</td>
		<td>Da sito locale a sito locale</td>
		<td>Da sito locale ad Azure</td>
		<td>Da sito locale a sito locale</td>
		<td>Da sito locale ad Azure</td>
    </tr>
	<tr align="left" valign="top">
		<td>Sì</td>
		<td>Sì</td>
		<td>Sì</td>
		<td>Presto disponibile</td>
		<td>Sì</td>
		<td>Presto disponibile</td>
    </tr>
    </tbody>
    </table>

## Supporto e integrazione

Site Recovery può essere integrato con le tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server native riepilogate nella tabella per fornire una soluzione di ripristino di emergenza.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Funzionalità</b></td>
		<td><b>Dettagli</b></td>
		<td><b>Versione di SQL Server</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>Gruppi di disponibilità AlwaysOn</b></td>
		<td><p>Più istanze autonome di SQL Server eseguite ciascuna in un cluster di failover con più nodi.</p> <p>I database possono essere raggruppati in gruppi di failover che possono essere copiati (con mirroring) in istanze di SQL Server in modo che non sia necessaria alcuna archiviazione condivisa.</p> <p>Fornisce il ripristino di emergenza tra un sito primario e uno o più siti secondari. Due nodi possono essere configurati in un cluster non condiviso con i database di SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico.</p></td>
		<td>Edizione SQL Server 2014/2012 Enterprise</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>Clustering di failover (AlwaysOn FCI)</b></td>
		<td><p>SQL Server usa il clustering di failover di Windows per l’elevata disponibilità dei carichi di lavoro SQL Server locali.</p><p>I nodi che eseguono le istanze di SQL Server con dischi condivisi sono configurati in un cluster di failover. Se un'istanza non è attiva, il failover del cluster viene eseguito su un’altra istanza.</p> <p>Il cluster non protegge da errori o interruzioni nell'archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, fibre channel o VHDX condivisi.</p></td>
		<td><p>Edizioni SQL Server Enterprise</p> <p>Edizione SQL Server Standard (limitata solo a due nodi)</p></td>
	<tr align="left" valign="top">
		<td><b>Mirroring del database in modalità di protezione elevata</b></td>
		<td>Protegge un singolo database in una singola copia secondaria. Disponibile nelle modalità di replica a protezione elevata (sincrona) e a prestazione elevata (asincrona). Non richiede un cluster di failover.</td>
		<td><p>SQL Server 2008 R2</p><p>Tutte le edizioni di SQL Server Enterprise</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>SQL Server autonomo</b></td>
		<td>SQL Server e il database si trovano in un singolo server (fisico o virtuale). Il clustering dell'host viene usato per la disponibilità elevata se il server è virtuale. Nessuna disponibilità elevata a livello di guest.</td>
		<td>Edizione Enterprise o Standard</td>
 
    </tbody>
    </table>

Nella tabella seguente vengono riepilogati i suggerimenti per l'integrazione delle tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server nella distribuzione di Site Recovery.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Versione</b></td>
		<td><b>Edizione</b></td>
		<td><b>Distribuzione</b></td>
		<td><b>Da sito locale a sito locale</b></td>
		<td><b>Da sito locale ad Azure</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 o 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>Istanza del cluster di failover</td>
		<td>Gruppi di disponibilità AlwaysOn</td>
		<td>Gruppi di disponibilità AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Gruppi di disponibilità AlwaysOn per la disponibilità elevata</td>
		<td>Gruppo di disponibilità AlwaysOn</td>
		<td>Gruppo di disponibilità AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Standard</td>
		<td>Istanza del cluster di failover</td>
		<td>Replica di Site Recovery con mirror locale</td>
		<td>Replica di Site Recovery con mirror locale</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise o Standard</td>
		<td>Autonoma</td>
		<td>Replica di Site Recovery con mirror locale</td>
		<td>Replica di Site Recovery con mirror locale</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise o Standard</td>
		<td>Autonoma</td>
		<td>Replica di Site Recovery con mirror locale</td>
		<td>Replica di Site Recovery con mirror locale</td>
    </tr>
    </tbody>
    </table>


## Prerequisiti di distribuzione

Di seguito sono riportati i prerequisiti necessari per iniziare:


- Una distribuzione di SQL Server locale che esegue una versione supportata di SQL Server. In genere è necessario anche un server Active Directory per il server SQL.
- Prerequisiti per lo scenario che si vuole distribuire. I prerequisiti sono disponibili in ciascun articolo relativo alla distribuzione. Tali prerequisiti sono elencati e collegati nella [Panoramica su Site Recovery](site-recovery-overview.md).
- Se si desidera configurare il ripristino in Azure, è necessario eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) nelle macchine virtuali di SQL Server per assicurarsi che siano compatibili con Azure e Site Recovery.

## Configurare la protezione

È necessario eseguire un paio di passaggi:

- Configurare Active Directory
- Configurare la protezione per un cluster di SQL Server o per un server autonomo

### Configurare Active Directory

Per fare in modo che SQL Server venga eseguito correttamente, è necessario Active Directory nel sito di ripristino secondario. Sono disponibili due opzioni:

- **Piccola impresa**: se si dispone di un numero ridotto di applicazioni e di un singolo controller di dominio per il sito locale e si vuole eseguire il failover dell'intero sito, è consigliabile usare la replica di Site Recovery per replicare il controller di dominio nel centro dati secondario o in Azure.

- **Media o grande impresa**: se si dispone di un numero elevato di applicazioni, si utilizza un insieme di strutture Active Directory e si vuole eseguire il failover dell'applicazione o del carico di lavoro, è consigliabile configurare un controller di dominio aggiuntivo nel centro dati o in Azure. Se si usano gruppi di disponibilità AlwaysOn per ripristinare un sito remoto è consigliabile configurare un altro controller di dominio aggiuntivo nel sito secondario o in Azure, da utilizzare per l'istanza di SQL Server ripristinata.

Le istruzioni riportate in questo documento presuppongono che un controller di dominio sia disponibile nella posizione secondaria.

### Configurare la protezione per un SQL Server autonomo


In questa configurazione è consigliabile usare la replica di Site Recovery per proteggere il computer SQL Server. La procedura varia a seconda che SQL Server sia configurato come una macchina virtuale o un server fisico e che si voglia eseguire la replica in Azure o in un sito locale secondario. Per istruzioni relative a tutti gli scenari di distribuzione vedere [Panoramica di Site Recovery](site-recovery-overview.md).


### Configurare la protezione per il cluster di SQL Server (2012 o 2014 Enterprise)

Se SQL Server usa gruppi di disponibilità per la disponibilità elevata o un'istanza del cluster di failover, è consigliabile utilizzare gruppi di disponibilità anche nel sito di ripristino. Queste linee guida sono valide per le applicazioni che non usano transazioni distribuite.

##### Da sito locale a sito locale

1. [Configurare i database](https://msdn.microsoft.com/library/hh213078.aspx) in gruppi di disponibilità.
2. Creare una nuova rete virtuale nel sito secondario.
3. Configurare una VPN da sito a sito tra la nuova rete virtuale e il sito primario.
4. Creare una macchina virtuale nel sito di ripristino e installarvi SQL Server.
5. Estendere i gruppi di disponibilità AlwaysOn esistenti alla nuova macchina virtuale di SQL Server. Configurare l'istanza di SQL Server come una copia di replica asincrona.
6. Creare un listener del gruppo di disponibilità o aggiornare il listener esistente per includere la macchina virtuale di replica asincrona.
7. Assicurarsi che la farm di applicazione sia configurata tramite il listener. Se è configurata tramite il nome del server di database, aggiornarla in modo che utilizzi il listener così che non sia necessario riconfigurarla dopo il failover.

#### Da sito locale ad Azure

Quando si esegue la replica in Azure, la configurazione di più gruppi di disponibilità è complicata perché ogni gruppo di disponibilità necessita di un listener dedicato e la configurazione di ciascun listener richiede un servizio cloud separato. Si consiglia di configurare un gruppo di disponibilità con tutti i database inclusi.

1. Creare una rete virtuale di Azure
2. Configurare una VPN da sito a sito tra il sito locale e la rete.
3. Creare una nuova macchina virtuale SQL Server di Azure e configurarla come una replica del gruppo di disponibilità asincrona. Se è necessaria una disponibilità elevata per il livello di SQL Server dopo il failover in Azure, configurare due copie di replica asincrona in Azure.
4. Configurare una replica del controller di dominio nella rete virtuale.
5. Assicurarsi che siano abilitate le estensioni di macchina virtuale nella macchina virtuale. Questa operazione è necessaria per eseguire il push degli script specifici per SQL Server in un piano di ripristino.
6. Configurare un listener di SQL Server per il gruppo di disponibilità tramite il servizio di bilanciamento del carico interno di Azure.
7. Configurare il livello applicazione per usare il listener per accedere al livello database. Per le applicazioni che usano transazioni distribuite si consiglia di usare Site Recovery con la replica SAN o la replica VMWare da sito a sito.

### Configurare la protezione per il cluster di SQL Server (Standard o 2008 R2)

Per un cluster che esegue l’edizione SQL Server Standard o SQL Server 2008 R2 è consigliabile usare la replica di Site Recovery per proteggere SQL Server.

#### Da sito locale a sito locale

- Per un ambiente Hyper-V, se l'applicazione usa transazioni distribuite è consigliabile distribuire [Site Recovery con la replica SAN](site-recovery-vmm-san.md).

- Per un ambiente VMware è consigliabile distribuire la protezione [VMware per VMware](site-recovery-vmware-to-vmware.md).

#### Da sito locale ad Azure

Site Recovery non fornisce il supporto di cluster guest durante la replica in Azure. Inoltre, SQL Server non fornisce una soluzione di ripristino di emergenza a costo contenuto per l'edizione Standard. È consigliabile proteggere il cluster di SQL Server locale in un SQL Server autonomo e ripristinarlo in Azure.


1. Configurare un'istanza di SQL Server autonoma aggiuntiva nel sito locale.
2. Configurare questa istanza come mirroring per i database che richiedono protezione. Configurare il mirroring in modalità di protezione elevata
3.	Configurare Site Recovery nel sito locale in base all'ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md)).
4.	Usare la replica di Site Recovery per eseguire la replica della nuova istanza di SQL Server in Azure. Si tratta di una copia mirror a protezione elevata, per cui verrà sincronizzata con il cluster primario, ma verrà replicata in Azure tramite la replica di Site Recovery.

Nell'immagine seguente viene illustrata questa configurazione.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##Creare piani di ripristino

I piani di ripristino raggruppano i computer che devono essere sottoposti a failover contemporaneamente. Acquisire ulteriori informazioni su [piani di ripristino](site-recovery-create-recovery-plans.md) e [failover](site-recovery-failover.md) prima di iniziare.


### Creare un piano di ripristino per i cluster di SQL Server (SQL Server 2012/2014 Enterprise)

#### Configurare gli script di SQL Server per il failover in Azure

In questo scenario vengono usati script personalizzati e automazione di Azure per i piani di ripristino, al fine di configurare un failover tramite script dei gruppi di disponibilità di SQL Server.

1.	Creare un file locale per fare in modo che lo script esegua il failover di un gruppo di disponibilità. Questo script di esempio consente di specificare un percorso per il gruppo di disponibilità nella replica di Azure e di eseguire il failover in tale istanza di replica. Questo script viene eseguito nella macchina virtuale di replica di SQL Server con l'estensione di script personalizzato.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Caricare lo script presente in un blob in un account di archiviazione di Azure. Esempio:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Creare un runbook di automazione di Azure per richiamare gli script nella macchina virtuale di replica di SQL Server in Azure. Per eseguire questa operazione usare questo script di esempio. Acquisire [ulteriori informazioni ](site-recovery-runbook-automation.md) sull'uso di runbook di automazione in piani di ripristino.

    	workflow SQLAvailabilityGroupFailover
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
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	Quando si crea un piano di ripristino per l'applicazione, aggiungere un passaggio di script "pre-Group 1 boot" che richiami il runbook di automazione per eseguire il failover dei gruppi di disponibilità.

#### Configurare gli script di SQL Server per il failover in un sito secondario

1. Aggiungere questo script di esempio nella libreria VMM nei siti primari e secondari.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Quando si crea un piano di ripristino per l'applicazione, aggiungere un passaggio di script "pre-Group 1 boot" che richiami lo script per eseguire il failover dei gruppi di disponibilità.

### Creare un piano di ripristino per i cluster di SQL Server (Standard)

#### Configurare gli script di SQL Server per il failover in Azure

1.	Creare un file locale per fare in modo che lo script esegua il failover nel mirror del database di SQL Server. Usare questo script di esempio.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Caricare lo script presente in un blob in un account di archiviazione di Azure. Usare questo script di esempio.

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Creare un runbook di automazione di Azure per richiamare lo script nella macchina virtuale di replica di SQL Server in Azure. Per eseguire questa operazione usare questo script di esempio. Acquisire [ulteriori informazioni ](site-recovery-runbook-automation.md) sull'uso di runbook di automazione in piani di ripristino. Prima di procedere assicurarsi che l'agente di macchina virtuale sia in esecuzione sulla macchina virtuale di SQL Server sottoposta a failover.

    	workflow SQLAvailabilityGroupFailover
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
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. Aggiungere questi passaggi nel piano di ripristino per eseguire il failover del livello di SQL Server:

	- Per il failover pianificato, aggiungere uno script lato primario per arrestare il cluster primario dopo l’“Arresto del gruppo”.
	- Aggiungere la macchina virtuale mirror del database di SQL Server al piano di ripristino, preferibilmente nel primo gruppo di avvio.
3.	Aggiungere uno script di post failover per eseguire il failover della copia mirror all'interno di questa macchina virtuale usando lo script di automazione precedente. Poiché il nome dell'istanza di database è cambiato, sarà necessario riconfigurare il livello applicazione per usare il nuovo database.


#### Configurare gli script di SQL Server per il failover in un sito secondario

1.	Aggiungere questo script di esempio nella libreria VMM nei siti primari e secondari.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Aggiungere la macchina virtuale mirror del database di SQL Server al piano di ripristino, preferibilmente nel primo gruppo di avvio.
3.	Aggiungere uno script di post failover per eseguire il failover della copia mirror all'interno di questa macchina virtuale usando lo script VMM precedente. Poiché il nome dell'istanza di database è cambiato, sarà necessario riconfigurare il livello applicazione per usare il nuovo database.





## Considerazioni sul failover di test

Se si usano gruppi di disponibilità AlwaysOn, non è possibile eseguire un failover di test del livello di SQL Server. In alternativa, considerare queste opzioni:

- Opzione 1

	1. Eseguire un failover di test dei livelli applicazione e front-end.
	2. Aggiornare il livello applicazione per accedere alla copia di replica in modalità sola lettura ed eseguire un test di sola lettura dell'applicazione.

- Opzione 2
1.	Creare una copia dell'istanza della macchina virtuale di SQL Server di replica (usano il clone VMM per il backup da sito a sito o Azure) e aprirla in una rete di test
2.	Eseguire il failover di test usando il piano di ripristino.

## Considerazioni sul failback

Per i cluster SQL standard, il failback dopo un failover non pianificato richiederà un backup e un ripristino di SQL Server dall'istanza del mirror al cluster originale e quindi una ridefinizione del mirror.





 

<!---HONumber=July15_HO2-->