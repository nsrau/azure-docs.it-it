---
title: Creare un servizio di bilanciamento del carico interno per Servizi cloud di Azure | Documentazione Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico interno usando PowerShell nel modello di distribuzione classica
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-service-management
ms.assetid: 57966056-0f46-4f95-a295-483ca1ad135d
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: e32ffa81f7465682579eec92087b98aebbe3c4a8

---

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Introduzione alla creazione di un servizio di bilanciamento del carico interno (classico) per i servizi cloud

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Servizi cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurare il servizio di bilanciamento del carico interno per i servizi cloud

Il servizio di bilanciamento del carico interno è supportato sia per le macchine virtuali che per i servizi cloud. Un endpoint del servizio di bilanciamento del carico interno creato in un servizio cloud esterno a una rete virtuale dell'area sarà accessibile solo nel servizio cloud.

La configurazione del servizio di bilanciamento del carico interno deve essere impostata durante la creazione della prima distribuzione nel servizio cloud, come illustrato nell'esempio seguente.

> [!IMPORTANT]
> Come prerequisito per eseguire i passaggi seguenti, è necessario avere già creato una rete virtuale per la distribuzione cloud. Per creare il bilanciamento del carico interno, saranno necessari il nome della rete virtuale e il nome della subnet.

### <a name="step-1"></a>Passaggio 1

Aprire il file di configurazione del servizio (.cscfg) per la distribuzione cloud in Visual Studio e aggiungere la sezione seguente per creare il bilanciamento del carico interno sotto l'ultimo elemento "`</Role>`" per la configurazione di rete.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Vengono aggiunti i valori per il file di configurazione di rete per mostrare come apparirà. Nell'esempio, si supponga di avere creato una subnet denominata "test_vnet" con una subnet 10.0.0.0/24 denominata test_subnet e un IP statico 10.0.0.4. Il servizio di bilanciamento del carico si chiamerà testLB.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Per altre informazioni sullo schema di bilanciamento del carico, vedere [Aggiungere il servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Passaggio 2

Modificare il file di definizione del servizio (.csdef) per aggiungere endpoint al bilanciamento del carico interno. Non appena viene creata un'istanza del ruolo, il file di definizione del servizio aggiunge le istanze del ruolo al bilanciamento del carico interno.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

Usando gli stessi valori dell'esempio precedente, vengono aggiunti i valori al file di definizione del servizio.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

Il traffico di rete verrà configurato per il bilanciamento del carico tramite il servizio di bilanciamento del carico testLB, usando la porta 80 per le richieste in ingresso e anche per l'invio alle istanze del ruolo di lavoro.

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico utilizzando l’affinità dell’IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Feb17_HO3-->


