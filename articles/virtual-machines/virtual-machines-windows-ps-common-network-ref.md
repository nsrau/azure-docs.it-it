<properties
	pageTitle="Comandi di rete di PowerShell comuni per le macchine virtuali | Microsoft Azure"
	description="Comandi di PowerShell comuni utili per iniziare a creare una rete virtuale e le risorse associate per le macchine virtuali."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Comandi di rete di Azure PowerShell comuni per le macchine virtuali

Se si vuole creare una macchina virtuale, è necessario creare una [rete virtuale](../virtual-network/virtual-networks-overview.md) o conoscere una rete virtuale esistente a cui è possibile aggiungere la VM. Quando si crea una macchina virtuale, è in genere necessario prendere in considerazione anche la creazione delle risorse illustrate in questo articolo.

## Creare risorse con Azure Powershell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Risorsa | Comando 
-------------- | -------------------------
Subnet | $internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>Una rete tipica può avere una subnet per un [servizio di bilanciamento del carico con connessione Internet](../load-balancer/load-balancer-internet-overview.md) e una subnet separata per un [servizio di bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md). |
Rete virtuale | Creare una rete virtuale:<BR><BR>$rgName = "[nome-gruppo-di-risorse](../powershell-azure-resource-manager.md)"<BR>$locName = "[nome-posizione](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "nome-rete-virtuale"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>Ottenere un elenco di reti virtuali:<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName | Sort Name | Select Name<BR><BR>Elencare le subnet in una rete virtuale:<BR><BR>Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets<BR><BR>Dovrebbe essere visualizzato un comando analogo al seguente che elenca le subnet:<BR><BR>Subnets<BR>-------<BR>{internetSubnet, internalSubnet}<BR><BR>L'indice di subnet per internetSubnet è 0 e l'indice di subnet per internalSubnet è 1.
Etichetta del nome di dominio | $domName = "nome\_dominio"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>È possibile specificare un'etichetta del nome di dominio DNS per una [risorsa IP pubblica](../virtual-network/virtual-network-ip-addresses-overview-arm.md), che crea un mapping per etichettanomedominio.posizione.appcloud.azure.com per l'indirizzo IP pubblico nei server DNS gestiti di Azure. L'etichetta può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere devono essere una lettera o un numero e l'etichetta del nome di dominio deve essere univoca nella rispettiva posizione di Azure. È necessario verificare sempre se l'etichetta del nome di dominio è univoca a livello globale. Se viene restituito **True**, il nome proposto è univoco a livello globale.
Indirizzo IP pubblico | $ipName = "nome-indirizzo-IP-pubblico"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>L'indirizzo IP pubblico usa l'etichetta del nome di dominio creata in precedenza e viene usato dalla configurazione front-end del servizio di bilanciamento del carico.
Configurazione dell'indirizzo IP front-end | $feConfigName = "nome-configurazione-ip-front-end"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>La configurazione front-end include l'indirizzo IP pubblico creato in precedenza per il traffico di rete in ingresso.
Pool di indirizzi back-end | $bePoolName = "nome-pool-back-end"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>Fornisce gli indirizzi interni per il back-end del servizio di bilanciamento del carico a cui si accede tramite un'interfaccia di rete.
Probe | $probeName = "nome-probe-integrità"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contiene i probe di integrità usati per controllare la disponibilità delle istanze delle macchine virtuali nel pool di indirizzi back-end.
Regola di bilanciamento del carico | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contiene regole che eseguono il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end.
Regole NAT in ingresso | $ruleName = "nome-regola-NAT"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contiene le regole che eseguono il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
Bilanciamento del carico | $lbName = "nome-servizio-bilanciamento-carico"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe
Interfaccia di rete | $vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "nome-interfaccia-di-rete"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Creare un'interfaccia di rete usando l'indirizzo IP pubblico e una subnet di rete virtuale creata in precedenza.
	
## Passaggi successivi

- Usare l'interfaccia di rete appena creata quando si [crea una VM](virtual-machines-windows-ps-create.md).
- Informazioni su come [creare una VM con più interfacce di rete](../virtual-network/virtual-networks-multiple-nics.md).

<!---HONumber=AcomDC_0629_2016-->