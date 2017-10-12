---
title: Configurare il bilanciamento del carico per SQL AlwaysOn | Documentazione Microsoft
description: Configurare il bilanciamento del carico per usare sempre SQL AlwaysOn e sfruttare PowerShell per creare il bilanciamento del carico per l'implementazione SQL
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
ms.openlocfilehash: 3ebbf1c4009d89b1f18b2ff8ff5dd243c456dff8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-load-balancer-for-sql-always-on"></a>Configurare il bilanciamento del carico per SQL AlwaysOn

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

I gruppi di disponibilità AlwaysOn di SQL Server ora possono essere eseguiti con ILB. Un gruppo di disponibilità è la soluzione principale di SQL Server per il ripristino di emergenza e la disponibilità elevata. Il listener del gruppo di disponibilità consente alle applicazioni client di connettersi facilmente alla replica primaria, indipendentemente dal numero di repliche nella configurazione.

Il nome del listener (DNS) viene associato a un indirizzo IP con carico bilanciato e il bilanciamento del carico di Azure indirizza il traffico in ingresso solo al server primario nel set di repliche.

È possibile usare il supporto ILB per gli endpoint di SQL Server AlwaysOn (listener). Ora si ha il controllo dell'accessibilità del listener e si può scegliere l'indirizzo IP con carico bilanciato da una subnet specifica nella rete virtuale.

Usando ILB sul listener, l'endpoint SQL Server (ad esempio, Server=tcp:ListenerName,1433;Database=DatabaseName) è accessibile solo per:

* Servizi e VM nella stessa rete virtuale
* Servizi e VM dalla rete locale connessa
* Servizi e VM da reti virtuali interconnesse

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 - SQL AlwaysOn configurato con bilanciamento del carico con connessione Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Aggiungere al servizio il bilanciamento del carico interno

1. Nell'esempio seguente verrà configurata una rete virtuale che contiene una subnet denominata "Subnet-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Aggiungere gli endpoint con carico bilanciato per ILB in ogni macchina virtuale

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    Nell'esempio precedente, si dispone di 2 macchine virtuali denominate "sqlsvc1" e "sqlsvc2" in esecuzione nel servizio cloud "Sqlsvc". Dopo la creazione di ILB con l'opzione `DirectServerReturn` vengono aggiunti gli endpoint con carico bilanciato all’ILB per consentire a SQL di configurare i listener per i gruppi di disponibilità.

Per altre informazioni su SQL AlwaysOn, vedere [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Vedere anche
[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
