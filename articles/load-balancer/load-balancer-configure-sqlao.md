---
title: Configurare Load Balancer per SQL Server Always On | Microsoft Docs
description: Configurare Load Balancer per usare sempre SQL Server Always On e sfruttare PowerShell per creare il bilanciamento del carico per l'implementazione SQL Server
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Configurare Load Balancer per SQL Server Always On

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

I gruppi di disponibilità Always On di SQL Server possono ora eseguire un servizio di bilanciamento del carico interno. Un gruppo di disponibilità è la soluzione principale di SQL Server per disponibilità elevata e ripristino di emergenza. Il listener del gruppo di disponibilità consente alle applicazioni client di connettersi facilmente alla replica primaria, indipendentemente dal numero di repliche nella configurazione.

Viene eseguito il mapping del nome del listener (DNS) a un indirizzo IP con bilanciamento del carico. Azure Load Balancer indirizza il traffico in ingresso al server primario nel set di repliche.

È possibile usare il supporto ILB per gli endpoint di SQL Server Always On (listener). Adesso si ha il controllo sull'accessibilità del listener. È possibile scegliere l'indirizzo IP con bilanciamento del carico da una subnet specifica nella rete virtuale.

Usando un ILB sul listener, l'endpoint SQL Server (ad esempio, Server=tcp:ListenerName,1433;Database=DatabaseName) è accessibile solo per:

* Servizi e VM nella stessa rete virtuale.
* Servizi e VM dalle reti locali connesse.
* Servizi e VM dalle reti virtuali interconnesse.

![Servizio di bilanciamento del carico interno per SQL Server Always On](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Aggiungere un servizio di bilanciamento del carico interno

1. Nell'esempio seguente verrà configurata una rete virtuale contenente una subnet denominata "Subnet-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Aggiungere degli endpoint con bilanciamento del carico per un servizio di bilanciamento del carico interno su ogni macchina virtuale.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    Nell'esempio precedente, si dispone di due macchine virtuali denominate "sqlsvc1" e "sqlsvc2" eseguite nel servizio cloud "Sqlsvc". Dopo aver creato il servizio di bilanciamento del carico interno con lo switch `DirectServerReturn`, aggiungere gli endpoint con bilanciamento del carico al servizio di bilanciamento del carico interno. Gli endpoint con bilanciamento del carico consentono a SQL Server di configurare i listener per i gruppi di disponibilità.

Per altre informazioni su SQL Server AlwaysOn, vedere [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Vedere anche 
* [Introduzione alla configurazione del bilanciamento del carico pubblico](load-balancer-get-started-internet-arm-ps.md)
* [Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)
* [Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
