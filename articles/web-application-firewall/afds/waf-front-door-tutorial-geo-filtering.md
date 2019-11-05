---
title: Configurare i criteri di web application firewall di filtro geografico per il servizio front door di Azure
description: Questa esercitazione Mostra come creare un criterio di filtro geografico e associare i criteri all'host front-end della porta anteriore esistente
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: dd9c3f972a2b598c0c03db2cb80986c08900c609
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517124"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Configurare un criterio WAF per il filtro geografico per la porta anteriore

Questa esercitazione mostra come usare Azure PowerShell per creare un criterio di filtro geografico di esempio e associarlo all'host front-end esistente di Frontdoor. Questo criterio di filtro geografico di esempio bloccherà le richieste provenienti da tutti i paesi/aree tranne gli Stati Uniti.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a configurare un criterio di filtro geografico, impostare l'ambiente di PowerShell e creare un profilo Frontdoor.
### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le proprie credenziali di Azure e installare il modulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Connettersi ad Azure con una finestra di dialogo interattiva per l'accesso

```
Install-Module -Name Az
Connect-AzAccount
```
Assicurarsi di avere la versione corrente di PowerShellGet installata. Eseguire il comando seguente e riaprire PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Installare il modulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Creare un profilo Frontdoor

Creare un profilo di porta anteriore seguendo le istruzioni descritte nella [Guida introduttiva: creare un profilo di sportello anteriore](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definire la condizione di corrispondenza del filtro geografico

Creare una condizione di corrispondenza di esempio che seleziona le richieste non provenienti da "US" usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) con parametri. I codici paese a due lettere per la mappatura dei paesi sono disponibili in informazioni [sul filtro geografico in un dominio per il front-end di Azure](waf-front-door-geo-filtering.md).

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

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzResourceGroup`. Creare quindi un oggetto criterio `geoPolicy` contenente `nonUSBlockRule` usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo Frontdoor. È necessario specificare un nome univoco per il criterio geografico. 

Nell'esempio seguente viene usato il nome del gruppo di risorse *myResourceGroupFD1* , supponendo che sia stato creato il profilo di porta anteriore usando le istruzioni fornite nell'articolo [introduttivo: creare una porta anteriore](../../frontdoor/quickstart-create-front-door.md) . Nell'esempio seguente sostituire il nome del criterio *geoPolicyAllowUSOnly* con un nome univoco del criterio.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Collegare il criterio web application firewall a un host front-end di Frontdoor

Collegare l'oggetto criterio web application firewall all'host front-end di Frontdoor e aggiornare le proprietà di Frontdoor. 

A questo scopo, recuperare prima di tutto l'oggetto Frontdoor usando [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Impostare quindi la proprietà WebApplicationFirewallPolicyLink del front-end sull'ID risorsa di `geoPolicy` usando [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> È sufficiente impostare la proprietà WebApplicationFirewallPolicyLink una sola volta per collegare un criterio web application firewall a un host front-end di Frontdoor. Gli aggiornamenti successivi del criterio vengono applicati automaticamente all'host front-end.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](../overview.md).
- Informazioni su [come creare una Frontdoor](../../frontdoor/quickstart-create-front-door.md).
