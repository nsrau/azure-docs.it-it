---
title: Riscrivere le intestazioni HTTP in un gateway applicazione Azure esistente
description: Questo articolo fornisce informazioni su come riscrivere le intestazioni HTTP in un gateway applicazione Azure esistente usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 68da63bcad3c670c5e8bda62dda656e29c41f899
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692923"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Riscrivere le intestazioni HTTP in un gateway applicazione esistente

È possibile usare Azure PowerShell per configurare delle [regole per riscrivere le intestazioni di richiesta e risposta HTTP](rewrite-http-headers.md) in uno [SKU del gateway applicazione con scalabilità automatica e ridondanza della zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) esistente

> [!IMPORTANT]
> Lo SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Recuperare la configurazione di un gateway applicazione esistente
> * Specificare la configurazione della regola di riscrittura dell'intestazione http
> * Aggiornare il gateway applicazione con la configurazione precedente per la riscrittura delle intestazioni http

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Ai fini di questa esercitazione, è necessario eseguire Azure PowerShell in locale. Deve essere installato il modulo Az 1.0.0 o versione successiva. Eseguire `Import-Module Az` e quindi `Get-Module Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzAccount` per creare una connessione ad Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Specificare la configurazione della regola di riscrittura dell'intestazione http**

Configurare i nuovi oggetti necessari per riscrivere le intestazioni http:

- **RequestHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della richiesta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.
- **ResponseHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della risposta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.
- **ActionSet**: questo oggetto contiene le configurazioni delle intestazioni di richiesta e risposta specificate in precedenza.
- **RewriteRule**: questo oggetto contiene tutti gli *ActionSet* specificati in precedenza.
- **RewriteRuleSet**: questo oggetto contiene tutte le *RewriteRule* e deve essere collegato a una regola di gestione della richiesta, di base o basata sul percorso.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Recuperare la configurazione del gateway applicazione esistente

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Recuperare la configurazione della regola di gestione della richiesta esistente

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aggiornare il gateway applicazione con la configurazione per la riscrittura delle intestazioni http

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Eliminare una regola di riscrittura

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un gateway applicazione con regole di routing basate su percorsi URL](./tutorial-url-route-powershell.md)
