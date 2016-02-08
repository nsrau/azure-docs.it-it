<properties 
   pageTitle="Creare un servizio di bilanciamento del carico Internet in Gestione risorse con PowerShell | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse con PowerShell."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Introduzione su come creare un servizio di bilanciamento del carico per Internet in Gestione risorse con PowerShell.

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet tramite il modello di distribuzione classica](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

La procedura seguente illustra come creare un servizio di bilanciamento del carico Internet usando Gestione risorse di Azure con PowerShell. Con Gestione risorse di Azure gli elementi per creare un servizio di bilanciamento del carico Internet vengono configurati singolarmente e quindi integrati per creare una risorsa.

Qui verrà illustrata la sequenza delle singole attività da eseguire per creare un servizio di bilanciamento del carico e viene illustrato in dettaglio cosa viene fatto per raggiungere l'obiettivo.

## Elementi necessari per creare un servizio di bilanciamento del carico Internet

È necessario creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico.

- Configurazione di IP front-end: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso. 

- Pool di indirizzi back-end: contiene interfacce di rete (NIC) per le macchine virtuali per la ricezione di traffico di rete dal servizio di bilanciamento del carico.

- Regole di bilanciamento del carico: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico alle porte nel pool di indirizzi back-end.

- Regole NAT in ingresso: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.

- Probe: contengono probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end.

È possibile ottenere altre informazioni sui componenti del servizio di bilanciamento del carico con Gestione risorse di Azure in [Supporto di Gestione risorse di Azure per il bilanciamento del carico](load-balancer-arm.md).


## Configurare PowerShell per l'uso di Gestione risorse

Assicurarsi di avere la versione di produzione più recente del modulo di Gestione risorse di Azure per PowerShell.

### Passaggio 1

		PS C:\> Login-AzureRmAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 2

Controllare le sottoscrizioni per l'account

		PS C:\> Get-AzureRmSubscription 

### Passaggio 3 

Scegliere quali sottoscrizioni Azure usare. <BR>

		PS C:\> Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

### Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)


    	PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Creare una rete virtuale e un indirizzo IP pubblico per il pool di indirizzi IP front-end

### Passaggio 1

Creare una subnet e una rete virtuale

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Passaggio 2

Creare una risorsa di indirizzo IP pubblico (PIP) di Azure denominata *PublicIP* che dovrà essere usata da un pool di indirizzi IP front-end con nome DNS *loadbalancernrp.westus.cloudapp.azure.com*. Il comando seguente usa il tipo di allocazione statica.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] Il servizio di bilanciamento del carico userà l'etichetta di dominio dell'indirizzo IP pubblico come prefisso per il nome di dominio completo. Questa è una differenza rispetto al modello di distribuzione classica, che usa il servizio cloud come nome di dominio completo del servizio di bilanciamento del carico. In questo esempio il nome di dominio completo sarà *loadbalancernrp.westus.cloudapp.azure.com*.

## Creare un pool di indirizzi IP front-end e un pool di indirizzi back-end

### Passaggio 1 

Creare un pool di indirizzi IP front-end denominato *LB-Frontend* che usa il PIP *PublicIp*.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Passaggio 2 

Creare un pool di indirizzi back-end denominato *LB-backend*.

	$beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Creare regole di bilanciamento del carico, regole NAT, un probe e un servizio di bilanciamento del carico

Questo esempio crea gli elementi seguenti:

- Regola NAT per la conversione di tutto il traffico in ingresso sulla porta 3441 alla porta 3389
- Regola NAT per la conversione di tutto il traffico in ingresso sulla porta 3442 alla porta 3389.
- Regola del servizio di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso sulla porta 80 verso la porta 80 negli indirizzi nel pool back-end.
- Regola probe per il controllo dello stato di integrità in una pagina denominata *HealthProbe.aspx*.
- Servizio di bilanciamento del carico che usa tutti gli oggetti precedenti.


### Passaggio 1

Creare le regole NAT.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Passaggio 2

Creare una regola del servizio di bilanciamento del carico.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Passaggio 3

Creare un probe di integrità.

	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Passaggio 4

Creare il servizio di bilanciamento del carico usando gli oggetti creati in precedenza.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Creare NIC

È necessario creare interfacce di rete (o modificare quelle esistenti) e associarle a regole NAT, regole del servizio di bilanciamento del carico e probe.

### Passaggio 1 

Ottenere la rete virtuale e la subnet per la rete virtuale in cui devono essere create le schede NIC.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Passaggio 2

Creare una NIC denominata *lb-nic1-be* e associarla alla prima regola NAT, quindi al primo (e unico) pool di indirizzi back-end.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Passaggio 3

Creare una scheda NIC denominata *lb-nic2-be* e associarla alla seconda regola NAT, quindi al primo (e unico) pool di indirizzi back-end.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Passaggio 4

Creare le NIC.


	PS C:\> $backendnic1

Output previsto:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Passaggio 5

Usare il cmdlet `Add-AzureRmVMNetworkInterface` per assegnare le schede NIC a macchine virtuali diverse.

È possibile trovare istruzioni per creare una macchina virtuale e assegnare una NIC in [Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), usando l'opzione 5 nell'esempio.


In alternativa, se è già stata creata una macchina virtuale, è possibile aggiungere l'interfaccia di rete seguendo questa procedura:

#### Passaggio 1 

Se non è ancora stata eseguita questa operazione, caricare la risorsa di bilanciamento del carico in una variabile. La variabile usata viene chiamata $lb e usa gli stessi nomi della risorsa di bilanciamento del carico creata in precedenza.

	$lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

#### Passaggio 2 

Caricare la configurazione back-end in una variabile.

	PS C:\> $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Passaggio 3 

Caricare l'interfaccia di rete già creata in una variabile. Il nome della variabile usata è $nic. Il nome dell'interfaccia di rete usata è lo stesso dell'esempio precedente.

	$nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Passaggio 4

Modificare la configurazione back-end nell'interfaccia di rete.

	PS C:\> $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Passaggio 5 

Salvare l'oggetto interfaccia di rete.

	PS C:\> Set-AzureRmNetworkInterface -NetworkInterface $nic

Dopo che un'interfaccia di rete viene aggiunta al pool di back-end di bilanciamento del carico, inizia a ricevere il traffico di rete in base alle regole di bilanciamento del carico per la risorsa di bilanciamento carico.

## Aggiornare un bilanciamento del carico esistente


### Passaggio 1

Utilizzare il bilanciamento del carico dall'esempio precedente, assegnare l'oggetto bilanciamento del carico alla variabile $slb utilizzando Get-AzureLoadBalancer

	$slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Passaggio 2

Nell'esempio seguente, si aggiungerà una nuova regola NAT in ingresso mediante la porta 81 nel front-end e la porta 8181 per il pool di back-end a un bilanciamento del carico esistente

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP


### Passaggio 3

Salvare la nuova configurazione utilizzando Set AzureLoadBalancer

	$slb | Set-AzureRmLoadBalancer

## Rimuovere un bilanciamento del carico

Usare il comando `Remove-AzureLoadBalancer` per eliminare un bilanciamento del carico creato in precedenza denominato "NRP-LB" in un gruppo di risorse denominato "NRP-RG"

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] È possibile utilizzare l’opzione facoltativa - Forzare per evitare la richiesta di eliminazione.

## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-internal-getstarted.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0128_2016-->