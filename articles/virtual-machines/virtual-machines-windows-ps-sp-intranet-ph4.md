<properties
	pageTitle="Farm di SharePoint Server 2013 fase 4 | Microsoft Azure"
	description="Creare le macchine virtuali di SharePoint Server e una nuova farm di SharePoint nella fase 4 della farm di SharePoint Server 2013 in Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Carico di lavoro di Farm intranet di SharePoint Fase 4: Configurare i server SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

In questa fase della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità di SQL Server AlwaysOn in servizi di infrastruttura di Azure, vengono realizzati l'applicazione e i livelli Web della farm di SharePoint e viene creata la farm utilizzando la configurazione guidata di SharePoint.

È necessario completare questa fase prima di passare alla [Fase 5](virtual-machines-windows-ps-sp-intranet-ph5.md). Per conoscere tutte le fasi, vedere [Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-windows-sp-intranet-overview.md).

## Creare le macchine virtuali del server SharePoint in Azure

Esistono quattro macchine virtuali del server SharePoint. Due macchine virtuali di SharePoint Server sono per i server Web di front-end e due per l'amministrazione e l'hosting di applicazioni SharePoint. Due server SharePoint in ogni livello garantiscono un'elevata disponibilità.

Innanzitutto, è necessario configurare il bilanciamento del carico interno in modo che Azure distribuisca il traffico dei client in modo uniforme tra i due server Web front-end. A tale scopo è necessario specificare un'istanza del servizio di bilanciamento del carico interno con un nome e un proprio indirizzo IP, assegnato dallo spazio degli indirizzi di subnet assegnato alla rete virtuale di Azure.

> [AZURE.NOTE] Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive. Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Specificare i valori per le variabili, rimuovendo i caratteri < and >. Nei set di comandi di Azure PowerShell seguenti vengono utilizzati i valori delle tabelle seguenti:

- Tabella M, per le macchine virtuali
- Tabella V, per le impostazioni di rete virtuale
- Tabella S, per la subnet
- Tabella ST, per gli account di archiviazione
- Tabella A, per i set di disponibilità

È importante ricordare che la tabella M è stata definita nella [Fase 2: configurare i controller di dominio](virtual-machines-windows-ps-sp-intranet-ph2.md) mentre le tabelle V, S, ST e A nella [Fase 1: configurare Azure](virtual-machines-windows-ps-sp-intranet-ph1.md).

Una volta specificati tutti i valori appropriati, eseguire il blocco risultante al prompt dei comandi di Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Successivamente, aggiungere un record di indirizzo DNS all'infrastruttura DNS interna dell'organizzazione che risolve il nome di dominio completo della farm di SharePoint (ad esempio spfarm.corp.contoso.com) nell'indirizzo IP assegnato all'istanza del servizio di bilanciamento del carico interno (il valore di $privIP nel blocco di comandi di Azure PowerShell precedente).

Utilizzare il seguente blocco di comandi di Azure PowerShell per creare le macchine virtuali per i quattro server SharePoint. Una volta specificati tutti i valori appropriati, eseguire il blocco risultante al prompt dei comandi di Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Poiché queste macchine virtuali sono per un'applicazione intranet, non sono assegnate a un indirizzo IP pubblico o a un'etichetta di nome di dominio DNS ed esposti in Internet. Tuttavia, questo significa anche che non è possibile connettersi a esse dal portale di Azure. Il pulsante **Connetti** non è disponibile quando si visualizzano le proprietà della macchina virtuale.

Usare il client desktop remoto preferito e creare una connessione Desktop remoto a ogni macchina virtuale. Usare il nome computer o il nome DNS della Intranet e le credenziali dell'account amministratore locale.

A questo punto, aggiungere ogni macchina virtuale al dominio di Active Directory appropriato usando i comandi seguenti al prompt di Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Si noti che è necessario fornire le credenziali dell'account di dominio dopo aver immesso il comando **Add-Computer**.

