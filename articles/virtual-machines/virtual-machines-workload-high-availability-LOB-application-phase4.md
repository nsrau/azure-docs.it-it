<properties 
	pageTitle="Applicazione line-of-business fase 4 | Microsoft Azure" 
	description="Creare i server Web e caricare l'applicazione line-of-business nella fase 4 dell'applicazione line-of-business di Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="josephd"/>

# Carico di lavoro dell'applicazione line-of-business - Fase 4: Configurare i server Web

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.
 

In questa fase della distribuzione di un'applicazione line-of-business a disponibilità elevata in servizi di infrastruttura di Azure vengono creati i server Web in cui caricare l'applicazione line-of-business.

È necessario completare questa fase prima di passare alla [Fase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md). Per informazioni su tutte le fasi, vedere [Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

## Creare le macchine virtuali del server Web in Azure

Sono due le macchine virtuali del server Web in cui distribuire le applicazioni ASP.NET o applicazioni meno recenti che possono essere ospitate in Internet Information Services (IIS) 8 in Windows Server 2012 R2.

> [AZURE.NOTE]Questo articolo contiene i comandi per Azure Powershell Preview 1.0. Per eseguire questi comandi in Azure PowerShell 0.9.8 e nelle versioni precedenti, sostituire tutte le istanze di "-AzureRM" con "-Azure" e aggiungere il comando **Switch-AzureMode AzureResourceManager** prima di eseguire i comandi. Per altre informazioni, vedere [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/).

Innanzitutto, è necessario configurare il bilanciamento del carico interno in modo che Azure distribuisca in modo uniforme tra i due server Web il traffico dei client destinato all'applicazione line-of-business. A tale scopo è necessario specificare un'istanza del servizio di bilanciamento del carico interno con un nome e un proprio indirizzo IP, assegnato dallo spazio degli indirizzi di subnet assegnato alla rete virtuale di Azure.

Compilare le variabili ed eseguire il set di comandi seguente:

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

A questo punto, aggiungere un record di indirizzo DNS all'infrastruttura DNS interna dell'organizzazione per risolvere il nome di dominio completo dell'applicazione line-of-business (ad esempio lobapp.corp.contoso.com) all'indirizzo IP assegnato all'istanza del servizio di bilanciamento del carico interno (valore di $privIP nel blocco di comandi di Azure PowerShell precedente).

A questo punto, usare il blocco di comandi di PowerShell seguente per creare le macchine virtuali per i due server Web. Si noti che in questo set di comandi di PowerShell vengono usati i valori delle tabelle seguenti:

- Tabella M, per le macchine virtuali
- Tabella V, per le impostazioni di rete virtuale
- Tabella S, per la subnet
- Tabella ST, per gli account di archiviazione
- Tabella A, per i set di disponibilità

È importante ricordare che la Tabella M è stata definita nella [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) e le Tabelle V, S, ST e A sono state definite nella [Fase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Dopo aver specificato tutti i valori appropriati, eseguire il blocco risultante al prompt di Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Poiché queste macchine virtuali sono per un'applicazione intranet, non sono assegnate a un indirizzo IP pubblico o a un'etichetta di nome di dominio DNS ed esposti in Internet. Tuttavia, questo significa anche che non è possibile connettersi a esse dal portale di anteprima di Azure. Il pulsante **Connetti** non è disponibile quando si visualizzano le proprietà della macchina virtuale.

Usare il client desktop remoto preferito e creare una connessione Desktop remoto a ogni macchina virtuale del server Web. Usare il nome computer o il nome DNS della Intranet e le credenziali dell'account amministratore locale.

A questo punto, aggiungere ogni macchina virtuale del server Web al dominio di Active Directory appropriato usando i comandi seguenti al prompt di Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Si noti che è necessario fornire le credenziali dell'account di dominio dopo aver immesso il comando **Add-Computer**.

Dopo il riavvio per riconnettersi alle macchine virtuali usare un account con privilegi di amministratore locale.

A questo punto, installare e configurare IIS per ogni server Web.

1. Eseguire Server Manager, quindi fare clic su **Aggiungi ruoli e funzionalità**.
2. Nella pagina Prima di iniziare, fare clic su **Avanti**.
3. Nella pagina Selezione tipo di installazione fare clic su **Avanti**.
4. Nella pagina Selezione server di destinazione fare clic su **Avanti**.
5. Nella pagina Ruoli server fare clic su **Server Web (IIS)** nell'elenco **Ruoli**.
6. Quando richiesto, fare clic su **Aggiungi funzionalità**, quindi su **Avanti**.
7. Nella pagina Selezione funzionalità fare clic su **Avanti**.
8. Nella pagina Server Web (IIS) fare clic su **Avanti**.
9. Nella pagina Selezione servizi ruolo selezionare o deselezionare le caselle di controllo per i servizi necessari per l'applicazione LOB, quindi fare clic su **Avanti**. 10. Nella pagina Conferma selezioni per l'installazione fare clic su **Installa**.

## Distribuire l'applicazione line-of-business nelle macchine virtuali del server Web

Da un computer della rete locale:

1.	Aggiungere i file per l'applicazione line-of-business ai due server Web.
2.	Creare i database per l'applicazione line-of-business nel cluster di SQL Server.
3.	Testare l'accesso all'applicazione line-of-business e la relativa funzionalità.

Questo diagramma illustra la configurazione ottenuta al termine di questa fase.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## Passaggio successivo

Per continuare con la configurazione di questo carico di lavoro, passare a [Fase 5: Creare il gruppo di disponibilità e aggiungere i database dell'applicazione](virtual-machines-workload-high-availability-LOB-application-phase5.md).

## Risorse aggiuntive

[Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664)

[Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=AcomDC_1125_2015-->