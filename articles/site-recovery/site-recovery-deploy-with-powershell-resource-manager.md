<properties
	pageTitle="Proteggere i server in Azure usando Azure PowerShell con Azure Resource Manager | Microsoft Azure"
	description="Automatizzare la protezione dei server in Azure con Azure Site Recovery usando PowerShell e Azure Resource Manager."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="03/16/2016"
	ms.author="bsiva"/>

# Eseguire la replica tra macchine virtuali Hyper-V locali e Azure con PowerShell e Azure Resource Manager

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portale classico](site-recovery-hyper-v-site-to-azure-classic.md)



## Panoramica

Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. È possibile utilizzare due tipi di moduli: il modulo Azure Profile o il modulo Azure Resource Manager.

I cmdlet di PowerShell per Site Recovery disponibili con Azure PowerShell per Azure Resource Manager consentono di proteggere e ripristinare i server in Azure.

Questo articolo descrive come usare Windows PowerShell con Azure Resource Manage per distribuire Site Recovery per configurare e orchestrare la protezione dei server in Azure. L'esempio usato in questo articolo illustra come proteggere, eseguire il failover e ripristinare in Azure le macchine virtuali di un host Hyper-V usando Azure PowerShell con Azure Resource Manager.

> [AZURE.NOTE] I cmdlet di PowerShell per Site Recovery consentono attualmente di configurare i seguenti elementi: da un sito Virtual Machine Manager a un altro, da un sito Virtual Machine Manager ad Azure e da un sito di Hyper-V ad Azure.

Non è necessario essere un esperto di PowerShell per usare questo articolo, ma si presume che si conoscano i concetti di base, ad esempio moduli, cmdlet e sessioni. Per altre informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Altrei informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] I partner Microsoft che fanno parte del programma Cloud Solution Provider (CSP) possono configurare e gestire la protezione dei server dei clienti nelle rispettive sottoscrizioni CSP dei clienti (sottoscrizioni tenant).

## Prima di iniziare

Assicurarsi che siano rispettati i prerequisiti seguenti:

