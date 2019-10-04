---
title: Riscrivere le intestazioni HTTP in un gateway applicazione di Azure
description: Questo articolo fornisce informazioni su come riscrivere le intestazioni HTTP nel Gateway applicazione di Azure usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947199"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con il Gateway applicazione di Azure - Azure PowerShell

Questo articolo descrive come usare Azure PowerShell per configurare un [SKU di Gateway applicazione v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) istanza riscrivere le intestazioni HTTP nelle richieste e risposte.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

- È necessario eseguire Azure PowerShell in locale per completare la procedura descritta in questo articolo. È inoltre necessario disporre di Az versione del modulo 1.0.0 o versione successiva. Eseguire `Import-Module Az` e quindi `Get-Module Az` per determinare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzAccount` per creare una connessione ad Azure.
- È necessario disporre di un'istanza SKU di Gateway applicazione v2. Riscrivere le intestazioni non è supportato nello SKU v1. Se non è disponibile lo SKU v2, creare un [SKU di Gateway applicazione v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) istanza prima di iniziare.

## <a name="create-required-objects"></a>Creare gli oggetti richiesti

Per configurare riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti che sono necessari per la riscrittura di intestazione HTTP:

   - **RequestHeaderConfiguration**: Utilizzato per specificare i campi di intestazione di richiesta che si prevede di riscrittura e il nuovo valore per le intestazioni.

   - **ResponseHeaderConfiguration**: Utilizzato per specificare i campi di intestazione di risposta che si prevede di riscrittura e il nuovo valore per le intestazioni.

   - **ActionSet**: Contiene le configurazioni delle intestazioni di richiesta e risposta specificate in precedenza.

   - **Condizione**: Configurazione facoltativa. Le condizioni di riscrittura di valutare il contenuto di richieste HTTP (S) e risposte. L'azione di riscrittura verificherà se la richiesta HTTP (S) o la risposta soddisfa la condizione di riscrittura.

     Se si associa più di una condizione con un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione con AND logica.

   - **RewriteRule**: Contiene più azioni di riscrittura / riscrivere le combinazioni di condizione.

   - **RuleSequence**: Eseguire una configurazione facoltativa che consente di determinare l'ordine in cui regole di riscrittura. Questa configurazione è utile quando sono presenti più regole di riscrittura in un set di riscrittura. Una regola di riscrittura con un valore di sequenza più basso di regole viene eseguito per prima. Se si assegna lo stesso valore di sequenza di regole da due regole di riscrittura, l'ordine di esecuzione è non deterministico.

     Se si non specifica in modo esplicito il RuleSequence, viene impostato un valore predefinito pari a 100.

   - **RewriteRuleSet**: Contiene più regole di riscrittura degli indirizzi che verrà associate a una regola di routing di richiesta.

2. Collegare il RewriteRuleSet a una regola di routing. La configurazione di riscrittura è allegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura di intestazione è associata a un listener di origine ed è una riscrittura intestazione globale. Quando si usa una regola di routing basato sul percorso, la configurazione di riscrittura di intestazione è definita nel mapping del percorso URL. In tal caso, si applica solo all'area di percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ogni riscrittura impostato su più listener. Ma è possibile applicare solo una riscrittura impostato su un listener specifico.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Specificare l'intestazione HTTP riscrivere configurazione regola

In questo esempio, modifichiamo un URL di reindirizzamento riscrivendo l'intestazione location nella risposta HTTP ogni volta che l'intestazione location contiene un riferimento a azurewebsites.net. A tale scopo, si aggiungerà una condizione da valutare se l'intestazione location nella risposta contiene azurewebsites.net. Si userà il modello `(https?):\/\/.*azurewebsites\.net(.*)$`. E si userà `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come il valore dell'intestazione. Questo valore sostituirà *azurewebsites.net* con *contoso.com* nell'intestazione location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Recuperare la configurazione del gateway applicazione

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Recuperare la configurazione della regola richiesta routing

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aggiornare il gateway applicazione con la configurazione per la riscrittura delle intestazioni HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Eliminare una regola di riscrittura

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come configurare alcuni casi d'uso comuni, vedere [intestazione common riscrivere scenari](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).