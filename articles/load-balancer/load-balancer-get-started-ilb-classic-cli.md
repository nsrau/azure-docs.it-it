---
title: Creare un servizio di bilanciamento del carico interno - Interfaccia della riga di comando di Azure (versione classica) | Documentazione Microsoft
description: "Informazioni su come creare un servizio di bilanciamento del carico interno usando l’interfaccia della riga di comando di Azure nel modello di distribuzione classica"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f740633230b2479f77d7d09a31dbbf3f72ffb174
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Introduzione alla creazione di un servizio di bilanciamento del carico interno (classico) tramite l’interfaccia di riga di comando di Azure

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Servizi cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Per creare un set con servizio di bilanciamento del carico interno per le macchine virtuali

Per creare un set di bilanciamento del carico interno e i server che gli inviano il traffico, è necessario eseguire queste operazioni:

1. Creare un'istanza della funzionalità di bilanciamento del carico interno che è l'endpoint del traffico in ingresso da configurare con carico bilanciato tra i server di un set con carico bilanciato.
2. Aggiungere gli endpoint corrispondenti alle macchine virtuali che possono ricevere il traffico in ingresso.
3. Configurare i server per inviare il traffico all'indirizzo IP virtuale (indirizzo VIP) dell'istanza del bilanciamento del carico interno.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Procedura dettagliata sulla creazione di un servizio di bilanciamento del carico interno tramite CLI

In questa guida viene illustrato come creare un servizio di bilanciamento del carico interno in base allo scenario precedente.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità classica, come illustrato di seguito.

    ```azurecli
    azure config mode asm
    ```

    Output previsto:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Creazione dell’endpoint e del set del servizio di bilanciamento del carico

Lo scenario presuppone le macchine virtuali "DB1" e "DB2" in un servizio cloud denominato "mytestcloud". Entrambe le macchine virtuali utilizzano una rete virtuale denominata "my testvnet" con subnet "subnet-1".

In questa guida viene creato un set di bilanciamento del carico interno usando la porta 1433 come porta privata e la porta 1433 come porta locale.

Si tratta di uno scenario comune in cui si dispone di macchine virtuali di SQL nel back-end che utilizzano un servizio di bilanciamento del carico interno per garantire che i server di database non siano esposti direttamente tramite un indirizzo IP pubblico.

### <a name="step-1"></a>Passaggio 1

Creare un set di bilanciamento del carico interno utilizzando `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Per ulteriori informazioni, vedere `azure service internal-load-balancer --help` .

È possibile controllare le proprietà del servizio di bilanciamento del carico interno utilizzando il comando `azure service internal-load-balancer list` *nome del servizio cloud*.

Qui di seguito un esempio di output:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Passaggio 2

Configurare il set di bilanciamento del carico interno quando si aggiunge il primo endpoint. È possibile associare l'endpoint, la macchina virtuale e la porta probe al set di bilanciamento del carico interno in questo passaggio.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Passaggio 3

Verificare la configurazione del servizio di bilanciamento del carico utilizzando `azure vm show` *nome macchina virtuale*

```azurecli
azure vm show DB1
```

L'output è il seguente:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Creare un endpoint di desktop remoto per una macchina virtuale

È possibile creare un endpoint di desktop remoto per inoltrare il traffico di rete da una porta pubblica a una porta locale per una macchina virtuale specifica utilizzando `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Rimuovere la macchina virtuale dal servizio di bilanciamento del carico

È possibile rimuovere una macchina virtuale da un set di bilanciamento del carico interno impostato eliminando l'endpoint associato. Una volta rimosso l'endpoint, la macchina virtuale non appartiene più al set del servizio di bilanciamento del carico.

Utilizzando l'esempio precedente, è possibile rimuovere l'endpoint creato per la macchina virtuale "DB1" dal servizio di bilanciamento del carico interno "ilbset" utilizzando il comando `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Per ulteriori informazioni, vedere `azure vm endpoint --help` .

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico utilizzando l’affinità dell’IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

