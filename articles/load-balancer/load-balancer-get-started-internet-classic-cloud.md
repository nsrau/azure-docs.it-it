---
title: Introduzione alla creazione del servizio di bilanciamento del carico per Internet nel modello di distribuzione classica per i servizi cloud | Documentazione di Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico Internet nel modello di distribuzione classica per i servizi cloud
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-service-management
ms.assetid: 0bb16f96-56a6-429f-88f5-0de2d0136756
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: cf1eafc7bca5bddeb32f1e1e05e660d6877ed805
ms.openlocfilehash: 6a471050a03c8399b0715c331b54636c68fd71cb

---

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Introduzione alla creazione del servizio di bilanciamento del carico Internet per i servizi cloud

> [!div class="op_single_selector"]
> * [Portale di Azure classico](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Servizi cloud di Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, la distribuzione classica e Azure Resource Manager. È importante comprendere i [modelli e strumenti di distribuzione](../azure-classic-rm.md) prima di lavorare con le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md).

I servizi cloud vengono configurati automaticamente con il servizio di bilanciamento del carico e possono essere personalizzati tramite il modello del servizio.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Creare un bilanciamento del carico tramite il file di definizione del servizio

È possibile usare Azure SDK per .NET 2.5 per aggiornare il servizio cloud. Le impostazioni degli endpoint per i servizi cloud vengono configurate nel file di [definizione del servizio](https://msdn.microsoft.com/library/azure/gg557553.aspx), con estensione csdef.

L'esempio seguente mostra la configurazione di un file servicedefinition.csdef per una distribuzione cloud:

Analizzando il frammento di codice per il file con estensione csdef generato da una distribuzione cloud, è possibile vedere l'endpoint esterno configurato per usare le porte HTTP sulle porte 10000, 10001 e 10002.

```xml
<ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
<Endpoints>
    <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
    <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
    <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

    <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

    <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
        <AllocatePublicPortFrom>
            <FixedPortRange min=“10110” max=“10120“  />
        </AllocatePublicPortFrom>
    </InstanceInputEndpoint>
    <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
</Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Controllo dello stato di integrità del servizio di bilanciamento del carico per i servizi cloud

Di seguito è riportato un esempio di probe di integrità:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
</LoadBalancerProbes>
```

Il servizio di bilanciamento del carico combina le informazioni dell'endpoint e le informazioni del probe per creare un URL nel formato `http://{DIP of VM}:80/Probe.aspx`, che verrà usato per eseguire una query sull'integrità del servizio.

Il servizio rileva probe periodiche dallo stesso indirizzo IP. Si tratta della richiesta del probe di integrità proveniente dall'host del nodo in cui è in esecuzione la macchina virtuale. Il servizio deve rispondere con un codice di stato HTTP 200 per indicare l'integrità al bilanciamento del carico. Qualsiasi altro codice di stato HTTP (ad esempio 503) esclude direttamente la macchina virtuale dalla rotazione.

La definizione del probe ne controlla anche la frequenza. Nel caso precedente, il bilanciamento del carico controlla l'endpoint tramite probe ogni 5 secondi. Se non viene ricevuta alcuna risposta positiva per 10 secondi (due intervalli di probe), si presuppone che il probe abbia avuto esito negativo e la macchina virtuale viene esclusa dalla rotazione. Analogamente, se il servizio è escluso dalla rotazione e viene ricevuta una risposta positiva, il servizio viene immediatamente inserito di nuovo nella rotazione. Se il servizio passa continuamente da uno stato integro a uno non integro, il servizio di bilanciamento del carico può decidere di ritardare la reintroduzione del servizio nella rotazione fino a quando non risulta integro per un determinato numero di probe.

Per altre informazioni, fare riferimento allo schema di definizione del servizio per il [probe di integrità](https://msdn.microsoft.com/library/azure/jj151530.aspx) .

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO3-->


