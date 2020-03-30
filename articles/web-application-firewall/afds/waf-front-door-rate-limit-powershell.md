---
title: Configurare la regola limite di frequenza WAF per Front Door - Azure PowerShellConfigure WAF rate limit rule for Front Door - Azure PowerShell
description: Informazioni su come configurare una regola di limite di frequenza per un endpoint Front Door esistente.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649365"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurare una regola di limite di frequenza di Web Application Firewall tramite Azure PowerShellConfigure a Web Application Firewall rate limit rule using Azure PowerShell
La regola del limite di frequenza WAF (Web Application Firewall) di Azure per la porta anteriore di Azure controlla il numero di richieste consentite dai client durante una durata di un minuto.
Questo articolo illustra come configurare una regola di limite di frequenza WAF che controlla il numero di richieste consentite dai client a un'applicazione Web che contiene /promo nell'URL usando Azure PowerShell.This article shows how to configure a WAF rate limit rule that controlla the number of requests allowed from clients to a web application that contains */promo* in the URL using Azure PowerShell.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di limite di frequenza, configurare l'ambiente PowerShell e creare un profilo Front Door.
### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le credenziali di Azure e installare il modulo Az PowerShell.Follow the instructions on the page, to sign in with your Azure credentials, and install Az PowerShell module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Connettersi ad Azure con una finestra di dialogo interattiva per l'accessoConnect to Azure with an interactive dialog for sign in
```
Connect-AzAccount

```
Prima di installare il modulo Front Door, assicurarsi che sia installata la versione corrente di PowerShellGet. Eseguire il comando seguente e riaprire PowerShell.Run the following command and reopen PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installare il modulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Creare un profilo Frontdoor
Creare un profilo Della porta anteriore seguendo le istruzioni descritte in [Guida introduttiva: Creare un profilo Porta frontale](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definire le condizioni di corrispondenza degli URL
Definire una condizione di corrispondenza URL (URL contiene /promo) utilizzando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
L'esempio seguente corrisponde *a /promo* come valore della variabile *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Creare una regola di limite di frequenza personalizzataCreate a custom rate limit rule
Impostare un limite di frequenza utilizzando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). Nell'esempio seguente, il limite è impostato su 1000.In the following example, the limit is set to 1000. Le richieste da qualsiasi client alla pagina promo superiore a 1000 durante un minuto vengono bloccate fino all'inizio del minuto successivo.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurare un criterio di sicurezza

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzureRmResourceGroup`. Configurare quindi un criterio di protezione con una regola di limite di frequenza personalizzata utilizzando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo Front Door.

Nell'esempio seguente viene utilizzato il nome *myResourceGroupFD1* del gruppo di risorse con l'presupposto che sia stato creato il profilo Front Door utilizzando le istruzioni fornite nell'articolo [Guida introduttiva: Creare una porta](../../frontdoor/quickstart-create-front-door.md) frontale.

 utilizzando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Collegare i criteri a un host front-end Front Door anteriore
Collegare l'oggetto criterio di protezione a un host front-end Front Door esistente e aggiornare le proprietà di Front Door. Recuperare innanzitutto l'oggetto Porta anteriore utilizzando il comando [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Impostare quindi la proprietà *front-end WebApplicationFirewallPolicyLink* sull'ID *risorsa* del "$ratePolicy" creato nel passaggio precedente utilizzando il comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

Nell'esempio seguente viene utilizzato il nome *myResourceGroupFD1* del gruppo di risorse con l'presupposto che sia stato creato il profilo Front Door utilizzando le istruzioni fornite nell'articolo [Guida introduttiva: Creare una porta](../../frontdoor/quickstart-create-front-door.md) frontale. Inoltre, nell'esempio seguente, sostituire $frontDoorName con il nome del profilo Front Door. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> È sufficiente impostare la proprietà *WebApplicationFirewallPolicyLink* una sola volta per collegare un criterio di sicurezza a un front-end Front Door. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [Front Door](../../frontdoor/front-door-overview.md). 


