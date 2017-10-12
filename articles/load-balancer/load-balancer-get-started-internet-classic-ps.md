---
title: Creare un servizio di bilanciamento del carico con connessione Internet - Azure PowerShell versione classica | Documentazione Microsoft
description: "Informazioni su come creare un servizio di bilanciamento del carico Internet in modalità classica con PowerShell."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 66afcf703a5b6270569f36ca1663cd778ed6f495
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Introduzione alla creazione del servizio di bilanciamento del carico Internet (classico) in PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure classico](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Servizi cloud di Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, la distribuzione classica e Azure Resource Manager. È importante comprendere i [modelli e strumenti di distribuzione](../azure-classic-rm.md) prima di lavorare con le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Configurazione del servizio di bilanciamento del carico con PowerShell

Per impostare il servizio di bilanciamento del carico usando PowerShell, completare la procedura seguente:

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Dopo avere creato una macchina virtuale, è possibile usare i cmdlet di PowerShell per aggiungere un servizio di bilanciamento del carico a una macchina virtuale all'interno dello stesso servizio cloud.

Nell'esempio seguente si aggiunge un set di bilanciamento del carico denominato "webfarm" al servizio cloud "mytestcloud" (o myctestcloud.cloudapp.net), aggiungendo gli endpoint per il bilanciamento del carico alle macchine virtuali denominate "web1" e "web2". Il servizio di bilanciamento del carico riceve il traffico di rete sulla porta 80 e bilancia il carico tra le macchine virtuali definite dall’endpoint locale (in questo caso la porta 80) mediante TCP.

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
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile [iniziare a creare un bilanciamento del carico interno](load-balancer-get-started-ilb-classic-ps.md) e configurare il tipo di [modalità di distribuzione](load-balancer-distribution-mode.md) per il comportamento del traffico di rete per un servizio di bilanciamento del carico specifico.

Se l'applicazione deve mantenere attive le connessioni per i server dietro il servizio di bilanciamento del carico, è possibile ottenere altre informazioni sulle [impostazioni di timeout delle connessioni TCP inattive per un bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Ciò consente di ottenere informazioni sul comportamento delle connessioni inattive quando si usa Azure Load Balancer.
