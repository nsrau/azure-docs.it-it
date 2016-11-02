<properties 
	pageTitle="Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery | Microsoft Azure" 
	description="Questo articolo descrive come eseguire la replica di SQL Server tramite le funzionalità di ripristino di emergenza di SQL Server e Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2016" 
	ms.author="raynew"/>  


# Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery 


Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una panoramica rapida, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md).

 Questo articolo descrive come proteggere il back-end SQL Server di un'applicazione usando una combinazione di Azure Site Recovery e tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server. Prima di distribuire gli scenari descritti in questo articolo, è necessario avere una buona conoscenza delle funzionalità di ripristino di emergenza (clustering di failover, gruppi di disponibilità AlwaysOn, mirroring del database, log shipping) di SQL Server e di Azure Site Recovery.



## Overview

Molti carichi di lavoro usano SQL Server come base. Applicazioni come SharePoint, Dynamics e SAP usano SQL Server per implementare i servizi di dati. Le applicazioni distribuiscono SQL Server in diversi modi:

- **SQL Server autonomo**: SQL Server e tutti i database sono ospitati in un singolo computer (fisico o una macchina virtuale). Se è virtualizzato, il clustering dell'host viene usato per la disponibilità elevata. Non viene implementata alcuna disponibilità elevata a livello di guest.
- **Istanze di clustering di failover di SQL Server (AlwaysOn FCI)**: in questa configurazione due o più nodi di istanze di SQL Server con dischi condivisi vengono configurati in un cluster di failover di Windows. Se una delle istanze cluster non è attiva, il cluster può eseguire il failover di SQL Server in un'altra istanza. Questa configurazione in genere viene usata per la disponibilità elevata in un sito primario. Non protegge da errori o da interruzioni nel livello di archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, Fibre Channel o VHDX condiviso.
- **Gruppi di disponibilità AlwaysOn di SQL**: in questa configurazione due nodi vengono configurati in un cluster non condiviso con database di SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico.

Nelle edizioni Enterprise, SQL Server fornisce inoltre tecnologie di ripristino di emergenza native per il ripristino dei database in un sito remoto. In questo articolo si userà e si eseguirà l'integrazione di queste tecnologie di ripristino di emergenza SQL native:

- Gruppi di disponibilità AlwaysOn di SQL per il ripristino di emergenza per SQL Server 2012 o 2014 Enterprise.
- Mirroring del database SQL in modalità protezione elevata per SQL Server Standard (qualsiasi versione) o per SQL Server 2008 R2.


Site Recovery può proteggere SQL Server, come riepilogato nella tabella.

 |**Da sito locale a sito locale** | **Da sito locale ad Azure** 
---|---|---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì 
**Server fisico** | Sì | Sì


## Supporto e integrazione

Queste versioni di SQL Server sono supportate dagli scenari in questo articolo:


- SQL Server 2014 Enterprise e Standard
- SQL Server 2012 Enterprise e Standard
- SQL Server 2008 R2 Enterprise e Standard


Site Recovery si integra con le tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server native riepilogate nella tabella seguente per offrire una soluzione di ripristino di emergenza.

**Funzionalità** |**Dettagli** | **Versione di SQL Server** 
---|---|---
**Gruppo di disponibilità AlwaysOn** | Ognuna delle istanze autonome multiple di SQL Server viene eseguita in un cluster di failover con più nodi.<br/><br/>I database possono essere raggruppati in gruppi di failover che è possibile copiare (con mirroring) in istanze di SQL Server in modo che non sia necessaria alcuna archiviazione condivisa.<br/><br/>Garantisce il ripristino di emergenza tra un sito primario e uno o più siti secondari. Due nodi possono essere configurati in un cluster non condiviso con i database di SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico. | SQL Server 2014 e 2012 Enterprise
**Clustering di failover (AlwaysOn FCI)** | SQL Server usa il clustering di failover di Windows per l'elevata disponibilità dei carichi di lavoro SQL Server locali.<br/><br/>I nodi che eseguono le istanze di SQL Server con dischi condivisi sono configurati in un cluster di failover. Se un'istanza non è attiva, il cluster esegue il failover in un'altra istanza.<br/><br/>Il cluster non protegge da errori o interruzioni nell'archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, fibre channel o VHDX condivisi. | Edizioni SQL Server Enterprise<br/><br/>Edizione SQL Server Standard (limitata a soli due nodi)
**Mirroring del database (modalità di protezione elevata)** | Protegge un singolo database in una singola copia secondaria. Disponibile nelle modalità di replica a protezione elevata (sincrona) e a prestazione elevata (asincrona). Non richiede un cluster di failover. | SQL Server 2008 R2<br/><br/>Tutte le edizioni di SQL Server Enterprise
**SQL Server autonomo** | SQL Server e il database si trovano in un singolo server (fisico o virtuale). Il clustering dell'host viene usato per la disponibilità elevata se il server è virtuale. Nessuna disponibilità elevata a livello di guest. | Edizione Enterprise o Standard

