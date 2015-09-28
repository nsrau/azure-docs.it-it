<properties
   pageTitle="Introduzione alla configurazione del servizio di bilanciamento del carico interno con Gestione risorse di Azure | Microsoft Azure"
   description="Come creare regole di bilanciamento del carico interno, regole NAT e probe per Gestione risorse di Azure. Procedura dettagliata che illustra il processo end-to-end per creare una risorsa di bilanciamento del carico interno."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/22/2015"
   ms.author="joaoma" />

# Introduzione alla configurazione del servizio di bilanciamento del carico interno con Gestione risorse di Azure


> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internal-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-internal-arm-powershell.md)


La procedura seguente illustra come creare un servizio di bilanciamento del carico interno usando Gestione risorse di Azure con PowerShell. Con Gestione risorse di Azure gli elementi per creare un servizio di bilanciamento del carico interno vengono configurati singolarmente e quindi integrati per creare una risorsa.

In questa pagina viene illustrata la sequenza delle singole attività da eseguire per creare un servizio di bilanciamento del carico interno e viene illustrato in dettaglio come fare per raggiungere l'obiettivo di creare un servizio di bilanciamento del carico.


## Elementi necessari per creare un servizio di bilanciamento del carico interno

Gli elementi seguenti devono essere configurati prima di creare un servizio di bilanciamento del carico interno:

- Configurazione di IP front-end: configurazione dell'indirizzo IP privato per il traffico di rete in ingresso. 

- Pool di indirizzi back end: configurazione delle interfacce di rete che riceveranno il traffico con carico bilanciato proveniente dal pool di indirizzi IP front end.

- Regole di bilanciamento del carico: configurazione delle porte di origine e locali per il servizio di bilanciamento del carico.

- Probe: configurazione del probe dello stato di integrità per le istanze di macchina virtuale.

- Regole NAT in ingresso: configurazione delle regole della porta per accedere direttamente a una delle istanze di macchina virtuale.

È possibile ottenere altre informazioni sui componenti del servizio di bilanciamento del carico con Gestione risorse di Azure in [Supporto di Gestione risorse di Azure per il bilanciamento del carico](load-balancer-arm.md).

I passaggi seguenti illustrano come configurare il servizio di bilanciamento del carico in modo che il carico sia bilanciato tra 2 macchine virtuali.


## Procedura dettagliata con PowerShell


### Creare un gruppo di risorse per il servizio di bilanciamento del carico


### Passaggio 1
Verificare di passare alla modalità di PowerShell per usare i cmdlet ARM. Altre informazioni sono disponibili in [Uso di Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Passaggio 2

Accedere all'account Azure.


    PS C:\> Add-AzureAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.


### Passaggio 3

Scegliere le sottoscrizioni ad Azure da utilizzare.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Per visualizzare un elenco di sottoscrizioni disponibili, utilizzare il cmdlet "Get-AzureSubscription".


### Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un servizio di bilanciamento del carico usino lo stesso gruppo di risorse.

Nell'esempio precedente sono stati creare un gruppo di risorse denominato "NRP-RG" e la località "West US".

## Creare la rete virtuale e un indirizzo IP pubblico per il pool di indirizzi IP front-end


### Passaggio 1

Crea una subnet per la rete virtuale e la assegna alla variabile $backendSubnet

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Creare una rete virtuale:

	$vnet= New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crea la rete virtuale e aggiunge la subnet lb-subnet-be alla rete virtuale NRPVNet e la assegna alla variabile $vnet



## Creare il pool di indirizzi IP front-end e il pool di indirizzi back-end

Configurazione di un pool di indirizzi IP front-end per il traffico di rete in ingresso del servizio di bilanciamento del carico e un pool di indirizzi back-end per ricevere il traffico sottoposto a bilanciamento del carico.

### Passaggio 1 

Creare un pool IP front-end usando l'indirizzo IP privato 10.0.2.5 per la subnet 10.0.2.0/24 che sarà l'endpoint del traffico di rete in ingresso.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $backendSubnet.Id

### Passaggio 2 

Configurare un pool di indirizzi back-end usato per ricevere il traffico in ingresso dal pool di indirizzi IP front-end:

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Creare regole di bilanciamento del carico, regole NAT, probe e il servizio di bilanciamento del carico

Dopo aver creato il pool di indirizzi IP front-end e il pool di indirizzi back-end, è necessario creare le regole che faranno parte della risorsa di bilanciamento carico:

### Passaggio 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


L'esempio precedente crea gli elementi seguenti:

- Regola NAT secondo cui tutto il traffico in ingresso sulla porta 3441 passerà alla porta 3389.
- Seconda regola NAT secondo cui tutto il traffico in ingresso sulla porta 3442 passerà alla porta 3389.
- Regola di bilanciamento del carico per bilanciare il carico di tutto il traffico in ingresso sulla porta pubblica 80 alla porta locale 80 nel pool di indirizzi back-end.
- Regola probe per il controllo dello stato di integrità del percorso "HealthProbe.aspx"



### Passaggio 2

Creare il servizio di bilanciamento del carico aggiungendo tutti gli oggetti (regole NAT, regole di bilanciamento del carico, configurazioni di probe):

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Creare interfacce di rete

Dopo aver creato il servizio di bilanciamento del carico interno, è necessario definire quali interfacce di rete riceveranno il traffico di rete con bilanciamento del carico in ingresso, regole NAT e probe. In questo caso, l'interfaccia di rete viene configurata singolarmente e può essere assegnata a una macchina virtuale in un secondo momento.


### Passaggio 1 


Ottenere la rete virtuale di risorse e la subnet per creare interfacce di rete:

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


In questo passaggio viene creata un'interfaccia di rete che farà parte del pool di back-end del servizio di bilanciamento del carico e viene associata la prima regola NAT per RDP per questa interfaccia di rete:
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Passaggio 2

Creare una seconda interfaccia di rete denominata LB-Nic2-BE:

In questo passaggio viene creata una seconda interfaccia di rete, assegnata allo stesso pool di back-end del servizio di bilanciamento carico e viene associata la seconda regola NAT creata per RDP:

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


Il risultato finale sarà simile al seguente:


PS C:\> $backendnic1


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



### Passaggio 3 

Usare il comando Add-AzureVMNetworkInterface per assegnare la scheda di rete a una macchina virtuale.

È possibile trovare la procedura dettagliata per creare una macchina virtuale e assegnare una scheda di rete nella documentazione disponibile in [Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)


## Vedere anche

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=Sept15_HO3-->