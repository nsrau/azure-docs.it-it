---
title: Configurare la regola limite di velocità WAF per sportello anteriore-Azure PowerShell
description: Informazioni su come configurare una regola per il limite di velocità per un endpoint della porta anteriore esistente.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 4b8aa72c7b77da8fdde9925325587b67411de8d8
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506414"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurare una regola di limitazione della frequenza del firewall applicazione Web usando Azure PowerShell
La regola relativa al limite di velocità del firewall applicazione Web di Azure (WAF) per la porta anteriore di Azure controlla il numero di richieste consentite dai client durante una durata di un minuto.
Questo articolo illustra come configurare una regola di limitazione della velocità WAF che controlla il numero di richieste consentite dai client a un'applicazione Web che contiene */promo* nell'URL usando Azure PowerShell.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> I limiti di velocità vengono applicati per ogni indirizzo IP del client. Se si dispone di più client che accedono alla porta anteriore da indirizzi IP diversi, avranno i propri limiti di velocità applicati.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di limite di velocità, configurare l'ambiente di PowerShell e creare un profilo di sportello anteriore.
### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le credenziali di Azure e installare il modulo AZ PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Connettersi ad Azure con una finestra di dialogo interattiva per l'accesso
```
Connect-AzAccount

```
Prima di installare il modulo front door, verificare che sia installata la versione corrente di PowerShellGet. Eseguire il comando seguente e riaprire PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installare il modulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Creare un profilo Frontdoor
Creare un profilo di porta anteriore seguendo le istruzioni descritte nella [Guida introduttiva: creare un profilo di sportello anteriore](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definire le condizioni di corrispondenza URL
Definire una condizione di corrispondenza URL (l'URL contiene/promo) usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
L'esempio seguente corrisponde a */promo* come valore della variabile *requestUri* :

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Creare una regola per il limite di velocità personalizzato
Impostare un limite di velocità utilizzando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). Nell'esempio seguente il limite è impostato su 1000. Le richieste da qualsiasi client alla pagina promo che superano 1000 durante un minuto vengono bloccate fino all'avvio del minuto successivo.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurare un criterio di sicurezza

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzureRmResourceGroup`. Configurare quindi un criterio di sicurezza con una regola di limitazione della frequenza personalizzata usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo di porta anteriore.

Nell'esempio seguente viene usato il nome del gruppo di risorse *myResourceGroupFD1* , supponendo che sia stato creato il profilo della porta anteriore usando le istruzioni fornite nell'articolo [introduttivo: creare un portello anteriore](../../frontdoor/quickstart-create-front-door.md) , usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Collegare i criteri a un host front-end front-end
Collegare l'oggetto Criteri di sicurezza a un host front-end della porta anteriore esistente e aggiornare le proprietà della porta anteriore. Recuperare prima l'oggetto porta anteriore usando il comando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) .
Impostare quindi la proprietà front-end *WebApplicationFirewallPolicyLink* su *resourceId* di "$ratePolicy" creato nel passaggio precedente usando il comando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) . 

Nell'esempio seguente viene usato il nome del gruppo di risorse *myResourceGroupFD1* , supponendo che sia stato creato il profilo di porta anteriore usando le istruzioni fornite nell'articolo [introduttivo: creare una porta anteriore](../../frontdoor/quickstart-create-front-door.md) . Nell'esempio seguente, inoltre, sostituire $frontDoorName con il nome del profilo della porta anteriore. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> È necessario impostare la proprietà *WebApplicationFirewallPolicyLink* una sola volta per collegare un criterio di sicurezza a un front-end di sportello anteriore. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [sportello anteriore](../../frontdoor/front-door-overview.md). 


