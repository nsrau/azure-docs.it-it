---
title: 'Esercitazione: Configurare i criteri WAF di filtro geografico - Servizio Frontdoor di Azure'
description: Questa esercitazione mostra come creare un criterio di filtro geografico semplice e associarlo all'host front-end esistente del servizio Frontdoor.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 20aa038e15b1ae5734ad6f463c6f450368617119
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090032"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Esercitazione: Come configurare un criterio web application firewall di filtro geografico per Frontdoor
Questa esercitazione mostra come usare Azure PowerShell per creare un criterio di filtro geografico di esempio e associarlo all'host front-end esistente di Frontdoor. Questo criterio di filtro geografico di esempio bloccherà le richieste provenienti da tutti i paesi/aree tranne gli Stati Uniti.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Definire la condizione di corrispondenza del filtro geografico.
> - Aggiungere la condizione di corrispondenza del filtro geografico a una regola.
> - Aggiungere regole a un criterio.
> - Collegare il criterio web application firewall a un host front-end di Frontdoor.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti
* Prima di iniziare a configurare un criterio di filtro geografico, impostare l'ambiente di PowerShell e creare un profilo Frontdoor.
* Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definire la condizione di corrispondenza del filtro geografico

Creare una condizione di corrispondenza di esempio che seleziona le richieste non provenienti da "US" usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) con parametri. Il mapping tra gli indicativi di due lettere e i paesi/aree geografiche è disponibile [qui](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Aggiungere una condizione di corrispondenza del filtro geografico a una regola con Action e Priority

Creare un oggetto CustomRule `nonUSBlockRule` basato sulla condizione di corrispondenza, un'azione (Action) e una priorità (Priority) usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Un oggetto CustomRule può avere più condizioni di corrispondenza (MatchCondition).  In questo esempio, Action è impostata su Block e Priority su 1, la priorità più alta.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>Aggiungere regole a un criterio
Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzResourceGroup`. Creare quindi un oggetto criterio `geoPolicy` contenente `nonUSBlockRule` usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo Frontdoor. È necessario specificare un nome univoco per il criterio di filtro geografico. 

L'esempio seguente usa il nome del gruppo di risorse *FrontDoorQS_rg0* con il presupposto che il profilo Frontdoor sia stato creato seguendo le istruzioni fornite nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md). Nell'esempio seguente sostituire il nome del criterio *geoPolicyAllowUSOnly* con un nome univoco del criterio.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Collegare il criterio web application firewall a un host front-end di Frontdoor
Collegare l'oggetto criterio web application firewall all'host front-end di Frontdoor e aggiornare le proprietà di Frontdoor. 

A questo scopo, recuperare prima di tutto l'oggetto Frontdoor usando [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Impostare quindi la proprietà WebApplicationFirewallPolicyLink del front-end sull'ID risorsa di `geoPolicy` usando [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> È sufficiente impostare la proprietà WebApplicationFirewallPolicyLink una sola volta per collegare un criterio web application firewall a un host front-end di Frontdoor. Gli aggiornamenti successivi del criterio vengono applicati automaticamente all'host front-end.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti è stata configurata una regola di filtro geografico associata a un criterio WAF. Il criterio è stato quindi collegato a un host front-end di Frontdoor. Se non è più necessaria la regola di filtro geografico o il criterio WAF, è prima necessario annullare l'associazione dei criteri dall'host front-end prima di poter eliminare il criterio WAF.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="Annullare l'associazione dei criteri WAF":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un Web application firewall per Frontdoor, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Web Application Firewall e Frontdoor](front-door-waf.md)
