---
title: Gestire Protezione DDoS di Azure Standard con Azure PowerShell | Microsoft Docs
description: Informazioni su come gestire Protezione DDoS di Azure Standard con Azure PowerShell.
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: a1a3688d4ff215d05d2f78cdfa7d402e3fc20be2
ms.contentlocale: it-it
ms.lasthandoff: 09/26/2017

---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Gestire Protezione DDoS di Azure Standard con Azure PowerShell

>[!IMPORTANT]
>Protezione DDoS di Azure Standard (Protezione DDoS) è attualmente in versione di anteprima. Il servizio Protezione DDoS è supportato da un numero limitato di risorse di Azure e solo in aree specifiche. Per abilitare Protezione DDoS per la sottoscrizione, è necessario [eseguire la registrazione per il servizio](http://aka.ms/ddosprotection) durante la fase di anteprima limitata. Dopo la registrazione, si verrà contattati dal team di Protezione DDoS di Azure che fornirà supporto per il processo di abilitazione. Protezione DDoS è disponibile nelle aree Stati Uniti orientali, Stati Uniti occidentali e Stati Uniti centro-occidentali. Durante la fase di anteprima non verrà addebitato alcun costo per l'uso del servizio.

Questo articolo illustra come usare Azure PowerShell per abilitare Protezione DDoS, disabilitare Protezione DDoS e usare la telemetria per mitigare un attacco.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. Se è necessario installare o aggiornare Azure PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>Abilitare Protezione DDoS

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Creare una nuova rete virtuale e abilitare Protezione DDoS

Per creare una rete virtuale con il servizio Protezione DDoS abilitato, eseguire questo esempio:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

L'esempio crea una rete virtuale con due subnet e due server DNS. Quando si specificano i server DNS nella rete virtuale, le schede di interfaccia di rete e le macchine virtuali distribuite nella rete virtuale ereditano i server DNS come predefiniti. Il servizio Protezione DDoS è abilitato per tutte le risorse protette nella rete virtuale.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Abilitare Protezione DDoS in una rete virtuale esistente

Per abilitare Protezione DDoS in una rete virtuale esistente, eseguire questo esempio:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Disabilitare Protezione DDoS in una rete virtuale

Per disabilitare Protezione DDoS in una rete virtuale, eseguire questo esempio:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>Esaminare lo stato di Protezione DDoS per le reti virtuali 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di Protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurare avvisi per le metriche di Protezione DDoS

Sfruttando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di Protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco.

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>Configurare regole di avviso tramite posta elettronica con Azure PowerShell

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

3. Per creare una regola, per prima cosa è necessario disporre di alcune informazioni importanti. 

    - ID della risorsa per la quale si vuole impostare un avviso.
    - Definizioni delle metriche disponibili per tale risorsa. È possibile ottenere l'ID della risorsa tramite il portale di Azure. Presupponendo che la risorsa sia già stata creata, selezionarla nel portale di Azure. Nella pagina successiva selezionare *Proprietà* nella sezione *Impostazioni*. **ID RISORSA** è un campo nella pagina successiva. È anche possibile usare [Esplora risorse di Azure](https://resources.azure.com/). Un esempio di ID risorsa per un IP pubblico è: `/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    L'esempio seguente configura un avviso relativo a un IP pubblico associato a una rete virtuale. La metrica per creare un avviso è **Under DDoS attack or not** (Attacco DDoS in corso). Si tratta di un valore booleano 1 o 0. **1** indica che è in corso un attacco. **0** indica che non è in corso un attacco. L'avviso viene creato quando l'attacco è in corso dagli ultimi cinque minuti.

    Per creare un webhook o inviare un messaggio di posta elettronica quando viene creato un avviso, creare prima di tutto il messaggio di posta elettronica e/o i webhook. Subito dopo creare la regola con il tag -Actions, come illustrato nell'esempio seguente. Non è possibile associare il webhook o i messaggi di posta elettronica a regole già create usando PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. Verificare che gli avvisi siano stati creati correttamente esaminando le singole regole.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e alle [app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) per la creazione di avvisi.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurare la registrazione per le metriche di Protezione DDoS

Vedere gli [esempi di avvio rapido di PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md) per informazioni sull'accesso alla registrazione diagnostica di Azure e sulla sua configurazione con PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
