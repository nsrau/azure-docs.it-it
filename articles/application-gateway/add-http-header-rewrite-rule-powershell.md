---
title: Riscrivere le intestazioni HTTP in un gateway applicazione di Azure
description: Questo articolo fornisce informazioni su come riscrivere le intestazioni HTTP nel Gateway applicazione di Azure usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005622"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con il Gateway applicazione di Azure - Azure PowerShell

Questo articolo illustra come usare Azure PowerShell per configurare un [SKU di Gateway applicazione v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) riscrivere le intestazioni HTTP nelle richieste e risposte.

> [!IMPORTANT]
> Lo SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Ai fini di questa esercitazione, è necessario eseguire Azure PowerShell in locale. Deve essere installato il modulo Az 1.0.0 o versione successiva. Eseguire `Import-Module Az` e quindi `Get-Module Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzAccount` per creare una connessione ad Azure.
- È necessario avere un v2 Application Gateway SKU poiché l'intestazione della funzionalità di riscrittura non è supportato per lo SKU v1. Se non è disponibile lo SKU v2, creare un [SKU di Gateway applicazione v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) prima di iniziare.

## <a name="what-is-required-to-rewrite-a-header"></a>Che cosa è necessaria riscrivere un'intestazione

Per configurare riscrittura dell'intestazione HTTP, è necessario:

1. Creare i nuovi oggetti necessari per riscrivere le intestazioni http:

   - **RequestHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della richiesta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.

   - **ResponseHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della risposta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.

   - **ActionSet**: questo oggetto contiene le configurazioni delle intestazioni di richiesta e risposta specificate in precedenza.

   - **Condizione**: È una configurazione facoltativa. Se viene aggiunta una condizione di riscrittura, valuterà il contenuto delle richieste HTTP (S) e le risposte. La decisione di eseguire l'azione di riscrittura associato alla condizione di riscrittura si baseranno se la richiesta HTTP (S) o la risposta corrispondente con la condizione di riscrittura. 

     Se più condizioni sono associati a un'azione, quindi l'azione verrà eseguita solo quando vengono soddisfatte tutte le condizioni, ad esempio, verrà eseguita un'operazione con AND logica.

   - **RewriteRule**: contiene più riscrittura azione - riscrittura condizione combinazioni.

   - **RuleSequence**: Si tratta di una configurazione facoltativa. Consente di determinare l'ordine in cui vengono eseguite le regole di riscrittura diversi. Ciò risulta utile quando sono presenti più regole di riscrittura in un set di riscrittura. La regola di riscrittura con valore di sequenza minore regola Ottiene eseguita per prime. Se si fornisce la stessa sequenza di regole da due regole di riscrittura, l'ordine di esecuzione sarà non deterministica.

     Se si non specifica in modo esplicito il RuleSequence, verrà impostato un valore predefinito pari a 100.

   - **RewriteRuleSet**: questo oggetto contiene più regole di riscrittura degli indirizzi che verrà associate a una regola di routing di richiesta.

2. È necessario collegare il rewriteRuleSet con una regola di routing. Questo avviene perché la configurazione di riscrittura è allegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura delle intestazioni viene associata a un listener di origine e la riscrittura è globale. Quando viene usata una regola di routing basata sul percorso, la configurazione di riscrittura delle intestazioni è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione http e ogni set di riscrittura degli indirizzi può essere applicato a più listener. Tuttavia, è possibile applicare solo una riscrittura impostato su un listener specifico.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Specificare la configurazione della regola di riscrittura dell'intestazione http**

In questo esempio, si modificherà l'URL di reindirizzamento riscrivendo l'intestazione location nella risposta http ogni volta che l'intestazione location contiene un riferimento a "azurewebsites.net". A tale scopo, si aggiungerà una condizione da valutare se l'intestazione location nella risposta contiene azurewebsites.net usando il modello `(https?):\/\/.*azurewebsites\.net(.*)$`. Si userà `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come il valore dell'intestazione. In questo sostituirà *azurewebsites.net* con *contoso.com* nell'intestazione location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Recuperare la configurazione del gateway applicazione esistente

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Recuperare la configurazione della regola di gestione della richiesta esistente

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aggiornare il gateway applicazione con la configurazione per la riscrittura delle intestazioni http

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

Per altre informazioni sulla configurazione necessaria per eseguire alcuni dei casi d'uso, vedere [intestazione common riscrivere scenari](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).