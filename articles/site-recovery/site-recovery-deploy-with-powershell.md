<properties
	pageTitle="Replicare le macchine virtuali Hyper-V nei cloud VMM usando Azure Site Recovery e PowerShell | Microsoft Azure"
	description="Informazioni su come automatizzare la replica delle macchine virtuali Hyper-V nei cloud VMM usando Azure Site Recovery e PowerShell."
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="lauraa"/>

# Replicare le macchine virtuali Hyper-V nei cloud VMM usando Azure Site Recovery e PowerShell


## Panoramica

Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

In questo articolo viene illustrato come utilizzare PowerShell per automatizzare attività comuni, che è necessario eseguire quando si configura Azure Site Recovery per replicare le macchine virtuali Hyper-V nei cloud VMM di System Center VMM nell'archiviazione di Azure.

L’articolo include i prerequisiti per lo scenario e mostra come configurare un insieme di credenziali di Azure Site Recovery, installare il provider di Azure Site Recovery sul server VMM di origine, registrare il server nell'insieme di credenziali, aggiungere un account di archiviazione di Azure, installare l'agente di Servizi di ripristino di Azure nei server host Hyper-V, configurare le impostazioni di protezione per i cloud VMM che verranno applicate a tutte le macchine virtuali protette e quindi abilitare la protezione per tali macchine virtuali. Terminare con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.

