---
title: Introduzione alla creazione del servizio di bilanciamento del carico Internet nel modello classico tramite PowerShell | Documentazione Microsoft
description: "Informazioni su come creare un servizio di bilanciamento del carico Internet in modalità classica con PowerShell."
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7344f2c3eeb7d52f8bc60e564d66b2cc51f10f75

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Introduzione alla creazione del servizio di bilanciamento del carico Internet (classico) in PowerShell

[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Configurazione del servizio di bilanciamento del carico con PowerShell

Per impostare il servizio di bilanciamento del carico tramite powershell, seguire questa procedura:

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Dopo avere creato una macchina virtuale, è possibile usare i cmdlet di PowerShell per aggiungere un servizio di bilanciamento del carico a una macchina virtuale all'interno dello stesso servizio cloud.

Nell'esempio seguente si aggiungerà un set di bilanciamento del carico set denominato "webfarm" al servizio cloud "mytestcloud" (o myctestcloud.cloudapp.net), aggiungendo gli endpoint per il bilanciamento del carico alle macchine virtuali denominate "web1" e "web2". Il servizio di bilanciamento del carico riceve il traffico di rete sulla porta 80 e bilancia il carico tra le macchine virtuali definite dall’endpoint locale (in questo caso la porta 80) mediante TCP.

### <a name="step-1"></a>Passaggio 1

Creare un endpoint di carico bilanciato per la prima VM "web1"

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Passaggio 2

Creare un altro endpoint per la seconda VM "web2" usando lo stesso nome del set di bilanciamento del carico interno

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Rimuovere una macchina virtuale dal servizio di bilanciamento del carico

È possibile utilizzare Remove-AzureEndpoint per rimuovere un endpoint della macchina virtuale dal bilanciamento del carico

```powershell
    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile [iniziare a creare un bilanciamento del carico interno](load-balancer-get-started-ilb-classic-ps.md) e configurare il tipo di [modalità di distribuzione](load-balancer-distribution-mode.md) per il comportamento specifico del traffico di rete per il bilanciamento del carico.

Se l'applicazione deve mantenere attive le connessioni per i server dietro il servizio di bilanciamento del carico, è possibile ottenere altre informazioni sulle [impostazioni di timeout delle connessioni TCP inattive per un bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Ciò consente di ottenere informazioni sul comportamento delle connessioni inattive quando si usa il servizio di bilanciamento del carico di Azure.



<!--HONumber=Nov16_HO2-->


