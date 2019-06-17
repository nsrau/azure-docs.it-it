---
title: Configurare un criterio di web application firewall (WAF) con regole personalizzate e predefinite Ruse impostato per l'ingresso principale - Azure PowerShell
description: Informazioni su come configurare un Web Application firewall criterio è costituito da regole personalizzate e gestite per un endpoint porta d'ingresso esistente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: aac871e71ca0dd30a32e74dd92e417fc95eaa5e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241327"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurare un criterio di firewall applicazione web usando Azure PowerShell
Criteri di Azure web application firewall (WAF) definiscono le ispezioni obbligatorio quando arriva una richiesta in ingresso principale.
Questo articolo illustra come configurare un criterio di Web Application firewall che è costituito da alcune regole personalizzate e con gestiti di Azure predefinito Ruse impostare abilitato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di limite di frequenza, configurare l'ambiente di PowerShell e creare un profilo di porta principale.
### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina di accedere con le credenziali di Azure e installare il modulo PowerShell di Az.

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
Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo di porta principale](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regola personalizzata in base a parametri http

Nell'esempio seguente viene illustrato come configurare una regola personalizzata con due condizioni di corrispondenza utilizzando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Le richieste provengono da un sito specifico come definito dal referrer e stringa di query non contiene "password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regola personalizzata basata sul metodo di richiesta http
Creare una regola che blocca "PUT" metodo che usa [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) come indicato di seguito:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Creare una regola personalizzata basata sul vincolo delle dimensioni

L'esempio seguente crea una regola che blocca le richieste con Url di lunghezza superiore a 100 caratteri con Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Aggiungere gestito predefinito Set di regole

L'esempio seguente crea un gestito predefinito Set di regole usando Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Configurare un criterio di sicurezza

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzResourceGroup`. Successivamente, configurare i criteri di sicurezza con le regole create nei passaggi precedenti usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo di porta principale.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Criteri di collegamento a un host di front-end di ingresso principale
Collegare l'oggetto Criteri di sicurezza in un host di front-end di ingresso principale esistente e aggiornare le proprietà di ingresso principale. In primo luogo, recuperare l'oggetto porta d'ingresso usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Successivamente, impostare il front-end *WebApplicationFirewallPolicyLink* proprietà per il *resourceId* della "$" $myWAFPolicy creato nel passaggio precedente usando [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

L'esempio seguente usa il nome del gruppo di risorse *myResourceGroupFD1* con il presupposto che il profilo Frontdoor sia stato creato seguendo le istruzioni fornite nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md). Inoltre, nell'esempio seguente sostituire $frontDoorName con il nome del profilo di porta principale. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> È sufficiente impostare *WebApplicationFirewallPolicyLink* proprietà una sola volta per collegare i criteri di sicurezza a una porta d'ingresso front-end. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente per il front-end.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [ingresso principale](front-door-overview.md) 
- Altre informazioni su [WAF per ingresso principale](waf-overview.md)