## Indicazioni di distribuzione


Nella tabella seguente vengono riepilogate le indicazioni per l'integrazione delle tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server con Site Recovery.

**Versione** |**Edizione** | **Distribuzione** | **Da locale a locale** | **Da locale ad Azure** 
---|---|---|---|---
SQL Server 2014 o 2012 | Enterprise | Istanza del cluster di failover | Gruppi di disponibilità AlwaysOn | Gruppi di disponibilità AlwaysOn
| Enterprise | Gruppi di disponibilità AlwaysOn per la disponibilità elevata | Gruppi di disponibilità AlwaysOn | Gruppi di disponibilità AlwaysOn
 | Standard | Istanza del cluster di failover (FCI) | Replica di Site Recovery con mirror locale | Replica di Site Recovery con mirror locale
 | Enterprise o Standard | Autonoma | Replica di Site Recovery | Replica di Site Recovery
SQL Server 2008 R2 | Enterprise o Standard | Istanza del cluster di failover (FCI) | Replica di Site Recovery con mirror locale | Replica di Site Recovery con mirror locale
 | Enterprise o Standard | Autonoma | Replica di Site Recovery | Replica di Site Recovery
SQL Server (qualsiasi versione) | Enterprise o Standard | Istanza del cluster di failover: applicazione DTC | Replica di Site Recovery | Non supportato


## Prerequisiti di distribuzione

Di seguito sono riportati i prerequisiti necessari per iniziare:

- Una distribuzione di SQL Server locale che esegue una versione supportata di SQL Server. In genere è necessario anche un server Active Directory per il server SQL.
- Prerequisiti per lo scenario che si vuole distribuire. I prerequisiti sono disponibili in ciascun articolo relativo alla distribuzione. I collegamenti a questi articoli sono disponibili in [Che cos'è Site Recovery?](site-recovery-overview.md).
- Se si desidera configurare il ripristino in Azure, è necessario eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) nelle macchine virtuali di SQL Server per assicurarsi che siano compatibili con Azure e Site Recovery.


## Configurare Active Directory

Per fare in modo che SQL Server venga eseguito correttamente, è necessario Active Directory nel sito di ripristino secondario. Sono disponibili due opzioni:

- **Piccola impresa**: se si dispone di un numero ridotto di applicazioni e di un singolo controller di dominio per il sito locale e si vuole eseguire il failover dell'intero sito, è consigliabile usare la replica di Site Recovery per replicare il controller di dominio nel centro dati secondario o in Azure.

- **Media o grande impresa**: se si dispone di un numero elevato di applicazioni, si utilizza un insieme di strutture Active Directory e si vuole eseguire il failover dell'applicazione o del carico di lavoro, è consigliabile configurare un controller di dominio aggiuntivo nel centro dati o in Azure. Se si usano gruppi di disponibilità AlwaysOn per ripristinare un sito remoto è consigliabile configurare un altro controller di dominio aggiuntivo nel sito secondario o in Azure, da utilizzare per l'istanza di SQL Server ripristinata.

