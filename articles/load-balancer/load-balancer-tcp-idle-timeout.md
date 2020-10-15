---
title: Configurare il servizio di bilanciamento del carico TCP reset e timeout di inattività in Azure
titleSuffix: Azure Load Balancer
description: In questo articolo viene illustrato come configurare il timeout di inattività TCP di Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649848"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Configurare il timeout di inattività TCP per Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

Azure Load Balancer ha un'impostazione di timeout di inattività di 4 minuti a 120 minuti. Per impostazione predefinita, questa proprietà è impostata su 4 minuti. Se un periodo di inattività è più lungo del valore di timeout, non ci sono garanzie che venga mantenuta la sessione TCP o HTTP tra il client e il servizio cloud. Altre informazioni sul [timeout di inattività TCP](load-balancer-tcp-reset.md).

Nella sezione seguente è descritto come modificare le impostazioni del timeout di inattività per IP pubblico e risorse di bilanciamento del carico.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Configurare il timeout di inattività TCP per l'indirizzo IP pubblico

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` è facoltativo. Se non impostato, il timeout predefinito è 4 minuti. L'intervallo di timeout accettabile è compreso tra 4 e 120 minuti.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Impostare il timeout di inattività TCP sulle regole

Per impostare il timeout di inattività per un bilanciamento del carico, viene impostato 'IdleTimeoutInMinutes' sulla regola con carico bilanciato. Ad esempio:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](quickstart-load-balancer-standard-public-powershell.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)
