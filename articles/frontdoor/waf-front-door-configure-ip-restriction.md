---
title: Configurare una regola di restrizione IP con una regola del firewall applicazione web per l'ingresso principale di Azure
description: Informazioni su come configurare una regola WAF restrizione degli indirizzi IP per un endpoint porta d'ingresso esistente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: b129579916330a34a2a78d98f2c7653f129d3319
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523701"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Configurare una regola di restrizione IP con web application firewall per porta principale Azure (anteprima)
 Questo articolo illustra come configurare le regole di restrizione IP nel firewall applicazione web di Azure (WAF) per la porta d'ingresso usando il modello di comando di Azure, Azure PowerShell o Azure Resource Manager.

Una regola di controllo di accesso basati su indirizzi IP è una regola personalizzata di Web Application firewall che consente di controllare l'accesso alle applicazioni web specificando un elenco di indirizzi IP o intervalli di indirizzi IP nel formato Classless Interdomain Routing (CIDR).

Per impostazione predefinita, l'applicazione web è accessibile da internet. Se si desidera limitare l'accesso alle applicazioni web solo ai client da un elenco di indirizzi IP noti o intervalli di indirizzi IP, è necessario creare due regole di corrispondenza di IP. Prima regola corrispondente IP contiene l'elenco di indirizzi IP come valori corrispondenti e impostarla l'azione su "Consenti". Il secondo con priorità più bassa, consiste nel bloccare tutti gli altri indirizzi IP usando l'operatore "All" e impostare l'azione a "Blocco". Una volta che viene applicata una regola di restrizione IP, le richieste provenienti da indirizzi non inclusi in questo elenco consentito riceve una risposta 403 (accesso negato).  

> [!IMPORTANT]
> La funzionalità di restrizione di Web Application firewall IP per l'ingresso principale di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Configurare i criteri di Web Application firewall con CLI di Azure

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare i criteri di restrizione IP, configurare l'ambiente della riga di comando e creare un profilo di porta principale.

#### <a name="set-up-azure-cli-environment"></a>Configurare l'ambiente CLI di Azure
1. Installare il [CLI Azure](/cli/azure/install-azure-cli), oppure usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include il comando di Azure pre-installato e configurato da usare con il proprio account. Selezionare il **Provalo** pulsante della riga di comando comandi che seguono. Selezionando **Provalo** richiama una Cloud Shell a cui è possibile accedere al proprio account Azure con. Dopo aver avviato una sessione di cloud shell, immettere `az extension add --name front-door` per aggiungere l'estensione di ingresso principale.
 2. Se si usa l'interfaccia della riga di comando in locale in Bash, accedere ad Azure con `az login`.

#### <a name="create-front-door-profile"></a>Creare il profilo di porta principale
Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo di porta principale](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Creare un criterio di Web Application firewall

Creare un criterio di Web Application firewall con il [az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) comando. Nell'esempio seguente sostituire il nome del criterio *IPAllowPolicyExampleCLI* con un nome univoco del criterio.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Aggiungi regola di controllo di accesso IP personalizzato

Aggiungere una regola di controllo di accesso IP personalizzata per il criterio di WAF creato nel passaggio precedente con il [az network waf-policy-regola personalizzata creare](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) comando. 

Nell'esempio seguente:
-  Sostituire *IPAllowPolicyExampleCLI* con i criteri univoco creato in precedenza.
-  Sostituire *indirizzo ip-intervallo-1*, *ip-address-intervallo-2* con il proprio intervallo.

In primo luogo, creare l'indirizzo IP Consenti regola per gli indirizzi specificati.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Successivamente, creare un blocco di tutte le regole IP con priorità più bassa rispetto a regola di assenso l'indirizzo IP precedente. Sostituire il *IPAllowPolicyExampleCLI* con i criteri univoco creato in precedenza.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>Trovare ID dei criteri di Web Application firewall
Trovare l'ID di un criterio di Web Application firewall con il [show waf-criteri di rete az](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) comando. Sostituire il *IPAllowPolicyExampleCLI* con i criteri univoco creato in precedenza.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Criteri di collegamento Web Application firewall in un host di front-end di ingresso principale

Impostare l'ingresso principale *WebApplicationFirewallPolicyLink* ID per l'ID dei criteri con i [update porta d'ingresso di az network](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) comando. Sostituire il *IPAllowPolicyExampleCLI* con i criteri univoco creato in precedenza.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In questo esempio viene applicato il criterio di Web Application firewall per FrontendEndpoints [0]. È possibile collegare i criteri di Web Application firewall per uno qualsiasi del front-end.
> [!Note]
> È sufficiente impostare il **WebApplicationFirewallPolicyLink** proprietà una sola volta per collegare un criterio di Web Application firewall a una porta d'ingresso front-end. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente per il front-end.

## <a name="configure-waf-policy-with-azure-powershell"></a>Configurare i criteri di Web Application firewall con Azure PowerShell

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare i criteri di restrizione IP, configurare l'ambiente di PowerShell e creare un profilo di porta principale.

#### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. 

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina di accedere con le credenziali di Azure e installare il modulo PowerShell di Az.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Connettersi ad Azure con una finestra di dialogo interattivo per l'accesso
```
Connect-AzAccount

```
Prima di installare il modulo di ingresso principale, assicurarsi di che avere la versione corrente di PowerShellGet installato. Eseguire il seguente comando e riaprire PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Installare il modulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Creare un profilo Frontdoor
Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo di porta principale](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Definire la condizione di corrispondenza IP
Usare la [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) comando per definire una condizione di corrispondenza IP. Nel seguente esempio, sostituire *indirizzo ip-intervallo-1*, *ip-address-intervallo-2* con il proprio intervallo.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Creare una corrispondenza con tutte le regole di condizione
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Creare un regola di assenso IP
   Usare la [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) comando per definire un'azione e impostare la priorità. Nell'esempio seguente, saranno possibile richieste dal client gli indirizzi IP che corrispondono all'elenco. 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Creare un blocco di tutte le regole IP con priorità più bassa rispetto a regola di assenso l'indirizzo IP precedente.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Configurare i criteri di Web Application firewall
Individuare il nome del gruppo di risorse contenente il profilo Frontdoor usando `Get-AzResourceGroup`. Successivamente, configurare un criterio di Web Application firewall con la regola di blocco IP utilizzando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Criteri di collegamento Web Application firewall in un host di front-end di ingresso principale

Collegare l'oggetto Criteri di Web Application firewall in un host di front-end di ingresso principale esistente e aggiornare le proprietà di ingresso principale. Recuperare prima l'oggetto porta d'ingresso usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Successivamente, impostare il front-end *WebApplicationFirewallPolicyLink* proprietà sul valore ResourceID del *$IPAllowPolicyExamplePS* creato nel passaggio precedente con il [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) comandi.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In questo esempio viene applicato il criterio di Web Application firewall per FrontendEndpoints [0]. È possibile collegare i criteri di Web Application firewall per uno qualsiasi del front-end. È sufficiente impostare *WebApplicationFirewallPolicyLink* proprietà una sola volta per collegare un criterio di Web Application firewall a una porta d'ingresso front-end. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente per il front-end.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Configurare i criteri di Web Application firewall con modello di Resource Manager
Visualizzare il modello che crea una porta d'ingresso e un criterio di Web Application firewall con regole di restrizioni IP personalizzate [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di porta d'ingresso](quickstart-create-front-door.md).