- Account [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/). Inoltre, è possibile leggere le informazioni sui [prezzi di Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Per informazioni su questa versione e su come installarla, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/).
- Moduli [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). È possibile ottenere le ultime versioni di questi moduli da [PowerShell Gallery](https://www.powershellgallery.com/)

Questo articolo descrive come usare Azure Powershell con Azure Resource Manager per configurare e gestire la protezione dei server. L'esempio usato in questo articolo illustra come proteggere una macchina virtuale, in esecuzione su un host Hyper-V, in Azure. I prerequisiti seguenti sono specifici di questo esempio. Per un set più completo di requisiti per i diversi scenari di Site Recovery, vedere la documentazione relativa a uno scenario specifico.

- Host Hyper-V che esegue Windows Server 2012 R2 contenente una o più macchine virtuali.
- Server Hyper-V connessi a Internet, in modo diretto o tramite proxy.
- Le macchine virtuali da proteggere devono essere conformi ai [prerequisiti per le macchine virtuali](site-recovery-best-practices.md#virtual-machines).


## Passaggio 1: Accedere al proprio account Azure


1. Aprire una console di PowerShell ed eseguire questo comando per accedere all'account di Azure. Il cmdlet visualizza una pagina Web che richiederà le credenziali dell'account.

    	Login-AzureRmAccount

	In alternativa, è anche possibile includere le credenziali dell'account come parametro nel cmdlet `Login-AzureRmAccount` usando il parametro `-Credential`.

	Se si è un partner CSP che opera per conto di un tenant, è necessario specificare il cliente come tenant usando l'ID tenant o il nome di dominio primario del tenant.

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. Un account può disporre di molte sottoscrizioni, pertanto è necessario associare la sottoscrizione da utilizzare all'account.

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Verificare che la sottoscrizione sia registrata per usare i provider di Azure per Servizi di ripristino e Site Recovery usando i comandi seguenti:

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	Nell’output di questi comandi, se **RegistrationState** è impostato su **Registered**, è possibile procedere con il passaggio 2. Se non lo è, è necessario registrare il provider mancante nella sottoscrizione.

	Per registrare il provider di Azure per Site Recovery, eseguire il comando seguente:

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery

	Analogamente, se si usano i cmdlet di Servizi di ripristino per la prima volta nella sottoscrizione, è necessario registrare il provider di Azure per Servizi di ripristino. Prima di eseguire questa operazione, abilitare l'accesso al provider di Servizi di ripristino nella sottoscrizione eseguendo questo comando:

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP] Dopo il completamento di questo comando, l'abilitazione dell'accesso al provider di Servizi di ripristino nella sottoscrizione potrebbe richiedere fino a un'ora. I tentativi eseguiti nel frattempo di registrare il provider di Servizi di ripristino nella sottoscrizione con il comando `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` potrebbero non riuscire. In questo caso, attendere un'ora e riprovare.

	Dopo aver abilitato l'accesso al provider di Servizi di ripristino nella sottoscrizione, registrare il provider nella sottoscrizione eseguendo questo comando:

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	Verificare che i provider siano stati registrati correttamente usando i comandi `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` e `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## Passaggio 2: Configurare l'insieme di credenziali di Servizi di ripristino

1. Creare un gruppo di risorse di Azure Resource Manager in cui creare l'insieme di credenziali o usare un gruppo di risorse esistente. È possibile creare un nuovo gruppo di risorse usando il comando seguente:

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	dove la variabile $ResourceGroupName contiene il nome del gruppo di risorse che si vuole creare e la variabile $Geo contiene l'area di Azure in cui creare il gruppo di risorse (ad esempio, Brasile meridionale).

	È possibile ottenere un elenco di gruppi di risorse nella sottoscrizione usando il cmdlet `Get-AzureRmResourceGroup`.

2. Creare un nuovo insieme di credenziali di Servizi di ripristino di Azure, come segue:

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

	Per recuperare un elenco di insiemi di credenziali esistenti, usare il cmdlet `Get-AzureRmRecoveryServicesVault`.

> [AZURE.NOTE] Per eseguire operazioni sugli insiemi di credenziali di Site Recovery creati usando il portale classico o il modulo di PowerShell di gestione dei servizi di Azure, è possibile recuperare un elenco di tali insiemi di credenziali usando il cmdlet `Get-AzureRmSiteRecoveryVault`. Per tutte le nuove operazioni, occorre creare un nuovo insieme di credenziali di Servizi di ripristino. Gli insiemi di credenziali di Site Recovery creati prima sono comunque supportati, ma non dispongono delle ultime funzionalità.

## Passaggio 3: Impostare il contesto dell’insieme di credenziali di Servizi di ripristino

1.  Impostare il contesto dell’insieme di credenziali eseguendo il comando seguente:

		Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## Passaggio 4: Creare un sito Hyper-V e generare una nuova chiave di registrazione dell'insieme di credenziali per il sito.

1. Creare un nuovo sito Hyper-V, come segue:

		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	Questo cmdlet avvia un processo di Site Recovery per creare il sito e restituisce un oggetto processo di Site Recovery. Attendere il completamento del processo e verificare che sia stato completato correttamente.

	È possibile recuperare l'oggetto processo e quindi controllare lo stato corrente del processo usando il cmdlet Get-AzureRmSiteRecoveryJob.
2. Generare e scaricare una chiave di registrazione per il sito come segue:

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

	Copiare la chiave scaricata nell'host Hyper-V. La chiave è necessaria per registrare l'host Hyper-V nel sito.

## Passaggio 5: Installare il provider di Azure Site Recovery e l'agente Servizi di ripristino di Azure nell'host Hyper-V

1. Scaricare il programma di installazione per l'ultima versione del provider da [Microsoft](https://aka.ms/downloaddra).

2. Eseguire il programma di installazione nell'host Hyper-V e al termine dell'installazione continuare con il passaggio della registrazione.

3. Quando richiesto, fornire la chiave di registrazione del sito scaricata e completare la registrazione dell'host Hyper-V nel sito.

4. Verificare che l'host Hyper-V sia stato registrato nel sito usando il comando seguente:

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## Passaggio 6: Creare un criterio di replica e associarlo al contenitore di protezione

1. Creare un criterio di replica come segue:

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	Controllare il processo restituito per assicurarsi che la creazione del criterio di replica sia riuscita.

	>[AZURE.IMPORTANT] L'account di archiviazione specificato deve essere nella stessa area di Azure dell'insieme di credenziali di Servizi di ripristino e avere la replica geografica abilitata.
	>
	> - Se l'account di archiviazione di ripristino specificato è di tipo Archiviazione di Azure (classico), il failover dei computer protetti ripristina il computer nello IaaS di Azure (classico).
	> - Se l'account di archiviazione di ripristino specificato è di tipo Archiviazione di Azure (Azure Resource Manager), il failover dei computer protetti ripristina il computer nello IaaS di Azure (Azure Resource Manager).

2. Ottenere il contenitore di protezione corrispondente al sito come segue:

		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	Avviare l'associazione del contenitore di protezione al criterio di replica come segue:

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	Attendere il completamento del processo di associazione e assicurarsi che sia riuscito.

##Passaggio 7: Abilitare la protezione per le macchine virtuali

1. Ottenere l'entità di protezione corrispondente alla VM da proteggere come segue:

		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Avviare la protezione della macchina virtuale come segue:

		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT] L'account di archiviazione specificato deve essere nella stessa area di Azure dell'insieme di credenziali di Servizi di ripristino e avere la replica geografica abilitata.
	>
	> - Se l'account di archiviazione di ripristino specificato è di tipo Archiviazione di Azure (classico), il failover dei computer protetti ripristina il computer nello IaaS di Azure (classico).
	> - Se l'account di archiviazione di ripristino specificato è di tipo Archiviazione di Azure (Azure Resource Manager), il failover dei computer protetti ripristina il computer nello IaaS di Azure (Azure Resource Manager).

	> Se alla VM protetta è collegato più di un disco, specificare il disco del sistema operativo usando il parametro *OSDiskName*.

3. Attendere che le macchine virtuali raggiungano uno stato protetto dopo la replica iniziale. L’operazione può richiedere un certo tempo a seconda di fattori quali la quantità di dati da replicare e la larghezza di banda upstream disponibile in Azure. I valori State e StateDescription del processo vengono aggiornati come segue quando la VM raggiunge uno stato protetto.

		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. Aggiornare le proprietà di ripristino, ad esempio la dimensione del ruolo VM e la rete di Azure a cui associare le schede di interfaccia di rete delle macchine virtuali in caso di failover.

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## Passaggio 8: Eseguire un failover di test

1. Eseguire un processo di failover di test come segue:

		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Verificare che la VM di test sia creata in Azure. Il processo di failover di test viene sospeso dopo la creazione della VM di test in Azure. Il processo viene completato pulendo gli elementi creati alla ripresa del processo, come illustrato nel passaggio successivo.

3. Completare il failover di test come descritto di seguito:

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_0518_2016-->