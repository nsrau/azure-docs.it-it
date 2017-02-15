---
title: "Configurare la modalità di distribuzione del bilanciamento del carico |Documentazione Microsoft"
description: "Come configurare la modalità di distribuzione del bilanciamento del carico di Azure per supportare l&quot;affinità IP di origine"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 664e668d6660fae956d4b6bdf0179abf67d743b1
ms.openlocfilehash: 1bc28b6f2979f47284a27a5a25becb8599b2ffab

---

# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurare la modalità di distribuzione del servizio di bilanciamento del carico

## <a name="hash-based-distribution-mode"></a>Modalità di distribuzione basata su hash

L'algoritmo di distribuzione predefinito è un hash a 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione, tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. La persistenza viene mantenuta solo all'interno di una sessione di trasporto. I pacchetti nella stessa sessione verranno indirizzati alla stessa istanza di IP di data center (DIP) nell'endpoint con carico bilanciato. Quando il client avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia e il traffico quindi viene indirizzato a un endpoint DIP diverso.

![bilanciamento del carico basato su hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1. Distribuzione a 5 tuple

## <a name="source-ip-affinity-mode"></a>Modalità di affinità IP di origine

Esiste un'altra modalità di distribuzione definita affinità IP di origine (anche nota come affinità di sessione o affinità del client IP). Il bilanciamento del carico di Azure può essere configurato in modo che usi 2 tuple (IP di origine, IP di destinazione) o 3 tuple (IP di origine, IP di destinazione, protocollo) per eseguire il mapping del traffico ai server disponibili. Usando l'affinità IP di origine, le connessioni avviate dallo stesso computer client passano allo stesso endpoint DIP.

Il diagramma seguente illustra una configurazione a 2 tuple. Si noti come la configurazione a 2 tuple viene eseguita tramite il bilanciamento del carico nella macchina virtuale 1 (VM1) e quindi sottoposta a backup nelle VM2 e VM3.

![affinità di sessione](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figura 2. Distribuzione a 2 tuple

L'affinità IP di origine risolve un'incompatibilità tra il Gateway Desktop remoto e Azure Load Balancer. Ora è possibile creare una farm di gateway Desktop remoto in un singolo servizio cloud.

Un altro scenario d'uso è il caricamento di contenuti multimediali dove il caricamento dei dati avviene tramite UDP, ma il piano di controllo viene raggiunto tramite TCP:

* Prima un client avvia una sessione TCP all'indirizzo pubblico con carico bilanciato e viene diretto a un DIP specifico. Questo canale viene lasciato attivo per monitorare lo stato della connessione
* Viene avviata una nuova sessione UDP dallo stesso computer client allo stesso endpoint pubblico con carico bilanciato. Qui ci si aspetta che anche questa connessione venga indirizzata allo stesso endpoint DIP della precedente connessione TCP in modo che il caricamento di contenuti multimediali possa essere eseguito a una velocità effettiva elevata, mantenendo allo stesso tempo un canale di controllo tramite TCP.

> [!NOTE]
> Quando il set con carico bilanciato viene modificato (rimozione o aggiunta di una macchina virtuale), la distribuzione delle richieste client viene ricalcolata. Non è possibile dipendere da nuove connessioni da client esistenti che terminano nello stesso server. Inoltre, l'uso della modalità di distribuzione dell'affinità IP di origine può comportare una distribuzione diversa del traffico. I client in esecuzione dietro i proxy possono essere visto come una applicazione client univoca.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configurazione delle impostazioni di affinità IP di origine per il bilanciamento del carico

Per le macchine virtuali, è possibile usare PowerShell per modificare le impostazioni di timeout:

Aggiungere un endpoint di Azure a una macchina virtuale e impostare la modalità di distribuzione del servizio di bilanciamento del carico

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

LoadBalancerDistribution può essere impostato su sourceIP per il bilanciamento del carico a 2 tuple (IP di origine, IP di destinazione), su sourceIPProtocol per il bilanciamento del carico a 3 tuple (IP di origine, IP di destinazione, protocollo) o su Nessuno per il comportamento predefinito con bilanciamento del carico a 5 tuple.

Usare il codice seguente per recuperare una configurazione con modalità di distribuzione del bilanciamento del carico con endpoint:

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

Se l'elemento LoadBalancerDistribution non viene specificato, il bilanciamento del carico di Azure utilizza l'algoritmo di 5 tuple predefinito

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Impostare la modalità di distribuzione su un set di endpoint con carico bilanciato

Se gli endpoint fanno parte di un set di endpoint con carico bilanciato, è necessario impostare la modalità di distribuzione sul set di endpoint con carico bilanciato:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configurazione del servizio cloud per modificare la modalità di distribuzione

È possibile usare Azure SDK per .NET 2.5 (in arrivo a novembre) per aggiornare il servizio cloud. Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file con estensione csdef. Per aggiornare la modalità di distribuzione del servizio di bilanciamento del carico per una distribuzione di servizi cloud, è necessario un aggiornamento della distribuzione.
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

È possibile configurare la distribuzione del servizio di bilanciamento del carico tramite l'API di gestione dei servizi. Assicurarsi di aggiungere l'intestazione `x-ms-version` impostata sulla versione `2014-09-01` o successiva.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Aggiornare la configurazione del set con carico bilanciato specificato in una distribuzione

#### <a name="request-example"></a>Esempio di richiesta

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

Il valore di LoadBalancerDistribution può essere sourceIP per l'affinità a 2 tuple, sourceIPProtocol per l'affinità a 3 tuple o Nessuno (per nessuna affinità, ad esempio 5 tuple).

#### <a name="response"></a>Response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->


