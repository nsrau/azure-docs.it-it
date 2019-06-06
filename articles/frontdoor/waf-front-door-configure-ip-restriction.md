---
title: Configurare una regola di restrizione IP con una regola del firewall applicazione web per il servizio di ingresso principale di Azure
description: Informazioni su come configurare una regola del firewall applicazione web per limitare gli indirizzi IP per un endpoint di servizio di ingresso principale di Azure esistente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743155"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Configurare una regola di restrizione IP con un web application firewall per il servizio di ingresso principale di Azure
Questo articolo illustra come configurare regole di restrizioni IP in un web application firewall (WAF) per il servizio di ingresso principale di Azure tramite la CLI di Azure, Azure PowerShell o un modello Azure Resource Manager.

Una regola di controllo di accesso basato su indirizzo IP è una regola personalizzata di Web Application firewall che consente di controllare l'accesso alle applicazioni web. Ciò avviene specificando un elenco di indirizzi IP o intervalli di indirizzi IP in formato Classless Interdomain Routing (CIDR).

Per impostazione predefinita, l'applicazione web è accessibile da internet. Se si desidera limitare l'accesso ai client da un elenco di indirizzi IP noti o intervalli di indirizzi IP, è necessario creare due regole di corrispondenza di IP. La prima regola corrispondente a IP contiene l'elenco di indirizzi IP come valori di corrispondenza e imposta l'azione **Consenti**. Il secondo, con priorità più bassa, Blocca tutti gli altri indirizzi IP usando il **tutte** operatore e impostando l'azione su **blocco**. Dopo aver applicata una regola di restrizione IP, le richieste originate dagli indirizzi non inclusi in questo elenco consentito ricevano una risposta 403 accesso negato.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurare un criterio di Web Application firewall con la CLI di Azure

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare i criteri di restrizione IP, configurare l'ambiente della riga di comando e creare un profilo del servizio di ingresso principale di Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Configurare l'ambiente della riga di comando di Azure
1. Installare il [CLI Azure](/cli/azure/install-azure-cli), o usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il **Provalo** pulsante nei comandi dell'interfaccia della riga che seguono e quindi accedere al proprio account Azure nella sessione Cloud Shell si apre. Dopo l'avvio della sessione, immettere `az extension add --name front-door` per aggiungere l'estensione del servizio di ingresso principale di Azure.
 2. Se si usa l'interfaccia della riga di comando in locale in Bash, accedere ad Azure usando `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Creare un profilo del servizio di ingresso principale di Azure
Creare un profilo del servizio di ingresso principale di Azure seguendo le istruzioni descritte nel [Guida introduttiva: Creare una porta d'ingresso per un'applicazione web a disponibilità elevata globale](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Creare un criterio di Web Application firewall

Creare un criterio di Web Application firewall tramite il [az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) comando. Nell'esempio che segue, sostituire il nome del criterio *IPAllowPolicyExampleCLI* con un nome univoco del criterio.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Aggiungere una regola di controllo di accesso IP personalizzata

Usare la [az network waf-policy-regola personalizzata creare](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) comando per aggiungere una IP accesso controllo regola personalizzata per i criteri di Web Application firewall appena creato.

Negli esempi seguenti:
-  Sostituire *IPAllowPolicyExampleCLI* con i criteri univoco creato in precedenza.
-  Sostituire *indirizzo ip-intervallo-1*, *ip-address-intervallo-2* con il proprio intervallo.

In primo luogo, creare l'indirizzo IP Consenti regola per gli indirizzi specificati.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
A questo punto, creare un **blocca tutte** regola con priorità più bassa rispetto al precedente **consentire** regola. Anche in questo caso, sostituire *IPAllowPolicyExampleCLI* nell'esempio seguente con i criteri univoco creato in precedenza.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>Trovare l'ID di un criterio di Web Application firewall 
Trovare ID dei criteri WAF usando il [show waf-criteri di rete az](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) comando. Sostituire *IPAllowPolicyExampleCLI* nell'esempio seguente con i criteri univoco creato in precedenza.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Collegare un criterio di Web Application firewall in un host di front-end del servizio di ingresso principale di Azure

Impostare il servizio di ingresso principale di Azure *WebApplicationFirewallPolicyLink* ID per l'ID dei criteri tramite il [update porta d'ingresso di az network](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) comando. Sostituire *IPAllowPolicyExampleCLI* con i criteri univoco creato in precedenza.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In questo esempio viene applicato il criterio di Web Application firewall al **FrontendEndpoints [0]** . È possibile collegare il criterio di Web Application firewall per uno qualsiasi del front-end.
> [!Note]
> È necessario impostare il **WebApplicationFirewallPolicyLink** proprietà una sola volta per collegare un criterio di Web Application firewall per un front-end del servizio di ingresso principale di Azure. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente per il front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurare un criterio di Web Application firewall con Azure PowerShell

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare i criteri di restrizione IP, configurare l'ambiente di PowerShell e creare un profilo del servizio di ingresso principale di Azure.

#### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell fornisce un set di cmdlet che usano il [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modello per la gestione delle risorse di Azure.

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere a PowerShell usando le credenziali di Azure e quindi installare il modulo di Az.

1. Connettersi ad Azure usando il comando seguente e quindi usare una finestra di dialogo interattivo per l'accesso.
    ```
    Connect-AzAccount
    ```
 2. Prima di installare un modulo del servizio di ingresso principale di Azure, assicurarsi di che avere la versione corrente del modulo PowerShellGet installate. Eseguire il comando seguente e quindi riaprire PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installare il modulo Az.FrontDoor usando il comando seguente. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Creare un profilo del servizio di ingresso principale di Azure
Creare un profilo del servizio di ingresso principale di Azure seguendo le istruzioni descritte nel [Guida introduttiva: Creare una porta d'ingresso per un'applicazione web a disponibilità elevata globale](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definire una condizione di corrispondenza IP
Usare la [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) comando per definire una condizione di corrispondenza IP.
Nell'esempio seguente, sostituire *indirizzo ip-intervallo-1*, *ip-address-intervallo-2* con il proprio intervallo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Creare un indirizzo IP *corrispondono alla condizione tutti* regola usando il comando seguente:
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
Creare un **blocca tutte** regola con priorità più bassa rispetto l'indirizzo IP precedente **consentire** regola.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Configurare un criterio di Web Application firewall
Trovare il nome del gruppo di risorse che contiene il profilo del servizio di ingresso principale di Azure usando `Get-AzResourceGroup`. Successivamente, configurare un criterio di Web Application firewall con l'indirizzo IP **blocca tutte** regola usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Collegare un criterio di Web Application firewall in un host di front-end del servizio di ingresso principale di Azure

Collegare un oggetto Criteri di Web Application firewall a una proprietà del servizio di ingresso principale di Azure host e aggiornare front-end esistente. Innanzitutto, recuperare l'oggetto servizio di ingresso principale di Azure utilizzando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Successivamente, impostare il **WebApplicationFirewallPolicyLink** proprietà per l'ID della risorsa *$IPAllowPolicyExamplePS*, creato nel passaggio precedente, utilizzando il [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)comando.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In questo esempio viene applicato il criterio di Web Application firewall al **FrontendEndpoints [0]** . È possibile collegare un criterio di Web Application firewall per uno qualsiasi del front-end. È necessario impostare il **WebApplicationFirewallPolicyLink** proprietà una sola volta per collegare un criterio di Web Application firewall per un front-end del servizio di ingresso principale di Azure. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente per il front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurare un criterio di Web Application firewall con un modello di Resource Manager
Per visualizzare il modello che crea un criterio di servizio di ingresso principale di Azure e un criterio di Web Application firewall con regole di restrizioni IP personalizzate, passare a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo del servizio di ingresso principale di Azure](quickstart-create-front-door.md).