Le istruzioni riportate in questo documento presuppongono che un controller di dominio sia disponibile nella posizione secondaria. [Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory con Site Recovery.

## Integrare la protezione con SQL Server AlwaysOn (in locale in Azure)


Site Recovery supporta in modo nativo SQL AlwaysOn. Se è stato creato un gruppo di disponibilità SQL con una macchina virtuale di Azure impostata come 'secondaria', è possibile usare Site Recovery per gestire il failover dei gruppi di disponibilità.

>[AZURE.NOTE] Questa funzionalità è attualmente in anteprima ed è disponibile quando i server host Hyper-V nel data center principale sono gestiti in cloud VMM e quando la configurazione di VMware è gestita da un [server di configurazione](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Attualmente questa funzionalità non è disponibile nel nuovo portale di Azure.

#### Prerequisiti

Elementi necessari per integrare SQL AlwaysOn con Site Recovery:

- Un'istanza di SQL Server locale (server autonomo o cluster di failover).
- Una o più macchine virtuali di Azure con SQL Server installato.
- Un gruppo di disponibilità di SQL configurato tra un'istanza di SQL Server locale e SQL Server in esecuzione in Azure
- La comunicazione remota di PowerShell deve essere abilitata nel computer SQL Server locale. Il server VMM o il server di configurazione deve essere in grado di effettuare chiamate remote di PowerShell al server SQL.
- Nell'istanza di SQL Server locale è necessario aggiungere un account utente ai gruppi di utenti SQL con almeno le autorizzazioni seguenti:
	- ALTER AVAILABILITY GROUP: [qui](https://msdn.microsoft.com/library/hh231018.aspx) e [qui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: [qui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- È necessario creare un account RunAs nel server VMM oppure un account nel server di configurazione usando CSPSConfigtool.exe per l'utente indicato nel passaggio precedente.
- Il modulo SQL PS deve essere installato in istanze di SQL Server in esecuzione in locale e nelle macchine virtuali di Azure
- L'agente VM deve essere installato nelle macchine virtuali in esecuzione in Azure
- NTAUTHORITY\\System deve disporre delle autorizzazioni seguenti in SQL Server in esecuzione su macchine virtuali in Azure:
	- ALTER AVAILABILITY GROUP: [qui](https://msdn.microsoft.com/library/hh231018.aspx) e [qui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: [qui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  Passaggio 1: Aggiungere un'istanza di SQL Server


1. Fare clic su **Aggiungi applicazione SQL** per aggiungere una nuova istanza del server SQL.

	![Aggiungi SQL](./media/site-recovery-sql/add-sql.png)  

2. In **Configura impostazioni SQL** > **Nome** specificare un nome descrittivo che faccia riferimento al server SQL.
3. **In SQL Server (nome di dominio completo)** specificare il nome di dominio completo (FQDN) dell'istanza del server SQL di origine da aggiungere. Nel caso in cui il server SQL sia installato in un cluster di failover, fornire l’FQDN del cluster e non di uno dei nodi del cluster.
4. In **Istanza di SQL Server** scegliere l'istanza predefinita o specificare il nome dell'istanza personalizzata.
5. In **Server di gestione** selezionare un server VMM o un server di configurazione registrato nell'insieme di credenziali di Site Recovery. Site Recovery usa questo server di gestione per comunicare con il server SQL.
6. In **Account RunAs** specificare il nome di un account RunAs creato nel server VMM specificato oppure l'account creato nel server di configurazione. Questo account viene usato per accedere a SQL Server e deve avere autorizzazioni di lettura e failover nei gruppi di disponibilità nel computer SQL Server.

	![Finestra di dialogo Aggiungi SQL](./media/site-recovery-sql/add-sql-dialog.png)  

Dopo aver aggiunto il server SQL, l'istanza verrà visualizzata nella scheda **SQL Server**.

![Elenco di server SQL](./media/site-recovery-sql/sql-server-list.png)  


#### Passaggio 2: Aggiungere un gruppo di disponibilità SQL

1. Dopo aver aggiunto il computer SQL Server, il passaggio successivo consiste nell'aggiungere i gruppi di disponibilità a Site Recovery. A tale scopo, fare clic sui server SQL aggiunti nel passaggio precedente e fare clic su Aggiungi gruppo di disponibilità SQL.

	![Aggiungi gruppo di disponibilità SQL](./media/site-recovery-sql/add-sqlag.png)  

2. Il gruppo di disponibilità SQL si può replicare in una o più macchine virtuali in Azure. Quando si aggiunge il gruppo di disponibilità SQL, è necessario specificare il nome e la sottoscrizione della macchina virtuale di Azure in cui si vuole che venga eseguito il failover da Site Recovery.

	![Finestra di dialogo Aggiungi gruppo di disponibilità SQL](./media/site-recovery-sql/add-sqlag-dialog.png)  

3. Nell'esempio precedente Gruppo di disponibilità DB1-AG diventa Primario nella macchina virtuale SQLAGVM2 in esecuzione all'interno della sottoscrizione DevTesting2 in un failover.

>[AZURE.NOTE] Solo i gruppi di disponibilità primari nell'istanza di SQL Server aggiunta nel passaggio precedente sono disponibili per l'aggiunta a Site Recovery. Se è stato creato un gruppo di disponibilità primario nel server SQL o se sono stati aggiunti ulteriori gruppi di disponibilità al server SQL dopo la sua aggiunta, aggiornarlo utilizzando l'opzione disponibile nel server SQL.

#### Passaggio 3: Creare un piano di ripristino

Il passaggio successivo consiste nel creare un piano di ripristino utilizzando macchine virtuali e gruppi di disponibilità. Selezionare lo stesso server VMM o di configurazione usato nel passaggio 1 come origine e Microsoft Azure come destinazione.

![Create Recovery Plan](./media/site-recovery-sql/create-rp1.png)  

![Create Recovery Plan](./media/site-recovery-sql/create-rp2.png)  

Nell'esempio l'applicazione di Sharepoint è costituita da 3 macchine virtuali che utilizzano un gruppo di disponibilità SQL come backend. In questo piano di ripristino potremmo selezionare sia il gruppo di disponibilità che la macchina virtuale che costituiscono l'applicazione.

È possibile personalizzare ulteriormente il piano di ripristino tramite lo spostamento di macchine virtuali in gruppi di failover diversi per la sequenza dell'ordine di failover. Il failover del gruppo di disponibilità viene sempre eseguito prima, poiché sarebbe essere utilizzato come backend di qualsiasi applicazione.

![Personalizzare il piano di ripristino](./media/site-recovery-sql/customize-rp.png)  

### Passaggio 4: Eseguire il failover

Dopo aver aggiunto un gruppo di disponibilità a un piano di ripristino, sono disponibili diverse opzioni di failover.

Failover | Dettagli
--- | ---
**Failover pianificato** | Il failover pianificato implica un failover senza perdita di dati. A tale scopo, la modalità di disponibilità del gruppo di disponibilità SQL viene impostata prima su Sincrona e quindi viene attivato un failover per rendere primario il gruppo di disponibilità nella macchina virtuale fornita durante l'aggiunta del gruppo di disponibilità a Site Recovery. Una volta completato il failover, la modalità di disponibilità viene impostata sullo stesso valore che aveva prima che il failover pianificato venisse attivato.
**Failover non pianificato** | Il failover non pianificato può generare perdite di dati. Durante l'attivazione del failover non pianificato, la modalità Disponibilità del gruppo di disponibilità non viene modificata e il gruppo di disponibilità viene reso primario nella macchina virtuale fornita durante l'aggiunta del gruppo di disponibilità a Site Recovery. Quando il failover non pianificato è completato e il server locale che esegue SQL Server è nuovamente disponibile, la replica inversa deve essere attivata nel gruppo di disponibilità. Si noti che questa azione non è disponibile nel piano di ripristino e può essere eseguita dal gruppo di disponibilità SQL nella scheda Server SQL
**Failover di test** | Il failover di test per il gruppo di disponibilità di SQL non è supportato. Se si attiva il failover di test di un piano di ripristino che contiene il gruppo di disponibilità di SQL, il failover viene ignorato per il gruppo di disponibilità.


Prendere in considerazione queste opzioni di failover.

Opzione | Dettagli
--- | ---
**Opzione 1** | 1\. Eseguire un failover di test dei livelli applicazione e front-end.<br/><br/>2. Aggiornare il livello applicazione per accedere alla copia di replica in modalità sola lettura ed eseguire un test di sola lettura dell'applicazione.
**Opzione 2** | 1\. Creare una copia dell'istanza della macchina virtuale del server SQL di replica (usando il clone VMM per il backup da sito a sito o Backup di Azure) e aprirla in una rete di test<br/><br/> 2. Eseguire il failover di test usando il piano di ripristino.

Passaggio 5: Eseguire il failback

Se si vuole rendere nuovamente primario il gruppo di disponibilità nell'istanza locale di Server SQL, attivare il failover pianificato nel piano di ripristino e scegliere la direzione da Microsoft Azure al server VMM locale.

>[AZURE.NOTE] Dopo un failover non pianificato, è necessario attivare la replica inversa nel gruppo di disponibilità per riprendere la replica. Fino a quando non viene eseguita questa operazione, la replica rimane sospesa.



### Proteggere i computer senza un server VMM o un server di configurazione

Per gli ambienti non gestiti da un server VMM o da un server di configurazione, è possibile usare i runbook di automazione di Azure per configurare un failover tramite script dei gruppi di disponibilità SQL. Di seguito sono riportati i passaggi di configurazione per:

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

## Integrare la protezione con SQL Server AlwaysOn (da locale a locale)

Se SQL Server usa gruppi di disponibilità per la disponibilità elevata o un'istanza del cluster di failover, è consigliabile utilizzare gruppi di disponibilità anche nel sito di ripristino. Queste linee guida sono valide per le applicazioni che non usano transazioni distribuite.

1. [Configurare i database](https://msdn.microsoft.com/library/hh213078.aspx) in gruppi di disponibilità.
2. Creare una nuova rete virtuale nel sito secondario.
3. Configurare una VPN da sito a sito tra la nuova rete virtuale e il sito primario.
4. Creare una macchina virtuale nel sito di ripristino e installarvi SQL Server.
5. Estendere i gruppi di disponibilità AlwaysOn esistenti alla nuova macchina virtuale di SQL Server. Configurare l'istanza di SQL Server come una copia di replica asincrona.
6. Creare un listener del gruppo di disponibilità o aggiornare il listener esistente per includere la macchina virtuale di replica asincrona.
7. Assicurarsi che la farm di applicazione sia configurata tramite il listener. Se è configurata tramite il nome del server di database, aggiornarla in modo che utilizzi il listener così che non sia necessario riconfigurarla dopo il failover.

Per le applicazioni che usano transazioni distribuite si consiglia di usare [Site Recovery con replica SAN](site-recovery-vmm-san.md) o la [replica di server VMware o fisici da sito a sito](site-recovery-vmware-to-vmware.md).

### Considerazioni sul piano di ripristino

1. Aggiungere questo script di esempio nella libreria VMM nei siti primari e secondari.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Quando si crea un piano di ripristino per l'applicazione, aggiungere un passaggio di script "pre-Group 1 boot" che richiami lo script per eseguire il failover dei gruppi di disponibilità.



## Proteggere un'istanza di SQL Server autonoma

In questa configurazione è consigliabile usare la replica di Site Recovery per proteggere il computer SQL Server. La procedura varia a seconda che SQL Server sia configurato come una macchina virtuale o un server fisico e che si voglia eseguire la replica in Azure o in un sito locale secondario. Per istruzioni relative a tutti gli scenari di distribuzione vedere [Panoramica di Site Recovery](site-recovery-overview.md).


## Proteggere un cluster di SQL Server (Standard o 2008 R2)

Per un cluster che esegue l’edizione SQL Server Standard o SQL Server 2008 R2 è consigliabile usare la replica di Site Recovery per proteggere SQL Server.

### Da sito locale a sito locale

- Se l'applicazione usa transazioni distribuite, è consigliabile distribuire [Site Recovery con replica SAN](site-recovery-vmm-san.md) per un ambiente Hyper-V e un [server VMware o fisico a VMware](site-recovery-vmware-to-vmware.md) per gli ambienti VMware.

- Per le applicazioni non DTC, utilizzare l'approccio precedente per ripristinare il cluster come server autonomo utilizzando un mirror del database ad alta protezione locale.

### Da sito locale ad Azure

Site Recovery non fornisce il supporto di cluster guest durante la replica in Azure. Inoltre, SQL Server non fornisce una soluzione di ripristino di emergenza a costo contenuto per l'edizione Standard. È consigliabile proteggere il cluster di SQL Server locale in un SQL Server autonomo e ripristinarlo in Azure.


1. Configurare un'istanza di SQL Server autonoma aggiuntiva nel sito locale.
2. Configurare questa istanza come mirroring per i database che richiedono protezione. Configurare il mirroring in modalità di protezione elevata
3.	Configurare Site Recovery nel sito locale in base all'ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [server VMware o fisico](site-recovery-vmware-to-azure-classic.md)).
4.	Usare la replica di Site Recovery per eseguire la replica della nuova istanza di SQL Server in Azure. Si tratta di una copia mirror a protezione elevata, per cui verrà sincronizzata con il cluster primario, ma verrà replicata in Azure con la replica di Site Recovery.

Nell'immagine seguente viene illustrata questa configurazione.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)  


### Considerazioni sul failback

Per i cluster SQL standard, il failback dopo un failover non pianificato richiede un backup e ripristino di SQL dall'istanza del mirror al cluster originale e quindi una ridefinizione del mirror.

## Passaggi successivi
[Altre informazioni](site-recovery-best-practices.md) sulla preparazione alla distribuzione di Site Recovery.










 

<!---HONumber=AcomDC_0810_2016-->