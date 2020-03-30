---
title: Configurare la regola WAF di restrizione IP per il portelare anteriore di AzureConfigure IP restriction WAF rule for Azure Front Door
description: Informazioni su come configurare una regola di Firewall applicazione Web per limitare gli indirizzi IP per un endpoint di Azure Front Door esistente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336098"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configurare una regola di restrizione IP con un Firewall applicazione Web per il portetnofront di AzureConfigure an IP restriction rule with a Web Application Firewall for Azure Front Door

Questo articolo illustra come configurare le regole di restrizione IP in un Web Application Firewall (WAF) per Azure Front Door usando il portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell o un modello di Azure Resource Manager.This article shows you how to configure IP Application restriction rules in a Web Application Firewall (WAF) for Azure Front Door by using the Azure portal, Azure CLI, Azure PowerShell, or an Azure Resource Manager template.

Una regola di controllo di accesso basata su indirizzo IP è una regola WAF personalizzata che consente di controllare l'accesso alle applicazioni Web. A tale scopo, specifica un elenco di indirizzi IP o intervalli di indirizzi IP in formato CIDR (Classless Inter-Domain Routing).

Per impostazione predefinita, l'applicazione Web è accessibile da Internet.By default, your web application is accessible from the Internet. Se si desidera limitare l'accesso ai client da un elenco di indirizzi IP noti o intervalli di indirizzi IP, è possibile creare una regola di corrispondenza IP che contiene l'elenco di indirizzi IP come valori corrispondenti e imposta operator su "Not" (negate is true) e l'azione **Blocca**. Dopo l'applicazione di una regola di restrizione IP, le richieste provenienti da indirizzi esterni a questo elenco consentito ricevono una risposta 403 Forbidden.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configurare un criterio WAF con il portale di AzureConfigure a WAF policy with the Azure portal

### <a name="prerequisites"></a>Prerequisiti

