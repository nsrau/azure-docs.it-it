---
title: Configurare le regole personalizzate WAF & set di regole predefinito per lo sportello frontale di AzureConfigure WAF custom rules & Default Rule Set for Azure Front Door
description: Informazioni su come configurare un criterio WAF costituito da regole personalizzate e gestite per un endpoint Front Door esistente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186642"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurare un criterio di Firewall applicazione Web con Azure PowerShellConfigure a Web Application Firewall policy using Azure PowerShell

I criteri waF (Web Application Firewall) di Azure definiscono le ispezioni necessarie quando una richiesta arriva alla porta anteriore.
Questo articolo illustra come configurare un criterio WAF costituito da alcune regole personalizzate e con il set di regole predefinito gestito da Azure abilitato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a configurare un criterio di limite di frequenza, configurare l'ambiente PowerShell e creare un profilo Front Door.

### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell

Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le credenziali di Azure e installare il modulo Az PowerShell.Follow the instructions on the page, to sign in with your Azure credentials, and install Az PowerShell module.

#### <a name="sign-in-to-azure"></a>Accedere ad Azure

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

Creare un profilo Della porta anteriore seguendo le istruzioni descritte in [Guida introduttiva: Creare un profilo Porta frontale](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regola personalizzata basata su parametri http

Nell'esempio seguente viene illustrato come configurare una regola personalizzata con due condizioni di corrispondenza utilizzando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Le richieste provengono da un sito specificato come definito dal referrer e la stringa di query non contiene "password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regola personalizzata basata sul metodo di richiesta http

Creare un metodo di blocco delle regole "PUT" utilizzando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) come segue:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Creare una regola personalizzata in base al vincolo di dimensioneCreate a custom rule based on size constraint

L'esempio seguente crea una regola che blocca le richieste con URL più lungo di 100 caratteri usando Azure PowerShell:The following example creates a rule blocking requests with Url that is longer than 100 characters using Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Aggiungi set di regole predefinito gestito

L'esempio seguente crea un set di regole predefinito gestito usando Azure PowerShell:The following example creates a managed Default Rule Set using Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configurare un criterio di sicurezza

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzResourceGroup`. Configurare quindi un criterio di sicurezza con regole create nei passaggi precedenti utilizzando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo Front Door.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Collegare i criteri a un host front-end Front Door anteriore

Collegare l'oggetto criterio di protezione a un host front-end Front Door esistente e aggiornare le proprietà di Front Door. Innanzitutto, recuperare l'oggetto Porta anteriore utilizzando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Impostare quindi la proprietà *front-end WebApplicationFirewallPolicyLink* sull'ID *risorsa* del "$myWAFPolicy" creato nel passaggio precedente utilizzando [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

Nell'esempio seguente viene utilizzato il nome *myResourceGroupFD1* del gruppo di risorse con l'presupposto che sia stato creato il profilo Front Door utilizzando le istruzioni fornite nell'articolo [Guida introduttiva: Creare una porta](../../frontdoor/quickstart-create-front-door.md) frontale. Inoltre, nell'esempio seguente, sostituire $frontDoorName con il nome del profilo Front Door. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> È sufficiente impostare la proprietà *WebApplicationFirewallPolicyLink* una sola volta per collegare un criterio di sicurezza a un front-end Front Door. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [Front Door](../../frontdoor/front-door-overview.md) 
- Scopri di più su [WAF with Front Door](afds-overview.md)
