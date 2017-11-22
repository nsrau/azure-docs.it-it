---
title: Gestire Protezione DDoS di Azure Standard con Azure PowerShell | Microsoft Docs
description: Informazioni su come gestire Protezione DDoS di Azure Standard con Azure PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: baac97db61b84000557e7150a64ffb64d81ce00c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Gestire Protezione DDoS di Azure Standard con Azure PowerShell

Informazioni su come abilitare e disabilitare la protezione dall'attacco Distributed Denial of Service (DDoS) e usare la telemetria per mitigare un attacco DDoS con Protezione DDoS Standard di Azure. Protezione DDoS Standard protegge le risorse di Azure, come le macchine virtuali, i bilanciamenti del carico e i gateway applicazione a cui è stato assegnato un [indirizzo IP pubblico](virtual-network-public-ip-address.md) di Azure. Per altre informazioni su Protezione DDoS Standard e sulle relative funzionalità, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md). 

>[!IMPORTANT]
>Protezione DDoS di Azure Standard (Protezione DDoS) è attualmente in versione di anteprima. Il servizio Protezione DDoS è supportato da un numero limitato di risorse di Azure ed è disponibile solo in aree specifiche. Per un elenco delle aree disponibili, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md). Per abilitare Protezione DDoS per la sottoscrizione, è necessario [eseguire la registrazione per il servizio](http://aka.ms/ddosprotection) durante la fase di anteprima limitata. Dopo la registrazione, si verrà contattati dal team di Protezione DDoS di Azure che guiderà l'utente nel processo di abilitazione.


## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. Se è necessario installare o aggiornare Azure PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Abilitare Protezione DDoS Standard - Nuova rete virtuale

Per creare una rete virtuale con il servizio Protezione DDoS abilitato, eseguire questo esempio:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

L'esempio crea una rete virtuale con due subnet e due server DNS. Quando si specificano i server DNS nella rete virtuale, le schede di interfaccia di rete e le macchine virtuali distribuite nella rete virtuale ereditano i server DNS come predefiniti. Il servizio Protezione DDoS è abilitato per tutte le risorse protette nella rete virtuale.

> [!WARNING]
> Quando si seleziona un'area, scegliere un'area supportata dall'elenco in [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Abilitare Protezione DDoS in una rete virtuale esistente

Per abilitare Protezione DDoS in una rete virtuale esistente, eseguire questo esempio:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> La rete virtuale deve essere presente in un'area supportata. Per un elenco delle aree supportate, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Disabilitare Protezione DDoS in una rete virtuale

Per disabilitare Protezione DDoS in una rete virtuale, eseguire questo esempio:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>Esaminare lo stato di Protezione DDoS per una rete virtuale 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di Protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurare avvisi per le metriche di Protezione DDoS

Sfruttando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di Protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco.

#### <a name="configure-email-alert-rules-via-azure"></a>Configurare regole di avviso tramite posta elettronica con Azure

1. Visualizzare l'elenco delle sottoscrizioni disponibili. Assicurarsi di lavorare con la giusta sottoscrizione. In caso contrario, impostare la sottoscrizione giusta usando l'output di Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Per elencare le regole esistenti in un gruppo di risorse, usare il comando seguente: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Per creare una regola, sono necessarie le informazioni seguenti: 

    - ID della risorsa per la quale si vuole impostare un avviso.
    - Definizioni delle metriche disponibili per tale risorsa. È possibile ottenere l'ID della risorsa tramite il portale di Azure. Presupponendo che la risorsa sia già stata creata, selezionarla nel portale di Azure. Nella pagina successiva selezionare *Proprietà* nella sezione *Impostazioni*. **ID RISORSA** è un campo nella pagina successiva. È anche possibile usare [Esplora risorse di Azure](https://resources.azure.com/). Un esempio di ID risorsa per un IP pubblico è: `/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    L'esempio seguente crea un avviso per un indirizzo IP pubblico associato a una risorsa in una rete virtuale. La metrica per creare un avviso è **Under DDoS attack or not** (Attacco DDoS in corso). Si tratta di un valore booleano 1 o 0. **1** indica che è in corso un attacco. **0** indica che non è in corso un attacco. L'avviso viene creato quando inizia un attacco negli ultimi 5 minuti.

    Per creare un webhook o inviare un messaggio di posta elettronica quando viene creato un avviso, creare prima di tutto il messaggio di posta elettronica e/o il webhook. Dopo aver creato il messaggio di posta elettronica o il webhook, creare immediatamente la regola con il tag `-Actions`, come illustrato nell'esempio seguente. Non è possibile associare il webhook o i messaggi di posta elettronica a regole esistenti usando PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Verificare che l'avviso sia stato creato correttamente esaminando la regola:

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e alle [app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) per la creazione di avvisi.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurare la registrazione per le metriche di Protezione DDoS

Vedere gli [esempi di avvio rapido di PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per informazioni sull'accesso alla registrazione diagnostica di Azure e sulla sua configurazione con PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)