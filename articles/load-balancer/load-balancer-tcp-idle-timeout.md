---
title: Configurare il timeout di inattività e il ripristino TCP del servizio di bilanciamento del carico
titleSuffix: Azure Load Balancer
description: In questo articolo viene illustrato come configurare Azure Load Balancer il timeout di inattività TCP e la reimpostazione.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747192"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Configurare il timeout di inattività e la reimpostazione TCP per Azure Load Balancer

Azure Load Balancer presenta l'intervallo di timeout di inattività seguente:

* da 4 minuti a 100 minuti per le regole in uscita
* da 4 minuti a 30 minuti per Load Balancer le regole e le regole NAT in ingresso

Per impostazione predefinita, è impostato su 4 minuti. Se un periodo di inattività è più lungo del valore di timeout, non vi è alcuna garanzia che la sessione TCP o HTTP venga mantenuta tra il client e il servizio. 

Le sezioni seguenti descrivono come modificare il timeout di inattività e le impostazioni di reimpostazione TCP per le risorse di bilanciamento del carico.

## <a name="set-tcp-reset-and-idle-timeout"></a>Imposta il timeout di inattività e la reimpostazione TCP
---
# <a name="portal"></a>[**Portale**](#tab/tcp-reset-idle-portal)

Per impostare il timeout di inattività e la reimpostazione TCP per un servizio di bilanciamento del carico, modificare la regola con carico bilanciato. 

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra selezionare **gruppi di risorse** .

3. Selezionare il gruppo di risorse per il servizio di bilanciamento del carico. In questo esempio il gruppo di risorse è denominato **myResourceGroup** .

4. Selezionare il servizio di bilanciamento del carico in uso. In questo esempio, il servizio di bilanciamento del carico è denominato **myLoadBalancer** .

5. In **Impostazioni** selezionare **regole di bilanciamento del carico** .

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Modificare le regole del servizio di bilanciamento del carico." border="true":::

6. Selezionare la regola di bilanciamento del carico. In questo esempio, la regola di bilanciamento del carico è denominata **myLBrule** .

7. Nella regola di bilanciamento del carico spostare il dispositivo di scorrimento in **timeout di inattività (minuti)** sul valore di timeout.  

8. In **TCP Reset** selezionare **Enabled** .

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Modificare le regole del servizio di bilanciamento del carico." border="true":::

9. Selezionare **Salva** .

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Per impostare il timeout di inattività e la reimpostazione TCP, impostare i valori nei parametri seguenti della regola di bilanciamento del carico con [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

Sostituire gli esempi seguenti con i valori delle risorse:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Interfaccia della riga di comando di Azure**](#tab/tcp-reset-idle-cli)

Per impostare il timeout di inattività e la reimpostazione TCP, usare i parametri seguenti per [AZ Network lb Rule Update](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--Idle-timeout**
* **--Enable-TCP-Reset**

Prima di iniziare, convalidare l'ambiente:

* Accedere al portale di Azure e verificare che la sottoscrizione sia attiva eseguendo `az login`.
* Controllare la versione dell'interfaccia della riga di comando di Azure in una finestra terminale o di comando eseguendo `az --version`. Per la versione più recente, vedere le [ultime note sulla versione](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se non si dispone della versione più recente, aggiornare l'installazione seguendo la [guida all'installazione per il sistema operativo o la piattaforma in uso](/cli/azure/install-azure-cli).

Sostituire gli esempi seguenti con i valori delle risorse:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul timeout di inattività TCP e sulla reimpostazione, vedere [Load Balancer la reimpostazione TCP e il timeout di inattività](load-balancer-tcp-reset.md)

Per altre informazioni sulla configurazione della modalità di distribuzione del servizio di bilanciamento del carico, vedere [configurare una modalità di distribuzione](load-balancer-distribution-mode.md)del servizio di bilanciamento del carico.
