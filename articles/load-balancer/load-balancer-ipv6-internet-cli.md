---
title: Creare un servizio di bilanciamento del carico pubblico con IPv6 - CLI di Azure | Documenti Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico con IPv6 in Gestione risorse di Azure mediante Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3abd47460999f7b059469a58a59a3e297e88effb
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Creare un servizio di bilanciamento del carico pubblico con IPv6 in Gestione risorse di Azure mediante Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfaccia della riga di comando di Azure](load-balancer-ipv6-internet-cli.md)
> * [Modello](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer è un servizio di bilanciamento del carico di livello 4 (TCP, UDP). Servizi di bilanciamento del carico garantire una disponibilità elevata mediante la distribuzione del traffico in entrata tra le istanze del servizio di integrità in servizi cloud o macchine virtuali in un set di bilanciamento del carico. Servizi di bilanciamento del carico possono inoltre presentare questi servizi su più porte e/o più indirizzi IP.

## <a name="example-deployment-scenario"></a>Scenario di distribuzione di esempio

Il diagramma seguente illustra la soluzione distribuita utilizzando il modello di esempio descritto in questo articolo di bilanciamento del carico.

![Scenario del bilanciamento del carico](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In questo scenario, creare le risorse di Azure seguenti:

* Due macchine virtuali (VM)
* Un'interfaccia di rete virtuale per ogni macchina virtuale con indirizzi IPv4 e IPv6 assegnati
* Un servizio di bilanciamento del carico pubblico con IPv4 e un indirizzo IP pubblico IPv6
* Un set di disponibilità che contiene due macchine virtuali
* Due regole di bilanciamento del carico per eseguire il mapping VIP pubblici per l'endpoint privato di carico

## <a name="deploy-the-solution-by-using-azure-cli"></a>Distribuire la soluzione usando l'interfaccia CLI di Azure

La procedura seguente viene illustrato come creare un servizio di bilanciamento del carico pubblico usando Gestione risorse di Azure con CLI di Azure. Con Gestione risorse di Azure, creare e configurare singolarmente, ogni oggetto e quindi inserire combinarli per creare una risorsa.

Per distribuire un servizio di bilanciamento del carico, creare e configurare gli oggetti seguenti:

* **Configurazione IP front-end**: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso.
* **Pool di indirizzi back-end**: contiene le interfacce di rete (NIC) per le macchine virtuali ricevere il traffico di rete dal bilanciamento del carico.
* **Regole di bilanciamento del carico**: contiene le regole che eseguono il mapping di una porta pubblica su servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end.
* **Regole NAT in ingresso**: contiene regole network address translation (NAT) che eseguono il mapping di una porta pubblica su servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
* **Probe**: contiene i probe di integrità che consentono di controllare la disponibilità delle istanze di macchine virtuali nel pool di indirizzi back-end.

Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](load-balancer-arm.md).

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Configurare l'ambiente CLI di Azure per usare Gestione risorse di Azure

In questo esempio, eseguire gli strumenti di interfaccia CLI di Azure in una finestra di comando di PowerShell. Per migliorare la leggibilità e il riutilizzo, utilizzare le funzionalità di script di PowerShell, non i cmdlet di Azure PowerShell.

1. Se non si è mai usato CLI di Azure, vedere [installare e configurare Azure CLI](../cli-install-nodejs.md) e seguire le istruzioni fino al punto in cui si seleziona l'account di Azure e la sottoscrizione.

2. Per passare alla modalità di gestione delle risorse, eseguire il **modalità di configurazione azure** comando:

    ```azurecli
    azure config mode arm
    ```

    Output previsto:

        info:    New mode is arm

3. Accedere a Azure e ottenere un elenco di sottoscrizioni:

    ```azurecli
    azure login
    ```

4. Al prompt di immettere le credenziali di Azure:

    ```azurecli
    azure account list
    ```

5. Selezionare la sottoscrizione che si desidera utilizzare e prendere nota dell'ID sottoscrizione da utilizzare nel passaggio successivo.

6. Impostare le variabili di PowerShell per l'utilizzo con i comandi CLI di Azure:

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
    azure group create $rgName $location
    ```

2. Creare un servizio di bilanciamento del carico:

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Creare una rete virtuale:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. In questa rete virtuale, creare due subnet:

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Creare indirizzi IP pubblici per il pool front-end

1. Impostare le variabili di PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Creare un indirizzo IP pubblico per il pool IP front-end:

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Il bilanciamento del carico utilizza l'etichetta del dominio dell'indirizzo IP pubblico come il nome di dominio completo (FQDN). Si tratta di una differenza rispetto alla distribuzione classica, che usa il nome del servizio cloud come nome di dominio completo del servizio di bilanciamento del carico.
    >
    > In questo esempio, il nome di dominio completo è *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Creare pool front-end e back-end

In questa sezione è creare i pool IP seguenti:
* Il pool IP front-end che riceve il traffico di rete in ingresso sul bilanciamento del carico.
* Il pool di IP back-end in cui il pool di server front-end invia il traffico di rete con bilanciamento del carico.

1. Impostare le variabili di PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Creare un pool IP front-end e associarlo con l'indirizzo IP pubblico che è stato creato nel passaggio precedente e il bilanciamento del carico.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Creare il probe, le regole NAT e regole di bilanciamento del carico

Questo esempio crea gli elementi seguenti:

* Una regola di probe per verificare la connettività alla porta TCP 80.
* Una regola NAT per convertire tutto il traffico in ingresso sulla porta 3389 alla porta 3389 per RDP.\*
* Una regola NAT per convertire tutto il traffico in ingresso sulla porta 3391 alla porta 3389 per remote desktop protocol (RDP).\*
* Una regola di bilanciamento del carico per bilanciare il traffico in ingresso sulla porta 80 per la porta 80 sugli indirizzi nel pool di back-end.

\*Le regole NAT sono associate a una specifica istanza di macchina virtuale di bilanciamento del carico. Il traffico di rete che arriva nella porta 3389 viene inviato per la macchina virtuale specifica e la porta associata la regola NAT. È necessario specificare un protocollo (UDP o TCP) per una regola NAT. È possibile assegnare a entrambi i protocolli sulla stessa porta.

1. Impostare le variabili di PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Creare il probe.

    L'esempio seguente crea un probe TCP che controlla la connettività alla porta TCP di back-end 80 ogni 15 secondi. Dopo due errori consecutivi, contrassegna la risorsa back-end come non disponibile.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Creare le regole NAT in ingresso che consentono connessioni RDP alle risorse back-end:

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Creare regole di bilanciamento del carico che invia il traffico alle porte di back-end diversi, a seconda di front-end che ha ricevuto la richiesta.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Verificare le impostazioni:

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
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

Creare schede di rete e associarli a regole, regole di bilanciamento carico e probe NAT.

1. Impostare le variabili di PowerShell:

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

2. Creare una scheda di rete per ogni back-end e aggiungere una configurazione IPv6:

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Creare le risorse della macchina virtuale back-end e associare ogni scheda di rete

Per creare le macchine virtuali è necessario un account di archiviazione. Per il bilanciamento del carico, le macchine virtuali devono essere membri di un set di disponibilità. Per ulteriori informazioni sulla creazione di macchine virtuali, vedere [creare una macchina virtuale di Azure usando PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Impostare le variabili di PowerShell:

    ```powershell
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
    ```

    > [!WARNING]
    > In questo esempio viene utilizzato il nome utente e la password per le macchine virtuali in testo non crittografato. Prestare attenzione appropriato quando si utilizzano queste credenziali in testo non crittografato. Per un metodo più sicuro per la gestione delle credenziali in PowerShell, vedere il [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

2. Creare il set di account e la disponibilità di archiviazione.

    Quando si creano le macchine virtuali, è possibile utilizzare un account di archiviazione esistente. Creare un nuovo account di archiviazione usando il comando seguente:

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Creare il set di disponibilità:

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Creare le macchine virtuali con le schede di rete associati:

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md)  
[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)  
[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
