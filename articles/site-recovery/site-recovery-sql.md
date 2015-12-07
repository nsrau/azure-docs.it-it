<properties 
	pageTitle="Ripristino di emergenza con SQL Server e Azure Site Recovery | Microsoft Azure" 
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
	ms.date="11/18/2015" 
	ms.author="raynew"/>


# Ripristino di emergenza con SQL Server e Azure Site Recovery 

Site Recovery è un servizio di Azure che favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino di macchine virtuali e server fisici. Site Recovery supporta una serie di meccanismi di replica per la protezione, la replica e il failover coerenti delle macchine in Azure o in un centro dati secondario. Per una panoramica di tutti gli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

 In questo articolo viene descritto come proteggere il backend SQL Server di un'applicazione usando una combinazione di Site Recovery e tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server. Prima di distribuire gli scenari descritti in questo articolo, è necessario avere una buona conoscenza di Site Recovery e delle funzionalità di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server (clustering di failover, gruppi di disponibilità AlwaysOn, mirroring del database, log shipping).



## Panoramica

Molti carichi di lavoro usano SQL Server come base. Applicazioni come SharePoint, Dynamics e SAP usano SQL Server per implementare i servizi di dati. Le funzionalità di disponibilità elevata e ripristino di emergenza di SQL Server come i gruppi di disponibilità di SQL Server vengono usate per proteggere e recuperare i database di SQL Server. Le applicazioni distribuiscono SQL Server nelle configurazioni seguenti


1. SQL Server autonomo: SQL Server e tutti i database sono ospitati in un singolo computer (fisico o una macchina virtuale). Se virtualizzato, il clustering degli host è utilizzato per la disponibilità elevata locale (HA) e non esiste alcun livello guest HA implementato. 
2.	Istanze di clustering di failover di SQL Server (note anche come Always ON FCI): In questa configurazione, 2 o più nodi di istanze di SQL server con dischi condivisi sono configurati in un Cluster di failover di Windows. Se una delle istanze cluster di failover di SQL non è attiva, il cluster può eseguire il failover di SQL in un'altra istanza. Il programma di installazione viene in genere utilizzato per la disponibilità elevata nel sito primario. Questo non protegge da errori o da interruzioni nel livello di archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, Fibre Channel o VHDX condiviso.
3.	Gruppi di disponibilità Always ON di SQL: in questa configurazione, 2 nodi sono configurati in un cluster non condiviso con database SQL configurati in un gruppo di disponibilità con replica sincrona e failover automatico.

SQL Server fornisce inoltre tecnologie di ripristino di emergenza native nelle edizioni Enterprise per il ripristino dei database in un sito remoto. Ove applicabile, verranno sfruttate e integrate le seguenti tecnologie SQL di ripristino di emergenza native per la creazione del piano di ripristino di emergenza basato su Azure Site Recovery


1. Gruppo di disponibilità Always On di SQL da Ripristino di emergenza per SQL 2012 o 2014 edizione Enterprise 
2.	Mirroring del Database SQL in modalità protezione elevata per SQL server standard (qualsiasi versione) o SQL server 2008 R2



Site Recovery può proteggere un'istanza di SQL Server in esecuzione in una macchina virtuale Hyper-V, una macchina virtuale VMware o un server fisico.

 |**Da sito locale a sito locale** | **Da sito locale ad Azure** 
---|---|---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì 
**Server fisico** | Sì | Sì


## Supporto e integrazione

Di seguito sono elencate le versioni ed edizioni di SQL Server che sono applicabili a questo articolo:


- SQL Server 2014 Enterprise e Standard
- SQL Server 2012 Enterprise e Standard
- SQL Server 2008 R2 Enterprise e Standard


Site Recovery si integra con le tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server native riepilogate nella tabella seguente per offrire una soluzione di ripristino di emergenza.

