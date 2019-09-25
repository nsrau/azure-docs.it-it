---
title: Configurare una regola di restrizione IP con una regola di web application firewall per il servizio front door di Azure
description: Informazioni su come configurare una regola di web application firewall per limitare gli indirizzi IP per un endpoint di servizio di Azure front door esistente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: adca1bdd0cf525627cc284b1c0d3509beddef131
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219380"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Configurare una regola di restrizione IP con un web application firewall per il servizio front door di Azure
Questo articolo illustra come configurare le regole di restrizione IP in un web application firewall (WAF) per il servizio front door di Azure usando l'interfaccia della riga di comando di Azure, Azure PowerShell o un modello di Azure Resource Manager.

Una regola di controllo di accesso basata su indirizzi IP è una regola WAF personalizzata che consente di controllare l'accesso alle applicazioni Web. Questa operazione viene eseguita specificando un elenco di indirizzi IP o intervalli di indirizzi IP nel formato CIDR (Inter-Domain Routing) senza classe.

Per impostazione predefinita, l'applicazione Web è accessibile da Internet. Se si vuole limitare l'accesso ai client da un elenco di indirizzi IP o intervalli di indirizzi IP noti, è possibile creare una regola di corrispondenza IP che contenga l'elenco di indirizzi IP come valori corrispondenti e imposta Operator su "not" (negazione è true) e l'azione da **bloccare**. Dopo che è stata applicata una regola di restrizione IP, le richieste originate da indirizzi esterni a questo elenco consentito ricevono una risposta 403-accesso negato.  

Un indirizzo IP client può essere diverso dall'indirizzo IP WAF osserva, ad esempio, quando un client accede a WAF tramite un proxy. È possibile creare regole di restrizione IP basate su indirizzi IP client (IndirizzoRemoto) o indirizzi IP socket visualizzati da WAF (SocketAddr). 

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurare un criterio WAF con l'interfaccia della riga di comando di Azure

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di restrizione IP, configurare l'ambiente dell'interfaccia della riga di comando e creare un profilo del servizio front door di Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Configurare l'ambiente dell'interfaccia della riga di comando di Azure
1. Installare l' [interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure o usare Azure cloud Shell. Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **prova** nei comandi dell'interfaccia della riga di comando seguenti e quindi accedere al proprio account Azure nella sessione cloud Shell visualizzata. Dopo l'avvio della sessione, `az extension add --name front-door` immettere per aggiungere l'estensione del servizio Azure front door.
 2. Se si usa l'interfaccia della riga di comando in locale in bash, accedere ad `az login`Azure usando.

