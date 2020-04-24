---
title: Riscrivere le intestazioni HTTP in un gateway applicazione di Azure
description: Questo articolo fornisce informazioni su come riscrivere le intestazioni HTTP in applicazione Azure gateway usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947199"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con applicazione Azure gateway-Azure PowerShell

Questo articolo descrive come usare Azure PowerShell per configurare un'istanza di [SKU del gateway applicazione V2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) per riscrivere le intestazioni HTTP in richieste e risposte.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

- Per completare i passaggi descritti in questo articolo, è necessario eseguire Azure PowerShell localmente. È anche necessario che AZ Module versione 1.0.0 o versione successiva sia installato. Eseguire `Import-Module Az` e quindi `Get-Module Az` per determinare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzAccount` per creare una connessione ad Azure.
- È necessario disporre di un'istanza di SKU del gateway applicazione V2. La riscrittura delle intestazioni non è supportata nello SKU V1. Se non si ha lo SKU V2, creare un'istanza di [SKU del gateway applicazione V2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) prima di iniziare.

## <a name="create-required-objects"></a>Creazione di oggetti necessari

Per configurare la riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti necessari per la riscrittura dell'intestazione HTTP:

   - **RequestHeaderConfiguration**: utilizzato per specificare i campi di intestazione della richiesta che si desidera riscrivere e il nuovo valore per le intestazioni.

   - **ResponseHeaderConfiguration**: consente di specificare i campi di intestazione della risposta che si intende riscrivere e il nuovo valore per le intestazioni.

   - **Actiont**: contiene le configurazioni delle intestazioni di richiesta e risposta specificate in precedenza.

   - **Condizione**: una configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP (S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP (S) corrisponde alla condizione di riscrittura.

     Se si associa più di una condizione a un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

   - **RewriteRule**: contiene più combinazioni di operazioni di riscrittura/riscrittura delle condizioni.

   - **RuleSequence**: configurazione facoltativa che consente di determinare l'ordine in cui vengono eseguite le regole di riscrittura. Questa configurazione è utile quando si dispone di più regole di riscrittura in un set di riscrittura. Viene eseguita prima una regola di riscrittura con un valore di sequenza di regole inferiore. Se si assegna lo stesso valore di sequenza della regola a due regole di riscrittura, l'ordine di esecuzione è non deterministico.

     Se RuleSequence non viene specificato in modo esplicito, viene impostato un valore predefinito di 100.

   - **RewriteRuleSet**: contiene più regole di riscrittura che saranno associate a una regola di routing delle richieste.

2. Aggiungere RewriteRuleSet a una regola di routing. La configurazione di riscrittura è collegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione dell'intestazione di riscrittura è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si usa una regola di routing basata sul percorso, la configurazione dell'intestazione di riscrittura è definita nella mappa del percorso URL. In tal caso, si applica solo all'area del percorso specifica di un sito.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ciascun set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Specificare la configurazione della regola di riscrittura dell'intestazione HTTP

In questo esempio verrà modificato un URL di reindirizzamento riscrivendo l'intestazione Location nella risposta HTTP ogni volta che l'intestazione Location contiene un riferimento a azurewebsites.net. A tale scopo, verrà aggiunta una condizione per valutare se l'intestazione Location nella risposta contiene azurewebsites.net. Verrà usato il modello `(https?):\/\/.*azurewebsites\.net(.*)$`. E utilizzeremo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come valore di intestazione. Questo valore sostituirà *azurewebsites.NET* con *contoso.com* nell'intestazione Location.

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

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Recuperare la configurazione della regola di routing delle richieste

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

Per altre informazioni su come configurare alcuni casi d'uso comuni, vedere [scenari comuni di riscrittura dell'intestazione](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).