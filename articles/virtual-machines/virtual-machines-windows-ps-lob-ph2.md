<properties 
	pageTitle="Applicazione line-of-business fase 2 | Microsoft Azure" 
	description="Creare e configurare i due controller di dominio di replica di Active Directory nella fase 2 dell'applicazione line-of-business di Azure." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Carico di lavoro dell'applicazione line-of-business - Fase 2: Configurare i controller di dominio

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.
 

In questa fase della distribuzione di un'applicazione line-of-business a disponibilità elevata in servizi di infrastruttura di Azure vengono configurati due controller di dominio di replica nella rete virtuale di Azure in modo che le richieste Web dei client relative alle risorse Web possano essere autenticate nella rete virtuale di Azure invece di usare la connessione VPN o ExpressRoute per inviare il traffico di autenticazione alla rete locale.

È necessario completare questa fase prima di passare alla [Fase 3](virtual-machines-windows-ps-lob-ph3.md). Per informazioni su tutte le fasi, vedere [Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-windows-lob-overview.md).

## Creare macchine virtuali controller di dominio in Azure

È innanzitutto necessario compilare la colonna **Nome macchina virtuale** della tabella M e modificare le dimensioni delle macchine virtuali secondo necessità nella colonna **Dimensione minima**.

Elemento | Nome macchina virtuale | Immagine della raccolta | Dimensione minima 
--- | --- | --- | --- 
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primo controller di dominio, nell'esempio DC1) | Windows Server 2012 R2 Datacenter | Standard\_D2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (secondo controller di dominio, nell'esempio DC2) | Windows Server 2012 R2 Datacenter | Standard\_D2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (server di database primario, nell'esempio SQL1) | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | 	Standard\_DS4
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (server di database secondario, nell'esempio SQL2) | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | 	Standard\_DS4
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (nodo di maggioranza per il cluster, nell'esempio MN1) | Windows Server 2012 R2 Datacenter | Standard\_D1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primo server Web, nell'esempio WEB1) | Windows Server 2012 R2 Datacenter | Standard\_D3
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (secondo server Web, nell'esempio WEB2) | Windows Server 2012 R2 Datacenter | Standard\_D3

**Tabella M - Macchine virtuali per l'applicazione line-of-business a disponibilità elevata in Azure**

Per un elenco completo delle dimensioni delle macchine virtuali, vedere [Dimensioni per le macchine virtuali](virtual-machines-linux-sizes.md).

Utilizzare il seguente blocco di comandi di Azure PowerShell per creare le macchine virtuali per i due controller di dominio. Specificare i valori per le variabili, rimuovendo i caratteri < and >. Si noti che in questo blocco di comandi di PowerShell vengono usati i valori delle tabelle seguenti:

- Tabella M, per le macchine virtuali
- Tabella V, per le impostazioni di rete virtuale
- Tabella S, per la subnet
- Tabella ST, per gli account di archiviazione
- Tabella A, per i set di disponibilità

È importante ricordare che le tabelle V, S, ST e A sono state definite nella [Fase 1: Configurare Azure](virtual-machines-windows-ps-lob-ph1.md).

> [AZURE.NOTE] Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive. Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Dopo aver specificato tutti i valori appropriati, eseguire il blocco risultante al prompt di Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Poiché queste macchine virtuali sono per un'applicazione intranet, non sono assegnate a un indirizzo IP pubblico o a un'etichetta di nome di dominio DNS ed esposti in Internet. Tuttavia, questo significa anche che non è possibile connettersi a esse dal portale di Azure. Il pulsante **Connetti** non è disponibile quando si visualizzano le proprietà della macchina virtuale.

## Configurare il primo controller di dominio

Usare il client desktop remoto preferito e creare una connessione Desktop remoto alla macchina virtuale del primo controller di dominio. Usare il nome computer o il nome DNS della Intranet e le credenziali dell'account amministratore locale.

A questo punto, è necessario aggiungere il disco dati aggiuntivi al primo controller di dominio.

### <a id="datadisk"></a>Per inizializzare un disco vuoto

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto, nel gruppo **Dischi**, fare clic sul disco **2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su OK.
6.	Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7.	Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8.	Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9.	Nella pagina Conferma selezioni, fare clic su **Crea**.
10.	Al termine, fare clic su **Chiudi**.

A questo punto, verificare la connettività del primo controller di dominio ai percorsi di rete dell'organizzazione.

### <a id="testconn"></a>Per testare la connettività

1.	Dal desktop aprire un prompt di Windows PowerShell.
2.	Utilizzare il comando **ping** per eseguire il ping dei nomi e degli indirizzi IP delle risorse nella rete dell'organizzazione.

Questa procedura garantisce il corretto funzionamento della risoluzione dei nomi DNS (ovvero, garantisce che la macchina virtuale sia configurata correttamente con i server DNS locali) e che i pacchetti possano essere inviati da e verso la rete virtuale cross-premise. Se questo test di base non viene superato, contattare il reparto IT per risolvere i problemi di recapito dei pacchetti e di risoluzione dei nomi DNS.

A questo punto, al prompt dei comandi Windows PowerShell nel primo controller di dominio, eseguire i comandi seguenti:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Verrà chiesto di fornire le credenziali di un account amministratore di dominio. Il computer verrà riavviato.

## Configurare il secondo controller di dominio

Usare il client desktop remoto preferito e creare una connessione Desktop remoto alla macchina virtuale del secondo controller di dominio. Usare il nome computer o il nome DNS della Intranet e le credenziali dell'account amministratore locale.

A questo punto, è necessario aggiungere il disco dati aggiuntivi al secondo controller di dominio. Vedere la procedura [Per inizializzare un disco vuoto](#datadisk).

A questo punto, al prompt di Windows PowerShell nel secondo controller di dominio, eseguire i comandi seguenti:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Verrà chiesto di fornire le credenziali di un account amministratore di dominio. Il computer verrà riavviato.

A questo punto, è necessario aggiornare i server DNS per la rete virtuale in modo che Azure assegni alle macchine virtuali gli indirizzi IP dei due nuovi controller di dominio da usare come server DNS. In questa procedura vengono usati i valori della Tabella V (per le impostazioni di rete virtuale) e della Tabella M (per le macchine virtuali).

1.	Nel riquadro sinistro del portale di Azure, fare clic su **Reti virtuali**, quindi fare clic sul nome della rete virtuale (Tabella V - Elemento 1 - Colonna Valore).
2.	Nel riquadro **Impostazioni** fare clic su **Server DNS**.
3.	Nel riquadro **Server DNS** digitare quanto segue:
	- Per **Server DNS primario**: Tabella V - Elemento 6 - Colonna Valore
	- Per **Server DNS secondario **: Tabella V - Elemento 7 - Colonna Valore
4.	Nel riquadro sinistro del portale di Azure, fare clic su **Macchine virtuali**.
5.	Nel riquadro **Macchine virtuali** fare clic sul nome del primo controller di dominio (Tabella M - Elemento 1 - Colonna Nome macchina virtuale).
6.	Nel riquadro per la macchina virtuale fare clic su **Riavvia**.
7.	Dopo aver avviato il primo controller di dominio, fare clic sul nome del secondo controller di dominio nel riquadro **Macchine virtuali** (Tabella M - Elemento 2 - Colonna Nome macchina virtuale).
8.	Nel riquadro per la macchina virtuale fare clic su **Riavvia**. Attendere finché non viene avviato il secondo controller di dominio.

Si noti che sono stati riavviati i due controller di dominio per cui essi non vengono configurati con i server DNS locali come server DNS. Dal momento che sono essi stessi server DNS, sono stati configurati automaticamente con i server DNS locali come server d'inoltro DNS quando sono stati alzati di livello e sono diventati controller di dominio.

A questo punto, è necessario creare un sito di replica di Active Directory per assicurarsi che i server nella rete virtuale di Azure usino i controller di dominio locali. Accedere al controller di dominio primario con un account amministratore di dominio ed eseguire i comandi seguenti da un prompt di Windows PowerShell con privilegi di amministratore:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

A questo punto, aggiungere un account utente per gestire le macchine virtuali di SQL Server.

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

In questo diagramma viene visualizzata la configurazione risultante dal corretto completamento di questa fase, con nomi di computer segnaposto.

![](./media/virtual-machines-windows-ps-lob-ph2/workload-lobapp-phase2.png)

## Passaggio successivo

- Seguire la [Fase 3](virtual-machines-windows-ps-lob-ph3.md) per continuare con la configurazione di questo carico di lavoro.

<!---HONumber=AcomDC_0518_2016-->