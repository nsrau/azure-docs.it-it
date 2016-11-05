---
title: Creare un servizio di bilanciamento del carico con connessione Internet con IPv6 in Azure Resource Manager usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni sulla creazione di un servizio di bilanciamento del carico con connessione Internet con IPv6 in Azure Resource Manager usando l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: sewhee

---
# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Creare un servizio di bilanciamento del carico con connessione Internet con IPv6 in Azure Resource Manager usando l'interfaccia della riga di comando di Azure
> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfaccia della riga di comando di Azure](load-balancer-ipv6-internet-cli.md)
> * [Modello](load-balancer-ipv6-internet-template.md)
> 
> 

Azure Load Balancer è un servizio di bilanciamento del carico di livello 4 (TCP, UDP). Il servizio di bilanciamento del carico offre disponibilità elevata distribuendo il traffico in ingresso tra istanze del servizio integre in servizi cloud o macchine virtuali in un set di bilanciamento del carico . Azure Load Balancer può anche presentare tali servizi su più porte, più indirizzi IP o entrambi.

## <a name="example-deployment-scenario"></a>Scenario di distribuzione di esempio
Il diagramma seguente illustra la soluzione di bilanciamento del carico distribuita usando il modello di esempio descritto in questo articolo.

![Scenario del bilanciamento del carico](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In questo scenario si creeranno le seguenti risorse di Azure:

* due macchine virtuali (VM)
* un'interfaccia di rete virtuale per ogni VM con l'assegnazione degli indirizzi IPv4 e IPv6
* un servizio di bilanciamento del carico con connessione Internet con un indirizzo IP pubblico IPv4 e IPv6
* un set di disponibilità contenente le due macchine virtuali
* due regole di bilanciamento del carico per eseguire il mapping degli indirizzi VIP pubblici agli endpoint privati

## <a name="deploying-the-solution-using-the-azure-cli"></a>Distribuzione della soluzione tramite l'interfaccia della riga di comando di Azure
La procedura seguente illustra come creare un servizio di bilanciamento del carico Internet usando Azure Resource Manager con l'interfaccia della riga di comando di Azure. Con Azure Resource Manager ogni risorsa viene creata e configurata singolarmente e quindi integrata per creare una risorsa.

Per distribuire un servizio di bilanciamento del carico è necessario creare e configurare gli oggetti seguenti:

* Configurazione di IP front-end: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso.
* Pool di indirizzi back-end: contiene interfacce di rete (NIC) per le macchine virtuali per la ricezione di traffico di rete dal servizio di bilanciamento del carico.
* Regole di bilanciamento del carico: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico alle porte nel pool di indirizzi back-end.
* Regole NAT in ingresso: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
* Probe: contengono probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end.

Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Configurare l'ambiente dell'interfaccia della riga di comando per l'uso di Azure Resource Manager
Per questo esempio verranno eseguiti gli strumenti dell'interfaccia della riga di comando in una finestra di comando di PowerShell. Non verranno usati i cmdlet di Azure PowerShell, ma le funzionalità di script di PowerShell per migliorare la leggibilità e il riutilizzo.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità Resource Manager.
   
        azure config mode arm
   
    Output previsto:
   
        info:    New mode is arm
3. Accedere ad Azure e ottenere un elenco delle sottoscrizioni.
   
        azure login
   
    Immettere le credenziali di Azure quando richiesto.
   
        azure account list
   
    Selezionare la sottoscrizione da usare. Prendere nota dell'ID sottoscrizione per il passaggio successivo.
4. Impostare le variabili di PowerShell per l'uso con i comandi dell'interfaccia della riga di comando.
   
        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group,-a-load-balancer,-a-virtual-network,-and-subnets"></a>Creare un gruppo di risorse, un servizio di bilanciamento del carico, una rete virtuale e subnet
1. Creare un gruppo di risorse
   
        azure group create $rgName $location
2. Creare un servizio di bilanciamento del carico
   
        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
3. Creare una rete virtuale.
   
        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
   
    Creare due subnet nella rete virtuale.
   
        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Creare indirizzi IP pubblici per il pool front-end
1. Configurare le variabili di PowerShell
   
        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"
2. Creare un indirizzo IP pubblico per il pool IP front-end.
   
        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
   
   > [!IMPORTANT]
   > Il servizio di bilanciamento del carico usa l'etichetta di dominio dell'indirizzo IP pubblico come nome di dominio completo. Si tratta di una differenza rispetto alla distribuzione classica, che usa il nome del servizio cloud come nome di dominio completo del servizio di bilanciamento del carico.
   > In questo esempio, il nome di dominio completo è *contoso09152016.southcentralus.cloudapp.azure.com*.
   > 
   > 

## <a name="create-front-end-and-back-end-pools"></a>Creare pool front-end e back-end
Questo esempio crea il pool di indirizzi IP front-end che riceve il traffico in ingresso per il servizio di bilanciamento del carico e il pool di indirizzi IP back-end a cui il pool front-end invia il traffico di rete con bilanciamento del carico.

1. Configurare le variabili di PowerShell
   
        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"
2. Creare un pool di indirizzi IP front-end che associa l'indirizzo IP pubblico creato nel passaggio precedente e il servizio di bilanciamento del carico.
   
        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe,-nat-rules,-and-lb-rules"></a>Creare il probe, le regole NAT e le regole di bilanciamento del carico
Questo esempio crea gli elementi seguenti:

* una regola probe per verificare la connettività alla porta TCP 80
* una regola NAT per la conversione di tutto il traffico in ingresso nella porta 3389 alla porta 3389 per RDP<sup>1</sup>
* una regola NAT per la conversione di tutto il traffico in ingresso nella porta 3391 alla porta 3389 per RDP<sup>1</sup>
* Regola del servizio di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso sulla porta 80 verso la porta 80 negli indirizzi nel pool back-end.

<sup>1</sup> Le regole NAT vengono associate a un'istanza di macchina virtuale specifica dietro al servizio di bilanciamento del carico. Il traffico di rete in arrivo sulla porta 3389 viene inviato alla specifica macchina virtuale e alla porta associata alla regola NAT. È necessario specificare un protocollo (UDP o TCP) per una regola NAT. Entrambi i protocolli non possono essere assegnati alla stessa porta.

1. Configurare le variabili di PowerShell
   
        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"
2. Creare il probe
   
    L'esempio seguente crea un probe TCP che verifica la connettività alla porta TCP di back-end 80 ogni 15 secondi. Contrassegna la risorsa back-end come non disponibile dopo due tentativi consecutivi non riusciti.
   
        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
3. Creare regole NAT in ingresso che consentano connessioni RDP alle risorse back-end
   
        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
4. Creare regole del servizio di bilanciamento del carico che inviano il traffico a porte back-end diverse a seconda del front-end che ha ricevuto la richiesta
   
        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
5. Verificare le impostazioni
   
        azure network lb show --resource-group $rgName --name $lbName
   
    Output previsto:
   
        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Creare NIC
Creare schede di interfaccia di rete e associarle a regole NAT, regole del servizio di bilanciamento del carico e probe.

1. Configurare le variabili di PowerShell
   
        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
2. Creare una scheda di interfaccia di rete per ogni back-end e aggiungere una configurazione di IPv6.
   
        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name
   
        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Creare le risorse delle macchine virtuali back-end e collegare ogni scheda di interfaccia di rete
Per creare le macchine virtuali è necessario un account di archiviazione. Per il bilanciamento del carico, le macchine virtuali devono essere membri di un set di disponibilità. Per altre informazioni sulla creazione di VM, vedere [Creare una VM di Azure con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. Configurare le variabili di PowerShell
   
        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"
   
   > [!WARNING]
   > Questo esempio usa il nome utente e la password per le macchine virtuali in testo non crittografato. È necessario prestare attenzione quando si usano credenziali non crittografate. Per un metodo più sicuro per la gestione delle credenziali in PowerShell, vedere il cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .
   > 
   > 
2. Creare l'account di archiviazione e il set di disponibilità
   
    È possibile usare un account di archiviazione esistente quando si creano le macchine virtuali. Il comando seguente consente di creare un nuovo account di archiviazione.
   
        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
   
    Creare quindi il set di disponibilità.
   
        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
3. Creare le macchine virtuali con le schede di interfaccia di rete associate
   
        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
   
        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>Passaggi successivi
[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!--HONumber=Oct16_HO2-->