Creare un profilo di Azure Front Door seguendo le istruzioni descritte in [Guida introduttiva: Creare una porta frontale per un'applicazione Web globale a disponibilità elevata.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Creare un criterio WAF

1. Nel portale di Azure selezionare **Crea una risorsa**, digitare Firewall applicazione **Web** nella casella di ricerca e quindi selezionare Web Application **Firewall (WAF)**.
2. Selezionare **Crea**.
3. Nella pagina **Crea criteri WAF** utilizzare i valori seguenti per completare la scheda **Nozioni di base:**
   
   |Impostazione  |valore  |
   |---------|---------|
   |Politica per     |WAF globale (porta anteriore)|
   |Subscription     |Selezionare la propria sottoscrizione|
   |Resource group     |Selezionare il gruppo di risorse in cui si trova lo sportello frontale.|
   |Nome criteri     |Digitare un nome per il criterio|
   |Stato criteri     |Attivato|

   Selezionare **Successivo: Impostazioni dei criteri**

1. Nella scheda **Impostazioni criteri** selezionare **Prevenzione**. Per il corpo della **risposta Blocca**, digitare Sei *stato bloccato!* in modo da poter vedere che la regola personalizzata è in vigore.
2. Selezionare **Avanti: Regole gestite**.
3. Selezionare **Avanti: Regole personalizzate**.
4. Selezionare **Aggiungi regola personalizzata**.
5. Nella pagina **Aggiungi regola personalizzata** utilizzare i valori di test seguenti per creare una regola personalizzata:On the Add custom rule page, use the following test values to create a custom rule:

   |Impostazione  |valore  |
   |---------|---------|
   |Nome regola personalizzata     |FdWafCustRule|
   |Stato     |Attivato|
   |Tipo regola     |Corrispondente|
   |Priorità    |100|
   |Tipo di corrispondenza     |Indirizzo IP|
   |Variabile di corrispondenza|RemoteAddr|
   |Operazione|Non contiene|
   |Indirizzo IP o intervallo|10.10.10.0/24|
   |Risultato|Nega traffico|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regola personalizzata":::

   Selezionare **Aggiungi**.
6. Selezionare **Avanti: Associazione**.
7. Selezionare **Aggiungi host front-end**.
8. Per **Host front-end**, selezionare l'host front-end e scegliere **Aggiungi**.
9. Selezionare **Revisione e creazione**.
10. Al termine della convalida dei criteri, selezionare **Crea**.

### <a name="test-your-waf-policy"></a>Testare i criteri WAF

1. Al termine della distribuzione dei criteri WAF, passare al nome host Front Door frontend.
2. Verrà visualizzato il messaggio di blocco personalizzato.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test delle regole WAF":::

   > [!NOTE]
   > Un indirizzo IP privato è stato intenzionalmente utilizzato nella regola personalizzata per garantire che la regola si attiverà. In una distribuzione effettiva, creare regole *di autorizzazione* e *negazione* utilizzando gli indirizzi IP per la situazione specifica.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurare un criterio WAF con l'interfaccia della riga di comando di AzureConfigure a WAF policy with the Azure CLI

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di restrizione IP, configurare l'ambiente CLI e creare un profilo porta frontale di Azure.Before you begin to configure an IP restriction policy, set up your CLI environment and create an Azure Front Door profile.

#### <a name="set-up-the-azure-cli-environment"></a>Configurare l'ambiente CLI di AzureSet up the Azure CLI environment
1. Installare [l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure o usare Azure Cloud Shell.Install the Azure CLI , or use Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi dell'interfaccia della riga di comando che seguono e quindi accedere all'account Azure nella sessione di Cloud Shell visualizzata. Dopo l'avvio `az extension add --name front-door` della sessione, immettere per aggiungere l'estensione Porta frontale di Azure.After the session starts, enter to add the Azure Front Door extension.
 2. Se si usa l'interfaccia della riga di comando `az login`in locale in Bash, accedere ad Azure usando .

#### <a name="create-an-azure-front-door-profile"></a>Creare un profilo della porta frontale di AzureCreate an Azure Front Door profile
Creare un profilo di Azure Front Door seguendo le istruzioni descritte in [Guida introduttiva: Creare una porta frontale per un'applicazione Web globale a disponibilità elevata.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Creare un criterio WAF

Creare un criterio WAF utilizzando il comando [az network front-door waf-policy create.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) Nell'esempio seguente sostituire il nome del criterio *IPAllowPolicyExampleCLI* con un nome di criterio univoco.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Aggiungere una regola di controllo di accesso IP personalizzataAdd a custom IP access control rule

Utilizzare il comando [az network front-door waf-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) per aggiungere una regola di controllo di accesso IP personalizzata per il criterio WAF appena creato.

Negli esempi seguenti:
-  Sostituire *IPAllowPolicyExampleCLI* con i criteri univoci creati in precedenza.
-  Sostituire *ip-address-range-1*, *ip-address-range-2* con il proprio intervallo.

Creare innanzitutto una regola di autorizzazione IP per il criterio creato dal passaggio precedente. 
> [!NOTE]
> **--defer** è obbligatorio perché una regola deve avere una condizione di corrispondenza da aggiungere nel passaggio successivo.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Successivamente, aggiungere la condizione di corrispondenza alla regola:Next, add match condition to the rule:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Trovare l'ID di un criterio WAF 
Trovare l'ID di un criterio WAF utilizzando il comando [az network front-door waf-policy show.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) Sostituire *IPAllowPolicyExampleCLI* nell'esempio seguente con i criteri univoci creati in precedenza.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Collegare un criterio WAF a un host front-end Azure Front Door

Impostare l'ID *WebApplicationFirewallPolicyLink* della porta anteriore di Azure sull'ID criterio utilizzando il comando az [network front-door update.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Sostituire *IPAllowPolicyExampleCLI* con i criteri univoci creati in precedenza.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In questo esempio, il criterio WAF viene applicato a **FrontendEndpoints[0]**. È possibile collegare la politica WAF a qualsiasi front-end.
> [!Note]
> È necessario impostare la proprietà WebApplicationFirewallPolicyLink una sola volta per collegare un criterio WAF a un front-end Azure Front Door.You need to set the **WebApplicationFirewallPolicyLink** only once to link a WAF policy to an Azure Front Door front-end. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurare un criterio WAF con Azure PowerShellConfigure a WAF policy with Azure PowerShell

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di restrizione IP, configurare l'ambiente PowerShell e creare un profilo porta frontale di Azure.Before you begin to configure an IP restriction policy, set up your PowerShell environment and create an Azure Front Door profile.

#### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell offre un set di cmdlet che usano il modello di Azure Resource Manager per la gestione delle risorse di Azure.Azure PowerShell provides a set of cmdlets that use the [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) model for managing Azure resources.

È possibile installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere a PowerShell usando le credenziali di Azure e quindi installare il modulo Az.Follow the instructions on the page to sign in to PowerShell by using your Azure credentials, and then install the Az module.

1. Connettersi ad Azure usando il comando seguente e quindi usare una finestra di dialogo interattiva per accedere.
    ```
    Connect-AzAccount
    ```
 2. Prima di installare un modulo Porta frontale di Azure, assicurarsi di avere installato la versione corrente del modulo PowerShellGet. Eseguire il comando seguente e quindi riaprire PowerShell.Run the following command, and then reopen PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installare il modulo Az.FrontDoor utilizzando il comando riportato di seguito. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Creare un profilo della porta frontale di AzureCreate an Azure Front Door profile
Creare un profilo di Azure Front Door seguendo le istruzioni descritte in [Guida introduttiva: Creare una porta frontale per un'applicazione Web globale a disponibilità elevata.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Definire una condizione di corrispondenza IPDefine an IP match condition
Utilizzare il comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) per definire una condizione di corrispondenza IP.
Nell'esempio seguente sostituire *ip-address-range-1*, *ip-address-range-2* con un intervallo personalizzato.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Creare una regola di autorizzazione IP personalizzataCreate a custom IP allow rule

Utilizzare il comando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) per definire un'azione e impostare una priorità. Nell'esempio seguente, le richieste non provenienti da indirizzi IP client che corrispondono all'elenco verranno bloccate.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurare un criterio WAF
Trovare il nome del gruppo di risorse che `Get-AzResourceGroup`contiene il profilo di Azure Front Door utilizzando . Configurare quindi un criterio WAF con la regola IP utilizzando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Collegare un criterio WAF a un host front-end Azure Front Door

Collegare un oggetto criterio WAF a un host front-end esistente e aggiornare le proprietà di Azure Front Door.Link a WAF policy object to an existing front-end host and update Azure Front Door properties. Recuperare innanzitutto l'oggetto Azure Front Door utilizzando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Impostare quindi la proprietà **WebApplicationFirewallPolicyLink** sull'ID risorsa di *$IPAllowPolicyExamplePS*, creato nel passaggio precedente, utilizzando il comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In questo esempio, il criterio WAF viene applicato a **FrontendEndpoints[0]**. È possibile collegare un criterio WAF a qualsiasi front-end. È necessario impostare la proprietà WebApplicationFirewallPolicyLink una sola volta per collegare un criterio WAF a un front-end Azure Front Door.You need to set the **WebApplicationFirewallPolicyLink** only once to link a WAF policy to an Azure Front Door front-end. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurare un criterio WAF con un modello di Resource ManagerConfigure a WAF policy with a Resource Manager template
Per visualizzare il modello che crea un criterio di Porta frontale di Azure e un criterio WAF con regole di restrizione IP personalizzate, passare a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di Porta frontale](../../frontdoor/quickstart-create-front-door.md)di Azure.
