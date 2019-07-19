---
title: Configurare un criterio di web application firewall (WAF) con regole personalizzate e set di Ruse predefinito per la porta anteriore-Azure PowerShell
description: Informazioni su come configurare un criterio WAF sono costituite da regole personalizzate e gestite per un endpoint di sportello anteriore esistente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: e9509172ac96a601235cc16e0d6d83c9b2f51902
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849129"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurare un criterio di web application firewall usando Azure PowerShell
I criteri di Azure web application firewall (WAF) definiscono le ispezioni richieste quando una richiesta arriva alla porta anteriore.
Questo articolo illustra come configurare un criterio WAF che è costituito da alcune regole personalizzate e con il set di Ruse predefinito gestito da Azure abilitato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di limite di velocità, configurare l'ambiente di PowerShell e creare un profilo di sportello anteriore.
### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le credenziali di Azure e installare il modulo AZ PowerShell.

#### <a name="sign-in-to-azure"></a>Accedi ad Azure
```
Connect-AzAccount

```
Prima di installare il modulo Frontdoor, accertarsi di avere la versione corrente di PowerShellGet installata. Eseguire il comando seguente e riaprire PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installare il modulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Creare un profilo Frontdoor
Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo di porta anteriore](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regola personalizzata basata su parametri http

Nell'esempio seguente viene illustrato come configurare una regola personalizzata con due condizioni di corrispondenza utilizzando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Le richieste provengano da un sito specificato come definito dal referrer e la stringa di query non contiene "password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regola personalizzata in base al metodo di richiesta http
Creare una regola che blocca il metodo "PUT" utilizzando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) come indicato di seguito:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Creare una regola personalizzata in base al vincolo di dimensione

Nell'esempio seguente viene creata una regola che blocca le richieste con un URL che supera i 100 caratteri utilizzando Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Aggiungi set di regole predefinite gestite

Nell'esempio seguente viene creato un set di regole predefinite gestito utilizzando Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configurare un criterio di sicurezza

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzResourceGroup`. Configurare quindi un criterio di sicurezza con le regole create nei passaggi precedenti usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo della porta anteriore.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Collegare i criteri a un host front-end front-end
Collegare l'oggetto Criteri di sicurezza a un host front-end della porta anteriore esistente e aggiornare le proprietà della porta anteriore. Per prima cosa, recuperare l'oggetto front door usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Impostare quindi la proprietà *WebApplicationFirewallPolicyLink* di front-end su *resourceId* di "$myWAFPolicy $" creato nel passaggio precedente usando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

L'esempio seguente usa il nome del gruppo di risorse *myResourceGroupFD1* con il presupposto che il profilo Frontdoor sia stato creato seguendo le istruzioni fornite nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md). Nell'esempio seguente, inoltre, sostituire $frontDoorName con il nome del profilo della porta anteriore. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> È necessario impostare la proprietà *WebApplicationFirewallPolicyLink* una sola volta per collegare un criterio di sicurezza a un front-end di sportello anteriore. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [sportello anteriore](front-door-overview.md) 
- Scopri di più su [WAF per lo sportello anteriore](waf-overview.md)
