---
title: Configurare il timeout di inattività TCP del servizio di bilanciamento del carico in Azure
titleSuffix: Azure Load Balancer
description: In questo articolo viene illustrato come configurare Azure Load Balancer timeout di inattività TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 565707b0e081a495f01f369125584038981b4ae8
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834655"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configurazione del timeout di inattività TCP di Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="tcp-idle-timeout"></a>Timeout di inattività TCP
La configurazione predefinita di Azure Load Balancer prevede che il timeout di inattività sia impostato su 4 minuti. Se un periodo di inattività è più lungo del valore di timeout, non ci sono garanzie che venga mantenuta la sessione TCP o HTTP tra il client e il servizio cloud.

Quando la connessione viene chiusa, l'applicazione client potrebbe ricevere il messaggio di errore seguente: "Connessione sottostante chiusa: una connessione che doveva restare attiva è stata chiusa dal server in modo imprevisto".

Una prassi comune consiste nell'usare una connessione TCP keep-alive per mantenere la connessione attiva per un periodo più lungo. Per altre informazioni, vedere questi [esempi .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Con la connessione keep-alive abilitata, i pacchetti vengono inviati durante i periodi di inattività della connessione. I pacchetti Keep-Alive assicurano che il valore del timeout di inattività non venga raggiunto e che la connessione venga mantenuta per un lungo periodo.

L'impostazione funziona solo per le connessioni in ingresso. Per evitare di perdere la connessione, configurare il keep-alive TCP con un intervallo inferiore rispetto all'impostazione del timeout di inattività o aumentare il valore del timeout di inattività. Per supportare questi scenari, è stato aggiunto il supporto per un timeout di inattività configurabile. È ora possibile impostare questo valore su una durata compresa tra 4 e 30 minuti.

TCP keep-alive funziona per gli scenari in cui la durata della batteria non è un vincolo. Non è consigliabile per le applicazioni per dispositivi mobili. L'uso di un'impostazione keep-alive TCP in un'applicazione per dispositivi mobili può far scaricare più velocemente la batteria del dispositivo.

![Timeout TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Le sezioni seguenti descrivono come modificare le impostazioni del timeout di inattività per le risorse IP pubblico e di bilanciamento del carico.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurazione del timeout TCP per l'IP pubblico a livello di istanza su 15 minuti

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` è facoltativo. Se non impostato, il timeout predefinito è 4 minuti. L'intervallo di timeout accettabile è compreso tra 4 e 30 minuti.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Impostare il timeout TCP su una regola con carico bilanciato su 15 minuti

Per impostare il timeout di inattività per un servizio di bilanciamento del carico, viene impostato ' IdleTimeoutInMinutes ' sulla regola con carico bilanciato. Ad esempio:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Passaggi successivi

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)
