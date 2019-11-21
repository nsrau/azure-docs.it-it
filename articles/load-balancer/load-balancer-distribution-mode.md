---
title: Configurare la modalità di distribuzione di Azure Load Balancer
titleSuffix: Azure Load Balancer
description: In this article, get started configuring the distribution mode for Azure Load Balancer to support source IP affinity.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225369"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurare la modalità di distribuzione per Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modalità di distribuzione basata su hash

The default distribution mode for Azure Load Balancer is a five-tuple hash. 

The tuple is composed of the:
* **Source IP**
* **Source port**
* **Destination IP**
* **Destination port**
* **Protocol type**

The hash is used to map traffic to the available servers. The algorithm provides stickiness only within a transport session. Packets that are in the same session are directed to the same datacenter IP behind the load-balanced endpoint. When the client starts a new session from the same source IP, the source port changes and causes the traffic to go to a different datacenter endpoint.

![Five-tuple hash-based distribution mode](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modalità di affinità IP di origine

The load balancer can also be configured by using the source IP affinity distribution mode. Questa modalità di distribuzione è nota anche come affinità di sessione o affinità IP client. The mode uses a two-tuple (source IP and destination IP) or three-tuple (source IP, destination IP, and protocol type) hash to map traffic to the available servers. By using source IP affinity, connections that are started from the same client computer go to the same datacenter endpoint.

The following figure illustrates a two-tuple configuration. Notice how the two-tuple runs through the load balancer to virtual machine 1 (VM1). Quindi viene eseguito il backup di VM1 in VM2 e VM3.

![Two-tuple session affinity distribution mode](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

La modalità affinità IP di origine risolve un'incompatibilità tra Azure Load Balancer e il Gateway Desktop remoto. Mediante questa modalità è possibile creare una farm di gateway Desktop remoto in un singolo servizio cloud.

Un altro scenario d'uso è il caricamento di contenuti multimediali. Il caricamento dei dati avviene tramite UDP, ma il piano di controllo viene raggiunto tramite TCP:

* A client starts a TCP session to the load-balanced public address and is directed to a specific DIP. Il canale viene lasciato attivo per monitorare lo stato della connessione.
* A new UDP session from the same client computer is started to the same load-balanced public endpoint. La connessione viene indirizzata allo stesso endpoint DIP della connessione TCP precedente. Il caricamento del supporto può essere eseguito con una velocità effettiva elevata mantenendo un canale di controllo tramite TCP.

> [!NOTE]
> Quando il set con carico bilanciato viene modificato (rimozione o aggiunta di una macchina virtuale), la distribuzione delle richieste client viene ricalcolata. Non è possibile dipendere da nuove connessioni da client esistenti che terminano nello stesso server. Inoltre, l'uso della modalità di distribuzione dell'affinità IP di origine può comportare una distribuzione diversa del traffico. I client in esecuzione dietro i proxy possono essere visti come un'applicazione client univoca.

## <a name="configure-source-ip-affinity-settings"></a>Configurare le impostazioni dell'affinità IP di origine

### <a name="azure-portal"></a>Portale di Azure

You can change the configuration of the distribution mode by modifying the load-balancing rule in the portal.

1. Sign in to the Azure portal and locate the Resource Group containing the load balancer you wish to change by clicking on **Resource Groups**.
2. In the load balancer overview screen, click on **Load-balancing rules** under **Settings**.
3. In the load-balancing rules screen, click on the load-balancing rule that you wish to change the distribution mode.
4. Under the rule, the distribution mode is changed by changing the **Session persistence** drop down box.  Sono disponibili le opzioni seguenti:
    
    * **None (hash-based)** - Specifies that successive requests from the same client may be handled by any virtual machine.
    * **Client IP (source IP affinity 2-tuple)** - Specifies that successive requests from the same client IP address will be handled by the same virtual machine.
    * **Client IP and protocol (source IP affinity 3-tuple)** - Specifies that successive requests from the same client IP address and protocol combination will be handled by the same virtual machine.

5. Choose the distribution mode and then click **Save**.

### <a name="azure-powershell"></a>Azure PowerShell

For virtual machines deployed with Resource Manager, use PowerShell to change the load-balancer distribution settings on an existing load-balancing rule. The following command updates the distribution mode: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Per le macchine virtuali classiche, usare Azure PowerShell per modificare le impostazioni di distribuzione. Aggiungere un endpoint di Azure a una macchina virtuale e configurare la modalità di distribuzione del bilanciamento del carico:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Set the value of the `LoadBalancerDistribution` element for the amount of load balancing required. Specify sourceIP for two-tuple (source IP and destination IP) load balancing. Specify sourceIPProtocol for three-tuple (source IP, destination IP, and protocol type) load balancing. Specify none for the default behavior of five-tuple load balancing.

Recuperare una configurazione di modalità di distribuzione del bilanciamento del carico con endpoint usando queste impostazioni:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

When the `LoadBalancerDistribution` element isn't present, Azure Load Balancer uses the default five-tuple algorithm.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurare la modalità di distribuzione su un set di endpoint con carico bilanciato

Quando gli endpoint fanno parte di un set di endpoint con carico bilanciato, è necessario configurare la modalità di distribuzione sul set di endpoint con carico bilanciato:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configurare la modalità di distribuzione per gli endpoint di Servizi cloud

Usare Azure SDK per .NET 2.5 per aggiornare il servizio cloud. Le impostazioni degli endpoint per Servizi cloud vengono effettuate nel file .csdef. Per aggiornare la modalità di distribuzione del bilanciamento del carico per una distribuzione di Servizi cloud, è necessario un aggiornamento della distribuzione.

Di seguito è riportato un esempio di modifiche del file con estensione csdef alle impostazioni degli endpoint:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Esempio di API

L'esempio seguente illustra come riconfigurare la modalità di distribuzione del bilanciamento del carico per un set con carico bilanciato specificato in una distribuzione. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Modificare la modalità di distribuzione per il set con carico bilanciato distribuito

Per modificare una configurazione di distribuzione esistente, usare il modello di distribuzione classico di Azure. Aggiungere l'intestazione `x-ms-version` e impostare il valore alla versione 2014-09-01 o a una versione successiva.

#### <a name="request"></a>Richiesta

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

As previously described, set the `LoadBalancerDistribution` element to sourceIP for two-tuple affinity, sourceIPProtocol for three-tuple affinity, or none for no affinity (five-tuple affinity).

#### <a name="response"></a>Response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del bilanciamento del carico interno di Azure](load-balancer-internal-overview.md)
* [Introduzione alla configurazione del bilanciamento del carico con connessione a Internet](load-balancer-get-started-internet-arm-ps.md)
* [Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
