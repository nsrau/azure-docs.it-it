---
title: Creare un servizio di bilanciamento del carico pubblico con IPv6 - Interfaccia della riga di comando di Azure
titlesuffix: Azure Load Balancer
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico con IPv6 tramite l'interfaccia della riga di comando di Azure.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 1caa8e7554024c3b2e3d86436d3d494d7995169a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142020"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Creare un servizio di bilanciamento del carico pubblico con IPv6 tramite l'interfaccia della riga di comando di Azure


Azure Load Balancer è un servizio di bilanciamento del carico di livello 4 (TCP, UDP). I servizi id bilanciamento del carico offrono disponibilità elevata distribuendo il traffico in ingresso tra istanze del servizio integre in servizi cloud o macchine virtuali in un set di bilanciamento del carico. I servizi di bilanciamento del carico possono anche presentare tali servizi su più porte, più indirizzi IP o entrambi.

## <a name="example-deployment-scenario"></a>Scenario di distribuzione di esempio

Il diagramma seguente illustra la soluzione di bilanciamento del carico distribuita usando il modello di esempio descritto in questo articolo.

![Scenario del bilanciamento del carico](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In questo scenario vengono create le risorse seguenti di Azure:

* Due macchine virtuali (VM)
* Un'interfaccia di rete virtuale per ogni macchina virtuale con assegnati sia indirizzi IPv4 che IPv6
* Un servizio di bilanciamento del carico pubblico con un indirizzo IP pubblico IPv4 e IPv6
* Un set di disponibilità contenente le due macchine virtuali
* Due regole di bilanciamento del carico per eseguire il mapping degli indirizzi VIP pubblici agli endpoint privati

## <a name="deploy-the-solution-by-using-azure-cli"></a>Distribuire la soluzione tramite l'interfaccia della riga di comando di Azure

La procedura seguente illustra come creare un servizio di bilanciamento del carico pubblico usando l'interfaccia della riga di comando di Azure. Con l'interfaccia della riga di comando di Azure ogni oggetto viene creato e configurato singolarmente, quindi gli oggetti vengono riuniti per creare una risorsa.

Per distribuire un servizio di bilanciamento del carico, creare e configurare gli oggetti seguenti:

* **Configurazione di IP front-end**: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso.
* **Pool di indirizzi back-end**: contiene le interfacce di rete (NIC) per le macchine virtuali per la ricezione del traffico di rete dal servizio di bilanciamento del carico.
* **Regole di bilanciamento del carico**: contiene le regole che eseguono il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end.
* **Regole NAT in ingresso**: contiene le regole NAT (Network Address Translation) che eseguono il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
* **Probe**: contiene i probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end.

## <a name="set-up-azure-cli"></a>Configurare l'interfaccia della riga di comando di Azure

In questo esempio gli strumenti dell'interfaccia della riga di comando di Azure vengono eseguiti in una finestra di comando di PowerShell. Per migliorare la leggibilità e il riutilizzo non vengono usati i cmdlet di Azure PowerShell, ma le funzionalità di script di PowerShell.

1. [Installare e configurare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) seguendo la procedura riportata nell'articolo collegato e accedere all'account di Azure.

2. Configurare le variabili di PowerShell per l'uso con i comandi dell'interfaccia della riga di comando di Azure:

    ```powershell
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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Creare un gruppo di risorse, un servizio di bilanciamento del carico, una rete virtuale e subnet

1. Creare un gruppo di risorse:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Creare un servizio di bilanciamento del carico:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Creare una rete virtuale:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. In questa rete virtuale creare due subnet:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Creare indirizzi IP pubblici per il pool front-end

1. Configurare le variabili di PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Creare un indirizzo IP pubblico per il pool IP front-end:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Il servizio di bilanciamento del carico usa l'etichetta di dominio dell'indirizzo IP pubblico come nome di dominio completo. Si tratta di una differenza rispetto alla distribuzione classica, che usa il nome del servizio cloud come nome di dominio completo del servizio di bilanciamento del carico.
    >
    > In questo esempio, il nome di dominio completo è *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Creare pool front-end e back-end

In questa sezione vengono creati i pool IP seguenti:
* Il pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
* Il pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.

1. Configurare le variabili di PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Creare un pool IP front-end e associarlo all'IP pubblico creato nel passaggio precedente e al servizio di bilanciamento del carico.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Creare il probe, le regole NAT e le regole di bilanciamento del carico

Questo esempio crea gli elementi seguenti:

* Una regola probe per verificare la connettività alla porta TCP 80.
* Una regola NAT per la conversione di tutto il traffico in ingresso sulla porta 3389 alla porta 3389 per RDP.\*
* Una regola NAT per la conversione di tutto il traffico in ingresso sulla porta 3391 alla porta 3389 per RDP (Remote Desktop Protocol).\*
* Una regola di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso sulla porta 80 alla porta 80 per gli indirizzi nel pool back-end.

\* Le regole NAT vengono associate a un'istanza di macchina virtuale specifica dietro al servizio di bilanciamento del carico. Il traffico di rete in arrivo sulla porta 3389 viene inviato alla specifica macchina virtuale e alla porta associata alla regola NAT. È necessario specificare un protocollo (UDP o TCP) per una regola NAT. Non è possibile assegnare entrambi i protocolli alla stessa porta.

1. Configurare le variabili di PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Creare il probe.

    L'esempio seguente crea un probe TCP che verifica la connettività alla porta TCP back-end 80 ogni 15 secondi. Dopo due tentativi consecutivi non riusciti, contrassegna la risorsa back-end come non disponibile.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Creare regole NAT in ingresso che consentano connessioni RDP alle risorse back-end:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Creare regole del servizio di bilanciamento del carico che inviano il traffico a porte back-end diverse a seconda del front-end che ha ricevuto la richiesta.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Verificare le impostazioni:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

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

1. Configurare le variabili di PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Creare una scheda di interfaccia di rete per ogni back-end e aggiungere una configurazione di IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Creare le risorse delle macchine virtuali back-end e collegare ogni scheda di interfaccia di rete

Per creare le macchine virtuali è necessario un account di archiviazione. Per il bilanciamento del carico, le macchine virtuali devono essere membri di un set di disponibilità. Per altre informazioni sulla creazione di macchine virtuali, vedere [Creare una macchina virtuale Windows con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Configurare le variabili di PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Questo esempio usa il nome utente e la password per le macchine virtuali in testo non crittografato. Prestare attenzione quando si usano queste credenziali come testo non crittografato. Per un metodo più sicuro per la gestione delle credenziali in PowerShell, vedere il cmdlet [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx).

2. Creare il set di disponibilità:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Creare le macchine virtuali con le schede di interfaccia di rete associate:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md)  
[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)  
[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
