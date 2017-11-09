---
title: "Configurare la modalità di distribuzione di Azure Load Balancer |Microsoft Docs"
description: "Come configurare la modalità di Azure Load Balancer per supportare l'affinità IP di origine."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d04a469c04553b7d6a14df7054ad5ef795baa500
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurare la modalità di distribuzione per Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Modalità di distribuzione basata su hash

La modalità di distribuzione predefinita per Azure Load Balancer è un hash a 5 tuple. La tupla è formata da IP di origine, porta di origine, IP di destinazione, porta di destinazione e tipo di protocollo. L'hash viene usato per eseguire il mapping del traffico ai server disponibili e l'algoritmo fornisce persistenza solo all'interno di una sessione di trasporto. I pacchetti che si trovano nella stessa sessione vengono indirizzati alla stessa istanza di IP di data center (DIP) nell'endpoint con carico bilanciato. Quando il client avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia e il traffico quindi viene indirizzato a un endpoint DIP diverso.

![Modalità di distribuzione basata su hash a 5 tuple](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modalità di affinità IP di origine

Load Balancer può anche essere configurato usando la modalità di distribuzione affinità IP di origine. Questa modalità di distribuzione è nota anche come affinità di sessione o affinità IP client. La modalità usa un hash a 2 tuple (IP di origine e IP di destinazione) o a 3 tuple (IP di origine, IP di destinazione e tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. Usando l'affinità IP di origine, le connessioni che vengono avviate dallo stesso computer client passano allo stesso endpoint DIP.

La figura seguente illustra una configurazione a 2 tuple. Si noti che la configurazione a 2 tuple viene eseguita tramite il bilanciamento del carico nella macchina virtuale 1 (VM1). Quindi viene eseguito il backup di VM1 in VM2 e VM3.

![Modalità di distribuzione affinità sessione a 2 tuple](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

La modalità affinità IP di origine risolve un'incompatibilità tra Azure Load Balancer e il Gateway Desktop remoto. Mediante questa modalità è possibile creare una farm di gateway Desktop remoto in un singolo servizio cloud.

Un altro scenario d'uso è il caricamento di contenuti multimediali. Il caricamento dei dati avviene tramite UDP, ma il piano di controllo viene raggiunto tramite TCP:

* Un client avvia una sessione TCP all'indirizzo pubblico con carico bilanciato e viene indirizzato a uno specifico DIP. Il canale viene lasciato attivo per monitorare lo stato della connessione.
* Viene avviata una nuova sessione UDP dallo stesso computer client allo stesso endpoint pubblico con carico bilanciato. La connessione viene indirizzata allo stesso endpoint DIP della connessione TCP precedente. Il caricamento del supporto può essere eseguito con una velocità effettiva elevata mantenendo un canale di controllo tramite TCP.

> [!NOTE]
> Quando il set con carico bilanciato viene modificato (rimozione o aggiunta di una macchina virtuale), la distribuzione delle richieste client viene ricalcolata. Non è possibile dipendere da nuove connessioni da client esistenti che terminano nello stesso server. Inoltre, l'uso della modalità di distribuzione dell'affinità IP di origine può comportare una distribuzione diversa del traffico. I client in esecuzione dietro i proxy possono essere visti come un'applicazione client univoca.

## <a name="configure-source-ip-affinity-settings"></a>Configurare le impostazioni dell'affinità IP di origine

Per le macchine virtuali, usare Azure PowerShell per modificare le impostazioni di timeout. Aggiungere un endpoint di Azure a una macchina virtuale e configurare la modalità di distribuzione del bilanciamento del carico:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Impostare il valore dell'elemento `LoadBalancerDistribution` per la quantità desiderata di bilanciamento del carico. Specificare sourceIP per il bilanciamento del carico a 2 tuple (IP di origine e IP di destinazione). Specificare sourceIPProtocol per il bilanciamento del carico a 3 tuple (IP di origine, IP di destinazione e tipo di protocollo). Specificare none per il comportamento predefinito del bilanciamento del carico a 5 tuple.

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

Quando l'elemento `LoadBalancerDistribution` non è presente, Azure Load Balancer usa l'algoritmo a 5 tuple predefinito.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurare la modalità di distribuzione su un set di endpoint con carico bilanciato

Quando gli endpoint fanno parte di un set di endpoint con carico bilanciato, è necessario configurare la modalità di distribuzione sul set di endpoint con carico bilanciato:

```powershell
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

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

Come descritto in precedenza, impostare l'elemento `LoadBalancerDistribution` su sourceIP per l'affinità a 2 tuple, sourceIPProtocol per l'affinità a 3 tuple o none per nessuna affinità (affinità a 5 tuple).

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
