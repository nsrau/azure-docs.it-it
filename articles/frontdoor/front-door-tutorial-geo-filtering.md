---
title: 'Esercitazione: Configurare un filtro geografico in un dominio per il servizio Frontdoor di Azure | Microsoft Docs'
description: Questa esercitazione mostra come creare un criterio di filtro geografico semplice e associarlo all'host front-end esistente di Frontdoor
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214879"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Come configurare un criterio di filtro geografico per Frontdoor
Questa esercitazione mostra come usare Azure PowerShell per creare un criterio di filtro geografico di esempio e associarlo all'host front-end esistente di Frontdoor. Questo criterio di filtro geografico di esempio bloccherà le richieste provenienti da tutti i paesi tranne gli Stati Uniti.

## <a name="1-set-up-your-powershell-environment"></a>1. Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le proprie credenziali di Azure e installare AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  Il supporto di [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) sarà presto disponibile.

Prima di installare il modulo Frontdoor, accertarsi di avere la versione corrente di PowerShellGet installata. Eseguire il comando seguente e riaprire PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Installare il modulo AzureRM.FrontDoor. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Definire le condizioni di corrispondenza del filtro geografico
Per iniziare, creare una condizione di corrispondenza di esempio che selezioni le richieste non provenienti da "US". Fare riferimento alla [guida](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) di PowerShell sui parametri per la creazione di una condizione di corrispondenza. Il mapping tra gli indicativi di due lettere e i paesi è disponibile [qui](front-door-geo-filtering.md).

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Aggiungere una condizione di corrispondenza del filtro geografico a una regola con Action e Priority

Creare quindi un oggetto CustomRule `nonUSBlockRule` in base a una condizione di corrispondenza, un'azione (Action) e una priorità (Priority).  Un oggetto CustomRule può avere più condizioni di corrispondenza (MatchCondition).  In questo esempio, Action è impostata su Block e Priority su 1, la priorità più alta.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Fare riferimento alla [guida](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) di PowerShell sui parametri per la creazione di un oggetto CustomRule.

## <a name="4-add-rules-to-a-policy"></a>4. Aggiungere regole a un criterio
In questo passaggio viene creato un oggetto criteri `geoPolicy` contenente la regola `nonUSBlockRule` definita nel passaggio precedente nel gruppo di risorse specificato. Usare il comando `Get-AzureRmResourceGroup` per trovare il valore di ResourceGroupName $resourceGroup.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Fare riferimento alla [guida](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) di PowerShell sui parametri per la creazione di criteri.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Collegare il criterio a un host front-end di Frontdoor
Gli ultimi passaggi sono il collegamento dell'oggetto dei criteri di protezione a un host front-end di Frontdoor esistente e l'aggiornamento delle proprietà di Frontdoor. Dapprima si recupera l'oggetto Frontdoor con il comando [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor), quindi si imposta la relativa proprietà WebApplicationFirewallPolicyLink del front-end sull'ID risorsa di `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Usare il [comando](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor) seguente per aggiornare l'oggetto Frontdoor.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> È sufficiente impostare la proprietà WebApplicationFirewallPolicyLink una volta per collegare un criterio di protezione a un host front-end di Frontdoor. Gli aggiornamenti successivi del criterio verranno applicati automaticamente all'host front-end.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [sicurezza del livello dell'applicazione con Frontdoor](front-door-application-security.md).
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
