<properties
	pageTitle="Proteggere le VM da un sito VMM a un altro con PowerShell e Gestione risorse di Microsoft Azure"
	description="Automatizzare la protezione tra due siti VMM locali e Azure tramite PowerShell e Gestione risorse di Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  Da sito VMM a sito VMM con PowerShell e Gestione risorse di Azure


## Panoramica

Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. È possibile utilizzare due tipi di moduli: il modulo Azure Profile o il modulo Gestione risorse di Azure (ARM).

In questo articolo viene descritto come utilizzare Windows PowerShell® con ARM per distribuire Azure Site Recovery per configurare e gestire la protezione delle macchine virtuali tra due siti VMM. Le macchine virtuali in esecuzione nei server host Hyper-V che si trovano in cloud privati VMM in un sito primario eseguiranno la replica e il failover su un sito VMM secondario mediante Hyper-V Replica.

Non è necessario essere un esperto di PowerShell per utilizzare questo articolo, ma si presume che si conoscano i concetti di base, come moduli, cmdlet e sessioni. Per ulteriori informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx). - Ulteriori informazioni su [Utilizzo di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md).

L’articolo include i prerequisiti per lo scenario e mostra come configurare Azure PowerShell per Site Recovery, creare un insieme di credenziali di Site Recovery, installare il provider di Azure Site Recovery su server VMM e registrarli nell’insieme di credenziali, configurare le impostazioni di protezione per i cloud VMM che verranno applicate a tutte le macchine virtuali protette e quindi abilitare la protezione per tali macchine virtuali. Terminare con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.


## Prima di iniziare

Assicurarsi che siano rispettati i prerequisiti seguenti:

- È necessario un account [Microsoft Azure](http://azure.microsoft.com/). È necessario un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/). Inoltre, è possibile leggere le informazioni sui [prezzi di Azure Site Recovery Manager](http://azure.microsoft.com/pricing/details/site-recovery/).
- È necessario un server VMM nei siti primari e secondari in esecuzione in System Center 2012 R2.
- Ogni server VMM deve disporre di almeno un cloud che contenga:
	- Uno o più gruppi host VMM.
	- Uno o più cluster o server host Hyper-V in ogni gruppo host.
	- Una o più macchine virtuali nel server Hyper-V di origine.
	- Per altre informazioni sulla configurazione dei cloud VMM:

		- [Novità del cloud privato con System Center 2012 R2 VMM](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) e [VMM 2012 e i cloud](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
		- [Configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [Creazione di un cloud privato in VMM](https://technet.microsoft.com/library/jj860425.aspx) e [Procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
- Uno o più server Hyper-V host che eseguono almeno Windows Server 2012 con ruolo Hyper-V e con gli ultimi aggiornamenti installati. Il server o il cluster deve essere incluso in un cloud VMM.
- Se si esegue Hyper-V in un cluster, si noti che il gestore del cluster non viene creato automaticamente se viene usato un cluster basato su indirizzi IP statici. Sarà necessario configurare manualmente il broker del cluster. Per istruzioni, vedere [Configurare il Gestore di replica Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

	- Sarà necessario Azure PowerShell. Assicurarsi di eseguire Azure PowerShell versione 0.9.6 o successiva. Leggere [Come installare e configurare Azure PowerShell](powershell-install-configure.md). 
	- L’installazione di Azure PowerShell consente di installare una console personalizzata. È possibile eseguire i comandi di PowerShell da questa console o da qualsiasi altro programma host, ad esempio Windows PowerShell ISE.

## Passaggio 1: Configurare PowerShell


1. Aprire una console di PowerShell ed eseguire questo comando per passare al modulo ARM:

    `Switch-AzureMode AzureResourceManager`

3. A questo punto eseguire questo comando per aggiungere l’account Azure alla sessione di PowerShell. Il cmdlet richiede le credenziali di accesso per il proprio account.

    `Add-AzureAccount`

	Si noti che se si è un partner CSP che opera per conto di un tenant, è necessario specificare il cliente come tenant quando si aggiunge l'account Azure:

    `Add-AzureAccount-Tenant "customer"`

5. Un account può disporre di molte sottoscrizioni, pertanto è necessario associare la sottoscrizione da utilizzare all'account.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. Se si utilizzano i cmdlet di Site Recovery per la prima volta nella sottoscrizione, è necessario registrare il provider di Azure per Site Recovery:

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## Passaggio 2: Configurare l'insieme di credenziali di Site Recovery

1. Se al momento non si dispone di un insieme di credenziali per Site Recovery, è necessario crearne uno eseguendo il cmdlet [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx):

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## Passaggio 3: generare una chiave di registrazione dell'insieme di credenziali

1. Eseguire il cmdlet [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) per ottenere una chiave di registrazione dell’insieme di credenziali. Questa chiave è necessaria per registrare i server VMM nell'insieme di credenziali:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## Passaggio 4: installare il provider di Azure Site Recovery

1. Scaricare il file di installazione del provider dalla pagina Avvio rapido nell'insieme di credenziali di Site Recovery.
2. In ogni server VMM creare una cartella utilizzando questo comando:

    `pushd C:\ASR`

3. Eseguire questo comando per estrarre i file dal provider scaricato:

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. Installare il provider eseguendo:

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. Attendere il termine dell'installazione e quindi registrare il server nell'insieme di credenziali:

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## Passaggio 5: Impostare il contesto dell’insieme di credenziali

1. Eseguire il cmdlet Get-AzureSiteRecoveryVault per assicurarsi che tutti i comandi vengano eseguiti nell'insieme di credenziali specifico:

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. Scaricare le impostazioni dell’insieme di credenziali:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. Per assicurarsi che i cmdlet vengano eseguiti nell'insieme di credenziali, eseguire:

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## Passaggio 3: configurare le impostazioni di protezione del cloud

Dopo aver registrato il server VMM nell'insieme di credenziali, è possibile configurare le impostazioni di protezione del cloud che verranno applicate a tutte le macchine virtuali negli host Hyper-V presenti nei cloud sul server VMM registrato.

1. Creare un contenitore nell'insieme di credenziali per il cloud primario e secondario:

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. Configurare le impostazioni di protezione da applicare ai cloud:

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. Avviare un processo per associare i contenitori di cloud alle impostazioni di protezione del cloud:

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## Passaggio 4: Abilitare la protezione VM

Abilitare la protezione per le VM nei cloud VMM:

1. Ottenere la VM che si desidera proteggere:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. Abilitare la protezione per la VM:

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## Passaggio 5: Eseguire un failover di test

1.	Selezionare la VM di cui si desidera eseguire il failover:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. Eseguire un processo di failover di test:

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. Verificare che la VM del failover sia visualizzata nel sito secondario e completare il failover:

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## Passaggi successivi

Per domande e commenti su questo scenario, visitare il [forum di Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)

<!---HONumber=Oct15_HO3-->