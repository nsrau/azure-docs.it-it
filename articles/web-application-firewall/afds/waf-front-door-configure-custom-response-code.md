---
title: Configurare risposte personalizzate per il Web Application Firewall (WAF) con Azure front door
description: Informazioni su come configurare un codice e un messaggio di risposta personalizzati quando WAF blocca una richiesta.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: a995460793686d8293d77965e74e2cbf916925a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005600"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Configurare una risposta personalizzata per il Web Application Firewall (WAF) di Azure

Per impostazione predefinita, quando WAF blocca una richiesta a causa di una regola corrispondente, restituisce un codice di stato 403 con il messaggio di **richiesta è bloccato** . Il messaggio predefinito include anche la stringa di riferimento del rilevamento che può essere usata per collegare le [voci di log](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor) per la richiesta.  È possibile configurare un codice di stato della risposta personalizzato e un messaggio personalizzato con la stringa di riferimento per il caso d'uso. Questo articolo descrive come configurare una pagina di risposta personalizzata quando una richiesta viene bloccata da WAF.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Configurare il codice di stato della risposta personalizzata e il portale di uso del messaggio

È possibile configurare un corpo e un codice di stato della risposta personalizzato in "impostazioni criterio" dal portale di WAF.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="Impostazioni dei criteri di WAF":::

Nell'esempio precedente, il codice di risposta è stato mantenuto come 403 e configurato un breve messaggio "Contattaci", come illustrato nell'immagine seguente:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Impostazioni dei criteri di WAF" inserisce la stringa di riferimento univoca nel corpo della risposta. Il valore corrisponde al campo TrackingReference nei `FrontdoorAccessLog` log e `FrontdoorWebApplicationFirewallLog` .

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Configurare il codice di stato della risposta personalizzata e il messaggio usare PowerShell

### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell

Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere con le proprie credenziali di Azure e installare il modulo Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Connettersi ad Azure con una finestra di dialogo interattiva per l'accesso

```
Connect-AzAccount
Install-Module -Name Az

```
Assicurarsi di avere la versione corrente di PowerShellGet installata. Eseguire il comando seguente e riaprire PowerShell.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installare il modulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Qui viene creato un gruppo di risorse con [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Creare un nuovo criterio WAF con una risposta personalizzata 

Di seguito è riportato un esempio di creazione di un nuovo criterio WAF con il codice di stato della risposta personalizzato impostato su 405 e il messaggio per l' **utente è bloccato.** uso di [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modificare il codice di risposta personalizzato o le impostazioni del corpo della risposta di un criterio WAF esistente usando [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Web Application Firewall con Azure front door](../afds/afds-overview.md)