Nel caso di problemi di configurazione di questo scenario, inviare le proprie domande al [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Prima di iniziare

Assicurarsi che siano rispettati i prerequisiti seguenti:

### Prerequisiti di Azure

- È necessario un account [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/).
- Per archiviare i dati replicati, sarà necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. Dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione. [Altre informazioni sull'Archiviazione di Azure](../storage/storage-introduction.md).
- È necessario assicurarsi che le macchine virtuali da proteggere soddisfino i [prerequisiti delle macchine virtuali di Azure](site-recovery-best-practices.md#virtual-machines).

### Prerequisiti di VMM
- È necessario un server VMM in esecuzione su System Center 2012 R2.
- È necessario almeno un cloud nel server VMM da proteggere. Il cloud deve contenere:
	- Uno o più gruppi host VMM.
	- Uno o più cluster o server host Hyper-V in ogni gruppo host.
	- Una o più macchine virtuali nel server Hyper-V di origine.

### Prerequisiti di Hyper-V

- I server Hyper-V host devono eseguire almeno Windows Server 2012 con ruolo Hyper-V e disporre degli ultimi aggiornamenti installati.
- Se si esegue Hyper-V in un cluster, si noti che il gestore del cluster non viene creato automaticamente se viene usato un cluster basato su indirizzi IP statici. Sarà necessario configurare manualmente il broker del cluster. A tale scopo, in Gestione server > Gestione cluster di failover, connettersi al cluster, fare clic su **Configura ruolo** e selezionare **Gestore replica Hyper-V** nella schermata **Seleziona ruolo** della procedura guidata Disponibilità elevata. 
- Qualsiasi server o cluster Hyper-V per cui si vuole gestire la protezione deve essere incluso in un cloud VMM.

### Prerequisiti di mapping di rete
Quando si proteggono le macchine virtuali in Azure il mapping di rete mappa tra le reti VM nel server VMM di origine e le reti di Azure in un server VMM di destinazione per eseguire le operazioni seguenti:

- Tutte le macchine virtuali di cui viene eseguito il failover nella stessa rete possono connettersi tra loro, indipendentemente dal piano di ripristino di appartenenza.
- Se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali.
- Se non si configura il mapping delle reti, solo le macchine virtuali di cui viene eseguito il failover nello stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.

Per distribuire il mapping di rete sarà necessario quanto segue:

- Le macchine virtuali che si vuole proteggere nel server VMM di origine devono essere connesse a una rete VM. È necessario che tale rete sia collegata a una rete logica associata al cloud.
- Una rete di Azure a cui le macchine virtuali replicate possono connettersi dopo il failover. Questa rete viene selezionata al momento del failover. La rete deve trovarsi nella stessa area della sottoscrizione di Azure Site Recovery.
- Altre informazioni sul [mapping di rete](site-recovery-network-mapping.md):

###Prerequisiti di PowerShell
Assicurarsi che Azure PowerShell sia pronto all’uso. Se già si utilizza PowerShell, è necessario eseguire l'aggiornamento alla versione 0.8.10 o successiva. Per informazioni sulla configurazione di PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md). Dopo avere impostato e configurato PowerShell, è possibile vedere tutti i cmdlet disponibili per il servizio [qui](https://msdn.microsoft.com/library/dn850420.aspx).

Per i suggerimenti che facilitano l’utilizzo dei cmdlet, ad esempio come i valori dei parametri, gli input e gli output vengono in genere gestiti in Azure PowerShell, vedere [Introduzione ai cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Passaggio 1: impostare la sottoscrizione 

In PowerShell, eseguire questi cmdlet:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Sostituire gli elementi all'interno dei segni di minore/maggiore ("< >") con le informazioni specifiche.

## Passaggio 2: creare un insieme di credenziali di Ripristino sito

In PowerShell, sostituire gli elementi all'interno dei segni di minore/maggiore ("< >") con le informazioni specifiche ed eseguire questi comandi:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## Passaggio 3: generare una chiave di registrazione dell'insieme di credenziali

Generare una chiave di registrazione nell'insieme di credenziali. Dopo aver scaricato il provider di Azure Site Recovery e averlo installato sul server VMM, si userà questa chiave per registrare il server VMM nell'insieme di credenziali.

1.	Ottenere il file di impostazione dell'insieme di ottenere e impostare il contesto:
	
	```
	
	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"
	
	$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	Impostare il contesto dell’insieme di credenziali eseguendo i comandi seguenti:
	
	```
	
	$VaultSettingFilePath = $vaultSetingsFile.FilePath 
	$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
	
	```

## Passaggio 4: installare il provider di Azure Site Recovery

1.	Nel computer VMM, creare una directory eseguendo il comando seguente:
	
	```
	
	pushd C:\ASR\
	
	```
	
2.	Estrarre i file utilizzando il provider scaricato eseguendo il comando seguente:
	
	```
	
	AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3.	Installare il provider utilizzando i comandi seguenti:
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
		$isNotInstalled = $true;
		if(Test-Path $installationRegPath)
		{
			$isNotInstalled = $false;
		}
	}While($isNotInstalled)
	
	```
	
	Attendere la fine dell'installazione.
	
4.	Registrare il server nell'insieme di credenziali utilizzando il comando seguente:
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## Passaggio 5: creare un account di archiviazione di Azure

Se non si dispone di un account di archiviazione di Azure, creare un account con la replica geografica abilitata eseguendo il comando seguente:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Si noti che l'account di archiviazione deve trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione.


## Passaggio 6: installare l'agente di Servizi di ripristino di Azure

Dal portale di Azure, installare l'agente di Servizi di ripristino di Azure in ogni server host Hyper-V presente nei cloud VMM da proteggere.

Eseguire il comando seguente in tutti gli host VMM:

```

marsagentinstaller.exe /q /nu

```


## Passaggio 7: configurare le impostazioni di protezione del cloud

1.	Creare un profilo di protezione cloud in Azure eseguendo il comando seguente:
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	Ottenere un contenitore di protezione eseguendo i comandi seguenti:
	
	```
	
	$PrimaryCloud = "testcloud"
	$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	Avviare l'associazione del contenitore di protezione al cloud:
	
	```
	
	$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	Al termine del processo, eseguire il comando seguente:

	```
	
	$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
	if($job -eq $null -or $job.StateDescription -ne "Completed")
	{
		$isJobLeftForProcessing = $true;
	}
	
	```

5.	Al termine del processo, eseguire il comando seguente:

	```
	Do
	{
		$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
		Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
		if($job -eq $null -or $job.StateDescription -ne "Completed")
		{
			$isJobLeftForProcessing = $true;
		}
		
		if($isJobLeftForProcessing)
		{
			Start-Sleep -Seconds 60
		}
	}While($isJobLeftForProcessing)
		
	```

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor).

## Passaggio 8: configurare il mapping di rete
Prima di iniziare il mapping di rete, verificare che le macchine virtuali nel server VMM di origine siano connesse a una rete VM. Inoltre, creare una o più rete virtuali di Azure. Si noti che è possibile mappare più reti VM a una singola rete di Azure.

Si noti che, se la rete di destinazione ha più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.

Il primo comando ottiene i server per l’insieme di credenziali corrente di Azure Site Recovery. Il comando archivia i server di Microsoft Azure Site Recovery nella variabile di matrice $Servers.

```
$Servers = Get-AzureSiteRecoveryServer

```

Il secondo comando ottiene la rete di ripristino del sito per il primo server nella matrice $Servers. Il comando archivia le reti nella variabile $Networks.

```

$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

Il terzo comando ottiene le sottoscrizioni di Azure tramite il cmdlet Get-AzureSubscription e quindi archivia tale valore nella variabile $Subscriptions.

```

$Subscriptions = Get-AzureSubscription

```

Il quarto comando ottiene le reti virtuali di Azure tramite il cmdlet Get-AzureVNetSite e quindi archivia tale valore nella variabile $AzureVmNetworks.

```

$AzureVmNetworks = Get-AzureVNetSite

```

Il cmdlet finale crea un mapping tra la rete primaria e la rete delle macchine virtuali di Azure. Il cmdlet specifica la rete primaria come primo elemento di $Networks. Il cmdlet specifica una rete di macchine virtuali come primo elemento di $AzureVmNetworks utilizzando il relativo ID. Il comando include l'ID sottoscrizione di Azure.

```

PS C:\> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## Passaggio 9: abilitare la protezione per le macchine virtuali

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud. Tenere presente quanto segue:

Le macchine virtuali devono soddisfare [i prerequisiti delle macchine virtuali di Azure](site-recovery-best-practices.md#virtual-machines).

Per abilitare la protezione, è necessario che le proprietà del sistema operativo e del disco del sistema operativo siano impostate per la macchina virtuale. Quando si crea una macchina virtuale basata su un modello di macchina virtuale VMM è possibile impostare la proprietà. È possibile impostare queste proprietà anche per le macchine virtuali esistenti nelle schede **Generale** e **Configurazione hardware** delle proprietà delle macchine virtuali. Se queste proprietà non vengono impostate in VMM, sarà possibile configurarle nel portale di Azure Site Recovery.


	
1.	Per abilitare la protezione, eseguire il comando seguente per ottenere il contenitore di protezione:
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. Ottenere l'entità di protezione (VM) eseguendo il comando seguente:
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
	```
		
3. Abilitare il ripristino di emergenza per la macchina virtuale eseguendo il comando seguente:

	```
	
	$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity 	-Protection Enable -Force
	
	```
	
## Testare la distribuzione

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino costituito da più macchine virtuali ed eseguire un failover di test per il piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata. Si noti che:

- Per eseguire la connessione alla macchina virtuale in Azure tramite Desktop remoto dopo il failover, abilitare Connessione Desktop remoto sulla macchina virtuale prima di eseguire il failover di test.
- Dopo il failover si userà un indirizzo IP pubblico per connettersi alla macchina virtuale in Azure tramite Desktop remoto. Se si vuole procedere in questo senso, assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale mediante un indirizzo pubblico.

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor).

### Creare un piano di ripristino

1. Creare un file XML come modello per il piano di ripristino utilizzando i dati seguenti e quindi salvarlo come "C:\\RPTemplatePath.xml".
2. Cambiare l'ID nodo RecoveryPlan, il nome, il PrimaryServerId e il SecondaryServerId.
3. Modificare il PrimaryProtectionEntityId del nodo ProtectionEntity (vmid da VMM).
4. È possibile aggiungere ulteriori macchine virtuali mediante l'aggiunta di più nodi ProtectionEntity.
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
	#>
	
	```
	
4. Inserire i dati nel modello:
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
5. Creare il RecoveryPlan:
	
	```
	
	$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	```
	
### Eseguire un failover di test

1.	Ottenere l'oggetto RecoveryPlan eseguendo il comando seguente:
	
	```
	
	$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2.	Avviare il failover di test eseguendo il comando seguente:
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a> Monitorare l'attività

Utilizzare i comandi seguenti per monitorare l'attività. Si noti che è necessario attendere l’esecuzione dei processi per il completamento dell'elaborazione.

```

Do
{
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        	$isJobLeftForProcessing = $true;
        }

	if($isJobLeftForProcessing)
        {
        	Start-Sleep -Seconds 60
        }
}While($isJobLeftForProcessing)

```


## Passaggi successivi

[Altre informazioni](https://msdn.microsoft.com/library/dn850420.aspx) sui cmdlet PowerShell per Azure Site Recovery. </a>.

<!---HONumber=AcomDC_0211_2016-->