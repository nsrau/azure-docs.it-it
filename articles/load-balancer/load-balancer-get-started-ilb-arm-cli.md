---
title: Creare un servizio di bilanciamento del carico interno - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico interno con l'interfaccia della riga di comando di Resource Manager
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 128b91c685b5f7e494a69ca5b04165a0ee7cbb78
ms.contentlocale: it-it
ms.lasthandoff: 03/21/2017

---

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Creare un servizio di bilanciamento del carico interno tramite l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modello](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra il modello di distribuzione Resource Manager che Microsoft consiglia di usare per le distribuzioni più recenti in sostituzione del [modello di distribuzione classica](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Distribuire la soluzione tramite l'interfaccia della riga di comando di Azure

La procedura seguente illustra come creare un servizio di bilanciamento del carico Internet usando Azure Resource Manager con l'interfaccia della riga di comando di Azure. Con Azure Resource Manager ogni risorsa viene creata e configurata singolarmente e quindi integrata per creare una risorsa.

È necessario creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico.

* **Configurazione di IP front-end**: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso
* **Pool di indirizzi back-end**: contiene interfacce di rete (NIC) per le macchine virtuali per la ricezione di traffico di rete dal servizio di bilanciamento del carico
* **Regole di bilanciamento del carico**: contiene le regole che eseguono il mapping di una porta pubblica sul servizio di bilanciamento del carico alla porta nel pool di indirizzi back-end
* **Regole NAT in ingresso**: contiene le regole che eseguono il mapping di una porta pubblica sul servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end
* **Probe**: contengono probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end

Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurare l'interfaccia della riga di comando per l'uso di Resource Manager

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md). Seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il comando **azure config mode** per passare alla modalità Resource Manager, come illustrato di seguito:

    ```azurecli
    azure config mode arm
    ```

    Output previsto:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Per creare un servizio di bilanciamento del carico interno, attenersi alla procedura dettagliata descritta di seguito

1. Accedere ad Azure.

    ```azurecli
    azure login
    ```

    Quando richiesto, immettere le credenziali di Azure.

2. Attivare la modalità Azure Resource Manager per gli strumenti di comando.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Tutte le risorse in Azure Resource Manager vengono associate a un gruppo di risorse. Se non è ancora stato fatto, creare un gruppo di risorse.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>Creare un set di bilanciamento del carico interno

1. Creare un bilanciamento del carico interno

    Nello scenario seguente, viene creato un gruppo di risorse denominato nrprg viene creato nell'area Stati Uniti orientali.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > Tutte le risorse per un servizio di bilanciamento del carico interno, ad esempio reti virtuali e subnet della rete virtuale, devono essere nello stesso gruppo di risorse e nella stessa area.

2. Creare un indirizzo IP di front-end per il servizio di bilanciamento del carico interno.

    L'indirizzo IP usato deve essere compreso nell'intervallo della subnet della rete virtuale.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. Creare un pool di indirizzi back-end.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    Dopo aver definito un indirizzo IP front-end e un pool di indirizzi back-end, è possibile creare regole del servizio di bilanciamento del carico, regole NAT in ingresso e probe di integrità personalizzati.

4. Creare una regola del servizio di bilanciamento del carico per il servizio di bilanciamento del carico interno.

    Attenendosi alla procedura precedente, il comando crea una regola del servizio di bilanciamento del carico in ascolto sulla porta 1433 nel pool front-end e invia traffico di rete con bilanciamento del carico al pool di indirizzi back-end usando ancora la porta 1433.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. Creare regole NAT in ingresso.

    Le regole NAT in ingresso vengono usate per creare endpoint in un servizio di bilanciamento del carico che viene inviato a un'istanza di macchina virtuale specifica. Nei passaggi precedenti sono state create due regole NAT per il desktop remoto.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. Creare probe di integrità per il servizio di bilanciamento del carico.

    Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano inviare il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > La piattaforma Microsoft Azure usa un indirizzo IPv4 statico e instradabile pubblicamente per un'ampia gamma di scenari di amministrazione. L'indirizzo IP è 168.63.129.16. Questo indirizzo IP non deve essere bloccato da alcun firewall, perché potrebbe causare un comportamento imprevisto.
    > Per quanto riguarda il bilanciamento del carico interno di Azure, questo indirizzo IP viene usato da probe di monitoraggio del servizio di bilanciamento del carico per determinare lo stato di integrità delle macchine virtuali in un set con carico bilanciato. Se si usa un gruppo di sicurezza di rete per limitare il traffico alle macchine virtuali di Azure in un set con carico bilanciato internamente o lo si applica a una subnet di rete virtuale, assicurarsi di aggiungere una regola di sicurezza di rete per consentire il traffico dall'indirizzo 168.63.129.16.

## <a name="create-nics"></a>Creare NIC

È necessario creare schede di interfaccia di rete (NIC) o modificare quelle esistenti e associarle a regole NAT, regole del servizio di bilanciamento del carico e probe.

1. Creare una scheda di interfaccia di rete denominata *lb-nic1-be* e associarla alla regola NAT *rdp1* e al pool di indirizzi back-end *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    Output previsto:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Creare una scheda di interfaccia di rete denominata *lb-nic2-be* e associarla alla regola NAT *rdp2* e al pool di indirizzi back-end *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. Creare una macchina virtuale denominata *DB1* e associarla alla scheda di interfaccia di rete denominata *lb-nic1-be*. Un account di archiviazione denominato *web1nrp* è stato creato prima dell'esecuzione del comando seguente:

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > Le macchine virtuali in un servizio di bilanciamento del carico devono trovarsi nello stesso set di disponibilità. Usare `azure availset create` per creare un set di disponibilità.

4. Creare una macchina virtuale (VM) denominata *DB2* e associarla alla scheda di interfaccia di rete denominata *lb-nic2-be*. Un account di archiviazione denominato *web1nrp* è stato creato prima dell'esecuzione del comando seguente.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>Eliminare un servizio di bilanciamento del carico

Per rimuovere un servizio di bilanciamento del carico, usare il comando seguente:

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico usando l'affinità dell'IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)