**Funzionalità** |**Dettagli** | **Versione di SQL Server** 
---|---|---
**Gruppo di disponibilità AlwaysOn** | <p>Più istanze autonome di SQL Server eseguite ognuna in un cluster di failover con più nodi.</p> <p>I database possono essere raggruppati in gruppi di failover che possono essere copiati (con mirroring) in istanze di SQL Server in modo che non sia necessaria alcuna archiviazione condivisa.</p> <p>Fornisce il ripristino di emergenza tra un sito primario e uno o più siti secondari. Due nodi possono essere configurati in un cluster non condiviso con i database di SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico.</p> | Edizione SQL Server 2014/2012 Enterprise
**Clustering di failover (AlwaysOn FCI)** | <p>SQL Server usa il clustering di failover di Windows per l'elevata disponibilità dei carichi di lavoro SQL Server locali.</p><p>I nodi che eseguono le istanze di SQL Server con dischi condivisi sono configurati in un cluster di failover. Se un'istanza non è attiva, il failover del cluster viene eseguito su un'altra istanza.</p> <p>Il cluster non protegge da errori o interruzioni nell'archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, Fibre Channel o VHDX condiviso.</p> | Edizioni SQL Server Enterprise</p> <p>SQL Server Standard Edition (limitata a soli due nodi)
**Mirroring del database (modalità di protezione elevata)** | Protegge un singolo database in una singola copia secondaria. Disponibile nelle modalità di replica a protezione elevata (sincrona) e a prestazione elevata (asincrona). Non richiede un cluster di failover. | <p>SQL Server 2008 R2</p><p>Tutte le edizioni di SQL Server Enterprise</p>
**SQL Server autonomo** | SQL Server e il database si trovano in un singolo server (fisico o virtuale). Il clustering dell'host viene usato per la disponibilità elevata se il server è virtuale. Nessuna disponibilità elevata a livello di guest. | Edizione Enterprise o Standard




Nella tabella seguente vengono riepilogati i suggerimenti per l'integrazione delle tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server nella distribuzione di Site Recovery.

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
- Prerequisiti per lo scenario che si vuole distribuire. I prerequisiti sono disponibili in ciascun articolo relativo alla distribuzione. Tali prerequisiti sono elencati e collegati nella [Panoramica su Site Recovery](site-recovery-overview.md).
- Se si desidera configurare il ripristino in Azure, è necessario eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) nelle macchine virtuali di SQL Server per assicurarsi che siano compatibili con Azure e Site Recovery.


## Configurare la protezione di AD

Per fare in modo che SQL Server venga eseguito correttamente, è necessario Active Directory nel sito di ripristino secondario. Sono disponibili due opzioni:

- **Piccola impresa**: se si dispone di un numero ridotto di applicazioni e di un singolo controller di dominio per il sito locale e si vuole eseguire il failover dell'intero sito, è consigliabile usare la replica di Site Recovery per replicare il controller di dominio nel centro dati secondario o in Azure.

- **Media o grande impresa**: se si dispone di un numero elevato di applicazioni, si utilizza un insieme di strutture Active Directory e si vuole eseguire il failover dell'applicazione o del carico di lavoro, è consigliabile configurare un controller di dominio aggiuntivo nel centro dati o in Azure. Se si usano gruppi di disponibilità AlwaysOn per ripristinare un sito remoto è consigliabile configurare un altro controller di dominio aggiuntivo nel sito secondario o in Azure, da utilizzare per l'istanza di SQL Server ripristinata.

