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
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Introduzione su come creare un servizio di bilanciamento del carico per Internet in Gestione risorse con PowerShell.

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. Se si sta cercando il modello di distribuzione classica di Azure, passare a [Introduzione alla creazione del servizio di bilanciamento del carico Internet tramite la distribuzione classica](load-balancer-get-started-internet-classic-portal.md)

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

La procedura seguente illustra come creare un servizio di bilanciamento del carico Internet usando Gestione risorse di Azure con PowerShell. Con Gestione risorse di Azure gli elementi per creare un servizio di bilanciamento del carico Internet vengono configurati singolarmente e quindi integrati per creare una risorsa.

In questa pagina verrà illustrata la sequenza delle singole attività da eseguire per creare un servizio di bilanciamento del carico e viene illustrato in dettaglio cosa viene fatto per raggiungere l'obiettivo di creare un servizio di bilanciamento del carico.

## Elementi necessari per creare un servizio di bilanciamento del carico Internet

È necessario creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico.

- Configurazione di IP front-end: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso. 

- Pool di indirizzi back-end: contiene interfacce di rete (NIC) per la ricezione di traffico dal servizio di bilanciamento del carico.

- Regole di bilanciamento del carico: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico alle porte nelle NIC nel pool di indirizzi back-end.

- Regole NAT in ingresso: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta in una singola NIC nel pool di indirizzi back-end.

- Probe: contengono probe di integrità usati per verificare la disponibilità delle macchine virtuali collegate alle NIC nel pool di indirizzi back-end.

È possibile ottenere altre informazioni sui componenti del servizio di bilanciamento del carico con Gestione risorse di Azure in [Supporto di Gestione risorse di Azure per il bilanciamento del carico](load-balancer-arm.md).


## Configurare PowerShell per l'uso di Gestione risorse
Assicurarsi di disporre della versione di produzione più recente del modulo Azure per PowerShell e di aver configurato correttamente PowerShell per l'accesso alla sottoscrizione di Azure.

### Passaggio 1

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Al prompt di Azure PowerShell eseguire il cmdlet **Switch-AzureMode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		Switch-AzureMode AzureResourceManager
	
	Output previsto:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]Il cmdlet Switch-AzureMode verrà presto dichiarato obsoleto. Di conseguenza, tutti i cmdlet di Gestione risorse verranno rinominati.



### Passaggio 2

Accedere all'account Azure.


    PS C:\> Add-AzureAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.


### Passaggio 3

Scegliere le sottoscrizioni ad Azure da utilizzare.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Per visualizzare un elenco di sottoscrizioni disponibili, utilizzare il cmdlet "Get-AzureSubscription".

## Creare un gruppo di risorse

Creare un nuovo gruppo di risorse denominato *NRP-RG* nella posizione di Azure *Stati Uniti occidentali*.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Creare una rete virtuale e un indirizzo IP pubblico per il pool di indirizzi IP front-end

### Passaggio 1

Creare una subnet e una rete virtuale

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Passaggio 2

Creare un indirizzo IP pubblico (PIP) denominato *PublicIP* che dovrà essere usato da un pool di indirizzi IP front-end con nome DNS *loadbalancernrp.westus.cloudapp.azure.com*. Il comando seguente usa il tipo di allocazione statica.

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]Il servizio di bilanciamento del carico userà l'etichetta di dominio dell'indirizzo IP pubblico come nome di dominio completo. Questa è una differenza rispetto al modello di distribuzione classica, che usa il servizio cloud come nome di dominio completo del servizio di bilanciamento del carico. In questo esempio il nome di dominio completo sarà *loadbalancernrp.westus.cloudapp.azure.com*.

## Creare un pool di indirizzi IP front-end e un pool di indirizzi back-end

### Passaggio 1 

Creare un pool IP front-end denominato *LB-Frontend* che usa l'IP pubblico *PublicIp*.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Passaggio 2 

Creare un pool di indirizzi back-end denominato *LB-backend*.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Creare regole di bilanciamento del carico, regole NAT, un probe e un servizio di bilanciamento del carico

Questo esempio crea gli elementi seguenti:

- Regola NAT per la conversione di tutto il traffico in ingresso sulla porta 3441 alla porta 3389<sup>1</sup>.
- Regola NAT per la conversione di tutto il traffico in ingresso sulla porta 3442 alla porta 3389.
- Regola del servizio di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso sulla porta 80 verso la porta 80 negli indirizzi nel pool back-end.
- Regola probe per il controllo dello stato di integrità in una pagina denominata *HealthProbe.aspx*.
- Servizio di bilanciamento del carico che usa tutti gli oggetti precedenti.


<sup>1</sup> le regole NAT vengono associate a un’istanza di macchina virtuale specifica dietro al servizio di bilanciamento del carico. Il traffico di rete in ingresso sulla porta 3341 sarà inviato ad una macchina virtuale specifica sulla porta 3389 associata alla regola NAT nell’esempio seguente. E’ necessario scegliere un protocollo per la regola NAT, UDP o TCP. Entrambi i protocolli non possono essere assegnati alla stessa porta.

### Passaggio 1

Creare le regole NAT.

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Passaggio 2

Creare una regola del servizio di bilanciamento del carico.

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Passaggio 3

Creare un probe di integrità.

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Passaggio 4

Creare il servizio di bilanciamento del carico usando gli oggetti creati in precedenza.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Creare NIC

È necessario creare schede di interfaccia di rete (NIC) o modificare quelle esistenti e associarle a regole NAT, regole del servizio di bilanciamento del carico e probe.

### Passaggio 1 

Recuperare la rete virtuale e la subnet in cui devono essere create le NIC.

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Passaggio 2

Creare una NIC denominata *lb-nic1-be* e associarla alla prima regola NAT, quindi al primo (e unico) pool di indirizzi back-end.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Passaggio 3

Creare una NIC denominata *lb-nic2-be* e associarla alla seconda regola NAT, quindi al primo (e unico) pool di indirizzi back-end.

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

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

Usare il cmdlet `Add-AzureVMNetworkInterface` per assegnare le schede NIC a macchine virtuali diverse.

È possibile trovare istruzioni per creare una macchina virtuale e assegnare una NIC in [Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), usando l'opzione 5 nell'esempio.

## Aggiornare un bilanciamento del carico esistente


### Passaggio 1

Utilizzare il bilanciamento del carico dall'esempio precedente, assegnare l'oggetto bilanciamento del carico alla variabile $slb utilizzando Get-AzureLoadBalancer

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Passaggio 2

Nell'esempio seguente, si aggiungerà una nuova regola NAT in ingresso mediante la porta 81 nel front-end e la porta 8181 per il pool di back-end a un bilanciamento del carico esistente

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Passaggio 3

Salvare la nuova configurazione utilizzando Set AzureLoadBalancer

	$slb | Set-AzureLoadBalancer

## Rimuovere un bilanciamento del carico

Utilizzare il comando Remove-AzureLoadBalancer per eliminare un bilanciamento del carico creato in precedenza denominato "NRP-LB" in un gruppo di risorse denominato "NRP-RG"

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]È possibile utilizzare l’opzione facoltativa - Forzare per evitare la richiesta di eliminazione.

## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-internal-getstarted.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->