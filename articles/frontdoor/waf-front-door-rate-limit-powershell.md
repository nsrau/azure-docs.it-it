---
title: Configurare una regola del web application firewall frequenza limite per la porta Front - Azure PowerShell
description: Informazioni su come configurare una regola di limite di velocità per un endpoint porta d'ingresso esistente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: 3701a69ab72abf20a4f1608a1cee56c9cea38aca
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523633"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurare una web application frequenza limite regola del firewall usando Azure PowerShell
La regola limite velocità firewall (WAF) delle applicazioni web di Azure per l'ingresso principale di Azure controlla il numero di richieste consentite da un indirizzo IP singolo client durante una durata di un minuto.
Questo articolo illustra come configurare una regola del limite di frequenza di Web Application firewall che controlla il numero di richieste consentite da un singolo client a un'applicazione web che contiene */promo* nell'URL usando Azure PowerShell.

> [!IMPORTANT]
> La funzionalità di WAF frequenza limite regola per l'ingresso principale di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di limite di frequenza, configurare l'ambiente di PowerShell e creare un profilo di porta principale.
### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina di accedere con le credenziali di Azure e installare il modulo PowerShell di Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Connettersi ad Azure con una finestra di dialogo interattiva per l'accesso
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

## <a name="define-url-match-conditions"></a>Definire condizioni di corrispondenza url
Definire una condizione di corrispondenza URL (URL contiene /promo) usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
L'esempio seguente vengono corrisposte */promo* come valore delle *RequestUri* variabile:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Creare una regola di limite di frequenza personalizzata
Impostare un limite di frequenza usando [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). Nell'esempio seguente, il limite è impostato su 1000. Le richieste da qualsiasi client alla pagina promozionale superiore a 1000 arco di un minuto vengono bloccate finché non viene avviato il minuto successivo.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurare un criterio di sicurezza

Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzureRmResourceGroup`. Successivamente, configurare i criteri di sicurezza con una regola di limite personalizzata aliquota usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) nel gruppo di risorse specificato che contiene il profilo di porta principale.

L'esempio seguente usa il nome del gruppo di risorse *myResourceGroupFD1* con il presupposto che il profilo Frontdoor sia stato creato seguendo le istruzioni fornite nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md).

 using [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Criteri di collegamento a un host di front-end di ingresso principale
Collegare l'oggetto Criteri di sicurezza in un host di front-end di ingresso principale esistente e aggiornare le proprietà di ingresso principale. Recuperare prima l'oggetto porta d'ingresso usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) comando.
Successivamente, impostare il front-end *WebApplicationFirewallPolicyLink* proprietà per il *resourceId* di "$ratePolicy" creato nel passaggio precedente usando [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) comando. 

L'esempio seguente usa il nome del gruppo di risorse *myResourceGroupFD1* con il presupposto che il profilo Frontdoor sia stato creato seguendo le istruzioni fornite nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md). Inoltre, nell'esempio seguente sostituire $frontDoorName con il nome del profilo di porta principale. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> È sufficiente impostare *WebApplicationFirewallPolicyLink* proprietà una sola volta per collegare i criteri di sicurezza a una porta d'ingresso front-end. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente per il front-end.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [ingresso principale](front-door-overview.md) 