Le istruzioni riportate in questo documento presuppongono che un controller di dominio sia disponibile nella posizione secondaria. È possibile fare riferimento alla guida alla soluzione di ripristino di emergenza (DR) di Active Directory (AD) [qui](http://aka.ms/asr-ad).

## Integrazione con SQL AlwaysOn in Azure

### Da sito locale ad Azure

Azure Site Recovery (ASR) supporta in modo nativo SQL AlwaysOn. Se è stato creato un gruppo di disponibilità SQL con una macchina virtuale di Azure impostato come database 'secondario', è possibile utilizzare ASR per gestire il failover dei gruppi di disponibilità.

Questa funzionalità è attualmente in anteprima ed è disponibile quando il datacenter principale è gestito da System Center Virtual Machine Manager (VMM).

#### Ambienti gestiti da un server VMM
Se si passa all'interno di un insieme di credenziali di ASR, si nota una scheda per i server SQL sotto la scheda Elementi protetti.

![Elementi protetti](./media/site-recovery-sql/protected-items.png)

Di seguito sono riportati i passaggi per integrare SQL AlwaysOn con ASR.

##### Prerequisiti
- Un server SQL locale nel server autonomo o un cluster di failover 
- Una o più macchine virtuali di Azure con SQL Server installato.
- Impostazione del gruppo di disponibilità di SQL tra server SQL Server locale e server SQL in esecuzione in Azure
- La comunicazione remota di PowerShell deve essere abilitata sul server SQL locale. Il server VMM deve essere in grado di effettuare chiamate remote di PowerShell a SQL Server
- Nel server SQL locale è necessario aggiungere un account utente ai gruppi di utenti SQL con almeno le seguenti autorizzazioni
	- ALTER AVAILABILITY GROUP: [riferimento 1](https://msdn.microsoft.com/it-IT/library/hh231018.aspx), [riferimento 2](https://msdn.microsoft.com/it-IT/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: [riferimento 1](https://msdn.microsoft.com/it-IT/library/ff877956.aspx#Security)
- È necessario creare un account RunAs nel server VMM per l'account nel passaggio precedente
- Il modulo SQL PS deve essere installato sui server SQL in esecuzione in locale e sulle macchine virtuali di Azure
- VM Agent deve essere installato nelle macchine virtuali in esecuzione in Azure
- NTAUTHORITY\\System deve disporre delle autorizzazioni seguenti in SQL Server in esecuzione su macchine virtuali in Azure
	- ALTER AVAILABILITY GROUP: [riferimento 1](https://msdn.microsoft.com/it-IT/library/hh231018.aspx), [riferimento 2](https://msdn.microsoft.com/it-IT/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: [riferimento 1](https://msdn.microsoft.com/it-IT/library/ff877956.aspx#Security)

##### 1\. Aggiunta di un server SQL

Fare clic su Aggiungi SQL per aggiungere un nuovo server SQL.

![Aggiungi SQL](./media/site-recovery-sql/add-sql.png)

Fornire dettagli del server SQL, VMM e credenziali da utilizzare per la gestione del server SQL.

![Finestra di dialogo Aggiungi SQL](./media/site-recovery-sql/add-sql-dialog.png)

###### Parametri
 - Nome: nome descrittivo che si desidera fornire per fare riferimento a questo server SQL
 - Server SQL (FQDN): nome di dominio completo (FQDN) del server SQL di origine che si desidera aggiungere. Nel caso in cui il server SQL sia installato in un cluster di failover, fornire l’FQDN del cluster e non di uno dei nodi del cluster. 
 - Istanza di SQL Server: scegliere l'istanza di SQL predefinita o specificare il nome dell'istanza SQL personalizzata.
 - Server VMM: selezionare uno dei server VMM che è già stato registrato con Azure Site Recovery (ASR). ASR utilizzerà questo server VMM per comunicare con SQL Server
 - ACCOUNT RUNAS: fornire il nome di un account RunAs creato nel server VMM selezionato precedentemente. Questo account RunAs verrà utilizzato per accedere al server SQL e deve disporre di autorizzazioni di lettura e failover nei gruppi di disponibilità nel server SQL. 

Dopo aver aggiunto il server SQL, esso verrà visualizzato nella scheda Server SQL.

![Elenco di server SQL](./media/site-recovery-sql/sql-server-list.png)

##### 2\. Aggiunta di un gruppo di disponibilità SQL

Una volta aggiunto il server SQL, il passaggio successivo consiste nell'aggiungere i gruppi di disponibilità ad ASR. A tale scopo, fare clic sui server SQL aggiunti nel passaggio precedente e fare clic su Aggiungi gruppo di disponibilità SQL.

![Aggiungi gruppo di disponibilità SQL](./media/site-recovery-sql/add-sqlag.png)

Il gruppo di disponibilità SQL si può replicare in una o più macchine virtuali in Azure. Quando si aggiunge il gruppo di disponibilità sql, è necessario fornire il nome e una sottoscrizione della macchina virtuale di Azure in cui si desidera che venga eseguito il failover da ASR.

![Finestra di dialogo Aggiungi gruppo di disponibilità SQL](./media/site-recovery-sql/add-sqlag-dialog.png)

Nell'esempio precedente Gruppo di disponibilità DB1-AG diventa Primario nella macchina virtuale SQLAGVM2 in esecuzione all'interno della sottoscrizione DevTesting2 in un failover.

>[AZURE.NOTE]Solo i gruppi di disponibilità primari nel server SQL aggiunto nel passaggio precedente sono disponibili per l’aggiunta ad ASR. Se è stato creato un gruppo di disponibilità primario nel server SQL o se sono stati aggiunti ulteriori gruppi di disponibilità al server SQL dopo la sua aggiunta, aggiornarlo utilizzando l'opzione disponibile nel server SQL.

#### 3\. Creazione di un piano di ripristino

Il passaggio successivo consiste nel creare un piano di ripristino utilizzando macchine virtuali e gruppi di disponibilità. Selezionare lo stesso server VMM utilizzato nel passaggio 1 come origine e Microsoft Azure come destinazione.

![Create Recovery Plan](./media/site-recovery-sql/create-rp1.png)

![Create Recovery Plan](./media/site-recovery-sql/create-rp2.png)

Nell'esempio l'applicazione di Sharepoint è costituita da 3 macchine virtuali che utilizzano un gruppo di disponibilità SQL come backend. In questo piano di ripristino potremmo selezionare sia il gruppo di disponibilità che la macchina virtuale che costituiscono l'applicazione.

È possibile personalizzare ulteriormente il piano di ripristino tramite lo spostamento di macchine virtuali in gruppi di failover diversi per la sequenza dell'ordine di failover. Il failover del gruppo di disponibilità viene sempre eseguito prima, poiché sarebbe essere utilizzato come backend di qualsiasi applicazione.

![Personalizzare il piano di ripristino](./media/site-recovery-sql/customize-rp.png)

#### 4\. Failover

Dopo aver aggiunto un gruppo di disponibilità a un piano di ripristino, sono disponibili diverse opzioni di failover.

##### Failover pianificato

Il failover pianificato implica un failover senza perdita di dati. Per ottenere questo, la modalità di disponibilità del gruppo di disponibilità SQL viene impostata prima su Sincrona e quindi viene attivato un failover per rendere il gruppo di disponibilità primario nella macchina virtuale fornita durante l'aggiunta del gruppo di disponibilità ad ASR. Una volta completato il failover, la modalità di disponibilità viene impostata sullo stesso valore che aveva prima che il failover pianificato venisse attivato.

##### Failover non pianificato

Il failover non pianificato può generare perdite di dati. Durante l’attivazione del failover non pianificato, la modalità di disponibilità del gruppo di disponibilità non viene modificata e il gruppo di disponibilità viene reso primario nella macchina virtuale fornita durante l'aggiunta del gruppo di disponibilità ad ASR. Quando il failover non pianificato è completato e il server locale che esegue SQL Server è nuovamente disponibile, la replica inversa deve essere attivata nel gruppo di disponibilità. Si noti che questa azione non è disponibile nel piano di ripristino e può essere eseguita dal gruppo di disponibilità SQL nella scheda Server SQL

##### Failover di test
Il failover di test per il gruppo di disponibilità di SQL non è supportato. Se si attiva il failover di test di un piano di ripristino che contiene il gruppo di disponibilità di SQL, il failover viene ignorato per il gruppo di disponibilità.

In alternativa, considerare queste opzioni:

###### Opzione 1



1. Eseguire un failover di test dei livelli applicazione e front-end.

2. Aggiornare il livello applicazione per accedere alla copia di replica in modalità sola lettura ed eseguire un test di sola lettura dell'applicazione.

###### Opzione 2

1.	Creare una copia dell'istanza della macchina virtuale di SQL Server di replica (usano il clone VMM per il backup da sito a sito o Azure) e aprirla in una rete di test
2.	Eseguire il failover di test usando il piano di ripristino.

#### Failback

Se si desidera rendere nuovamente primario il gruppo di disponibilità sul Server SQL locale, è possibile eseguire l’operazione attivando il failover pianificato nel piano di ripristino e scegliendo la direzione da Microsoft Azure al server VMM locale

#### Replica inversa

Dopo un failover non pianificato, è necessario attivare la replica inversa nel gruppo di disponibilità per riprendere la replica. Fino a quando non viene eseguita questa operazione, la replica rimane sospesa.


### Ambienti non gestiti da un server VMM

Per gli ambienti non gestiti da un server VMM, è possibile utilizzare i runbook di automazione di Azure per configurare un failover tramite script dei gruppi di disponibilità SQL. Di seguito sono riportati i passaggi di configurazione per:


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

###Da sito locale a sito locale
Se SQL Server usa gruppi di disponibilità per la disponibilità elevata o un'istanza del cluster di failover, è consigliabile utilizzare gruppi di disponibilità anche nel sito di ripristino. Queste linee guida sono valide per le applicazioni che non usano transazioni distribuite.


1. [Configurare i database](https://msdn.microsoft.com/library/hh213078.aspx) in gruppi di disponibilità.
2. Creare una nuova rete virtuale nel sito secondario.
3. Configurare una VPN da sito a sito tra la nuova rete virtuale e il sito primario.
4. Creare una macchina virtuale nel sito di ripristino e installarvi SQL Server.
5. Estendere i gruppi di disponibilità AlwaysOn esistenti alla nuova macchina virtuale di SQL Server. Configurare l'istanza di SQL Server come una copia di replica asincrona.
6. Creare un listener del gruppo di disponibilità o aggiornare il listener esistente per includere la macchina virtuale di replica asincrona.
7. Assicurarsi che la farm di applicazione sia configurata tramite il listener. Se è configurata tramite il nome del server di database, aggiornarla in modo che utilizzi il listener così che non sia necessario riconfigurarla dopo il failover.

Per le applicazioni che usano transazioni distribuite si consiglia di usare [Site Recovery con la replica SAN](site-recovery-vmm-san.md) o la [replica VMware da sito a sito](site-recovery-vmware-to-vmware.md).

#### Considerazioni sul piano di ripristino

1. Aggiungere questo script di esempio nella libreria VMM nei siti primari e secondari.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Quando si crea un piano di ripristino per l'applicazione, aggiungere un passaggio di script "pre-Group 1 boot" che richiami lo script per eseguire il failover dei gruppi di disponibilità.



## Configurare la protezione per un'istanza di SQL Server autonoma


In questa configurazione è consigliabile usare la replica di Site Recovery per proteggere il computer SQL Server. La procedura varia a seconda che SQL Server sia configurato come una macchina virtuale o un server fisico e che si voglia eseguire la replica in Azure o in un sito locale secondario. Per istruzioni relative a tutti gli scenari di distribuzione vedere [Panoramica di Site Recovery](site-recovery-overview.md).


## Configurare la protezione per il cluster di SQL Server (Standard o 2008 R2)

Per un cluster che esegue l’edizione SQL Server Standard o SQL Server 2008 R2 è consigliabile usare la replica di Site Recovery per proteggere SQL Server.

#### Da sito locale a sito locale

- Se l'applicazione usa transazioni distribuite è consigliabile distribuire [Site Recovery con la replica SAN](site-recovery-vmm-san.md) per un ambiente Hyper-V e [VMware a VMware](site-recovery-vmware-to-vmware.md) per gli ambienti VMware.

- Per le applicazioni non DTC, utilizzare l'approccio precedente per ripristinare il cluster come server autonomo utilizzando un mirror del database ad alta protezione locale.

#### Da sito locale ad Azure

Site Recovery non fornisce il supporto di cluster guest durante la replica in Azure. Inoltre, SQL Server non fornisce una soluzione di ripristino di emergenza a costo contenuto per l'edizione Standard. È consigliabile proteggere il cluster di SQL Server locale in un SQL Server autonomo e ripristinarlo in Azure.


1. Configurare un'istanza di SQL Server autonoma aggiuntiva nel sito locale.
2. Configurare questa istanza come mirroring per i database che richiedono protezione. Configurare il mirroring in modalità di protezione elevata
3.	Configurare Site Recovery nel sito locale in base all'ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md)).
4.	Usare la replica di Site Recovery per eseguire la replica della nuova istanza di SQL Server in Azure. Si tratta di una copia mirror a protezione elevata, per cui verrà sincronizzata con il cluster primario, ma verrà replicata in Azure con la replica di Site Recovery.

Nell'immagine seguente viene illustrata questa configurazione.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Considerazioni sul failback

Per i cluster SQL standard, il failback dopo un failover non pianificato richiede un backup e ripristino di SQL dall'istanza del mirror al cluster originale e quindi una ridefinizione del mirror.










 

<!---HONumber=AcomDC_1125_2015-->