#### <a name="create-an-azure-front-door-service-profile"></a>Creare un profilo del servizio front door di Azure
Creare un profilo del servizio front door di Azure seguendo le istruzioni descritte [nella Guida introduttiva: Creare una porta anteriore per un'applicazione](quickstart-create-front-door.md)Web globale a disponibilità elevata.

### <a name="create-a-waf-policy"></a>Crea un criterio WAF

Creare un criterio WAF usando il comando [AZ Network front-door WAF-policy create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . Nell'esempio seguente, sostituire il nome del criterio *IPAllowPolicyExampleCLI* con un nome di criterio univoco.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Aggiungere una regola di controllo di accesso IP personalizzata

Usare il comando [AZ Network front-door WAF-Policy Custom-Rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) per aggiungere una regola di controllo di accesso IP personalizzata per il criterio WAF appena creato.

Negli esempi seguenti:
-  Sostituire *IPAllowPolicyExampleCLI* con il criterio univoco creato in precedenza.
-  Sostituire *IP-address-range-1*, *IP-address-range-2* con il proprio intervallo.

Per prima cosa, creare una regola IP allow per i criteri creati nel passaggio precedente. Nota **: il rinvio** è obbligatorio perché una regola deve includere almeno una condizione di corrispondenza. 

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Successivamente, aggiungere la condizione di corrispondenza IP client alla regola:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
Per la condizione di corrispondenza IP socket (SocketAddr):
  ```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable SocketAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI                                                  

### Find the ID of a WAF policy 
Find a WAF policy's ID by using the [az network front-door waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) command. Replace *IPAllowPolicyExampleCLI* in the following example with your unique policy that you created earlier.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Collegare un criterio WAF a un host front-end del servizio front-end di Azure

Impostare il servizio front door di Azure *WebApplicationFirewallPolicyLink* ID sull'ID criterio usando il comando [AZ Network front-door Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Sostituire *IPAllowPolicyExampleCLI* con il criterio univoco creato in precedenza.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In questo esempio, il criterio WAF viene applicato a **FrontendEndpoints [0]** . È possibile collegare il criterio WAF ai front-end.
> [!Note]
> È necessario impostare la proprietà **WebApplicationFirewallPolicyLink** una sola volta per collegare un criterio WAF a un front-end del servizio di Azure front door. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurare un criterio WAF con Azure PowerShell

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare la configurazione di un criterio di restrizione IP, configurare l'ambiente di PowerShell e creare un profilo del servizio front door di Azure.

#### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell fornisce un set di cmdlet che usano il modello di [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure.

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere a PowerShell usando le credenziali di Azure e quindi installare il modulo AZ.

1. Connettersi ad Azure usando il comando seguente e quindi usare una finestra di dialogo interattiva per accedere.
    ```
    Connect-AzAccount
    ```
 2. Prima di installare un modulo del servizio di Azure front door, verificare che sia installata la versione corrente del modulo PowerShellGet. Eseguire il comando seguente e quindi riaprire PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installare il modulo AZ. FrontDoor usando il comando seguente. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Creare un profilo del servizio front door di Azure
Creare un profilo del servizio front door di Azure seguendo le istruzioni descritte [nella Guida introduttiva: Creare una porta anteriore per un'applicazione](quickstart-create-front-door.md)Web globale a disponibilità elevata.

### <a name="define-an-ip-match-condition"></a>Definire una condizione di corrispondenza IP
Usare il comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) per definire una condizione di corrispondenza IP.
Nell'esempio seguente sostituire *IP-address-range-1*, *IP-address-range-2* con il proprio intervallo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```

Per la condizione di corrispondenza IP socket (SocketAddr):   
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  SocketAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```


### <a name="create-a-custom-ip-allow-rule"></a>Creare una regola di autorizzazione IP personalizzata

Usare il comando [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) per definire un'azione e impostare una priorità. Nell'esempio seguente, le richieste provenienti da indirizzi IP client che corrispondono all'elenco verranno bloccate.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurare un criterio WAF
Trovare il nome del gruppo di risorse che contiene il profilo del servizio Azure front door usando `Get-AzResourceGroup`. Configurare quindi un criterio WAF con la regola IP usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Collegare un criterio WAF a un host front-end del servizio front-end di Azure

Collegare un oggetto Criteri WAF a un host front-end esistente e aggiornare le proprietà del servizio front door di Azure. Per prima cosa, recuperare l'oggetto servizio front door di Azure usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Impostare quindi la proprietà **WebApplicationFirewallPolicyLink** sull'ID risorsa di *$IPAllowPolicyExamplePS*, creato nel passaggio precedente, usando il comando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In questo esempio, il criterio WAF viene applicato a **FrontendEndpoints [0]** . È possibile collegare un criterio WAF ai front-end. È necessario impostare la proprietà **WebApplicationFirewallPolicyLink** una sola volta per collegare un criterio WAF a un front-end del servizio di Azure front door. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurare un criterio WAF con un modello di Gestione risorse
Per visualizzare il modello che crea un criterio del servizio front-end di Azure e un criterio WAF con regole di restrizione IP personalizzate, passare a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo del servizio front door di Azure](quickstart-create-front-door.md).