Dopo il riavvio, utilizzare la [procedura di accesso a una macchina virtuale con una connessione Desktop remoto](virtual-machines-windows-ps-sp-intranet-ph2.md#logon) quattro volte, una volta per ciascun server SharePoint, per accedere utilizzando le credenziali dell'account [Domain]\\sp\_farm\_db. Queste credenziali sono state create nella [Fase 2: configurazione dei controller di dominio](virtual-machines-windows-ps-sp-intranet-ph2.md).

Utilizzare la procedura [Per verificare la connettività](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn) quattro volte, una per ogni server SharePoint, per testare la connettività ai percorsi nella rete dell'organizzazione.

> [AZURE.NOTE] I server di SharePoint vengono creati dall'immagine di traccia di SharePoint Server 2013 versione di prova. È necessario convertire l'installazione per l'utilizzo di una chiave Retail o Volume License per le edizioni Standard o Enterprise di SharePoint Server 2013.

## Configurare la farm di SharePoint

Utilizzare questi passaggi per configurare il primo server SharePoint nella farm:

1.	Dal desktop del primo server applicazioni SharePoint, fare doppio clic su **Configurazione guidata Prodotti SharePoint 2013**. Quando viene richiesto di consentire al programma di apportare modifiche al computer, fare clic su **Sì**.
2.	Nella pagina **Prodotti SharePoint**, fare clic su **Avanti**.
3.	Verrà visualizzata la finestra di dialogo **Configurazione guidata Prodotti SharePoint**, con un avviso indicante che i servizi (ad esempio IIS) verranno riavviati o reimpostati. Fare clic su **Sì**.
4.	Nella pagina **Connessione a una server farm**, selezionare **Crea una nuova server farm**, quindi fare clic su **Avanti**.
5.	Nella pagina **Specifica impostazioni del database di configurazione**:
 - In **Server database**, digitare il nome del server di database primario.
 - In **Nome utente**, digitare [Domain]**\\sp\_farm\_db** (creato nella [Fase 2: configurazione dei controller di dominio](virtual-machines-windows-ps-sp-intranet-ph2.md)). È importante ricordare che l'account sp\_farm\_db dispone dei privilegi sysadmin nel database server.
 - In **Password**, digitare la password dell'account sp\_farm\_db.
6.	Fare clic su **Avanti**.
7.	Nella pagina **Specifica impostazioni di sicurezza della farm**, digitare due volte una passphrase. Registrare la passphrase e archiviarla in un luogo sicuro per riferimento futuro. Fare clic su **Avanti**.
8.	Nella pagina **Configurazione applicazione Web Amministrazione centrale SharePoint**, fare clic su **Avanti**.
9.	Verrà visualizzata la pagina **Completamento Configurazione guidata Prodotti SharePoint**. Fare clic su **Avanti**.
10.	Verrà visualizzata la pagina **Configurazione di Prodotti SharePoint in corso**. Attendere il termine del processo di configurazione, circa 8 minuti.
11.	Una volta configurata la farm, fare clic su **Fine**. Verrà avviato il nuovo sito Web di amministrazione.
12.	Per avviare la configurazione della farm di SharePoint, fare clic su **Avvia la procedura guidata**.

Eseguire la procedura seguente nel secondo server applicazioni di SharePoint e nei due server Web di front-end.

1.	Dal desktop fare doppio clic su **Configurazione guidata Prodotti SharePoint 2013**. Quando viene richiesto di consentire al programma di apportare modifiche al computer, fare clic su **Sì**.
2.	Nella pagina **Prodotti SharePoint**, fare clic su **Avanti**.
3.	Verrà visualizzata la finestra di dialogo **Configurazione guidata Prodotti SharePoint**, con un avviso indicante che i servizi (ad esempio IIS) verranno riavviati o reimpostati. Fare clic su **Sì**.
4.	Nella pagina **Connessione a una server farm**, fare clic su **Connetti a una server farm esistente** e selezionare **Avanti**.
5.	Nella pagina **Imposta server database di configurazione**, digitare il nome del server database primario in **Server database**, quindi fare clic su **Recupera nomi database**.
6.	Fare clic su **SharePoint\_Config** nell'elenco dei nomi di database, quindi fare clic su **Avanti**.
7.	Nella pagina **Specifica impostazioni di sicurezza della farm**, digitare la passphrase della procedura precedente. Fare clic su **Avanti**.
8.	Verrà visualizzata la pagina **Completamento Configurazione guidata Prodotti SharePoint**. Fare clic su **Avanti**.
9.	Nella pagina **Configurazione completata**, fare clic su **Fine**.

Quando in SharePoint viene creata la farm, viene configurato un set di account di accesso server nella macchina virtuale SQL Server primario. SQL Server 2012 introduce il concetto di utenti con password per i database indipendenti. Il database stesso archivia tutti i metadati del database e le informazioni degli utenti e non è necessario che un utente definito in questo database disponga di un account di accesso corrispondente. Le informazioni contenute in questo database vengono replicate dal gruppo di disponibilità e sono disponibili dopo un failover. Per ulteriori informazioni, vedere [Database indipendenti](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Tuttavia, per impostazione predefinita, i database di SharePoint non sono database indipendenti. Pertanto, sarà necessario configurare manualmente il server database secondario in modo che abbia lo stesso set di account di accesso per gli account farm di SharePoint server database primario. Effettuando la connessione a entrambi i server contemporaneamente, è possibile eseguire la sincronizzazione da SQL Server Management Studio.

Al termine di questa configurazione iniziale, sono disponibili ulteriori opzioni di configurazione per le funzionalità della farm di SharePoint. Per altre informazioni, vedere [Pianificazione per SharePoint 2013 nei servizi infrastruttura di Azure](http://msdn.microsoft.com/library/dn275958.aspx).

Questa è la configurazione che risulta dal corretto completamento di questa fase.

![](./media/virtual-machines-windows-ps-sp-intranet-ph4/workload-spsqlao_04.png)

## Passaggio successivo

- Seguire la [Fase 5](virtual-machines-windows-ps-sp-intranet-ph5.md) per continuare con la configurazione di questo carico di lavoro.

<!---HONumber=AcomDC_0323_2016-->