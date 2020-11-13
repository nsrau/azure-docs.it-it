---
title: Configurare la regola di WAF di restrizione IP per il front-end di Azure
description: Informazioni su come configurare una regola del Web Application Firewall per limitare gli indirizzi IP per un endpoint di Azure front door esistente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: f260bfc7b097931cc1a978e790c1d9dd966703ac
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563512"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configurare una regola di restrizione IP con un Web Application Firewall per lo sportello anteriore di Azure

Questo articolo illustra come configurare le regole di restrizione IP in un Web Application Firewall (WAF) per il front-end di Azure usando il portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell o un modello di Azure Resource Manager.

Una regola di controllo di accesso basata su indirizzi IP è una regola WAF personalizzata che consente di controllare l'accesso alle applicazioni Web. Questa operazione viene eseguita specificando un elenco di indirizzi IP o intervalli di indirizzi IP nel formato CIDR (Class senza Inter-Domain routing).

Per impostazione predefinita, l'applicazione Web è accessibile da Internet. Se si vuole limitare l'accesso ai client da un elenco di indirizzi IP o intervalli di indirizzi IP noti, è possibile creare una regola di corrispondenza IP che contenga l'elenco di indirizzi IP come valori corrispondenti e imposta Operator su "not" (negazione è true) e l'azione da **bloccare**. Dopo che è stata applicata una regola di restrizione IP, le richieste originate da indirizzi esterni a questo elenco consentito ricevono una risposta 403-accesso negato.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configurare un criterio WAF con il portale di Azure

### <a name="prerequisites"></a>Prerequisiti

Creare un profilo di porta anteriore di Azure seguendo le istruzioni descritte nella [Guida introduttiva: creare una porta anteriore per un'applicazione Web globale a disponibilità elevata](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Creare un criterio WAF

1. Nella portale di Azure selezionare **Crea una risorsa** , digitare  **Web Application Firewall** nella casella di ricerca e quindi selezionare **Web Application Firewall (WAF)**.
2. Selezionare **Crea**.
3. Nella pagina **Crea un criterio WAF** usare i valori seguenti per completare la scheda **nozioni di base** :
   
   |Impostazione  |Valore  |
   |---------|---------|
   |Criteri per     |WAF globale (porta anteriore)|
   |Sottoscrizione     |Selezionare la propria sottoscrizione|
   |Gruppo di risorse     |Selezionare il gruppo di risorse in cui si trova la porta anteriore.|
   |Nome criteri     |Digitare un nome per il criterio|
   |Stato criteri     |Attivato|

   Selezionare **Avanti: impostazioni dei criteri**

1. Nella scheda **Impostazioni criteri** selezionare **prevenzione**. Per il **corpo della risposta del blocco** , digitare *che è stato bloccato.* quindi, è possibile osservare che la regola personalizzata è attiva.
2. Selezionare **Avanti: regole gestite**.
3. Selezionare **Avanti: regole personalizzate**.
4. Selezionare **Aggiungi regola personalizzata**.
5. Nella pagina **Aggiungi regola personalizzata** usare i valori di test seguenti per creare una regola personalizzata:

   |Impostazione  |Valore  |
   |---------|---------|
   |Nome regola personalizzata     |FdWafCustRule|
   |Stato     |Attivato|
   |Tipo regola     |Corrispondenza|
   |Priorità    |100|
   |Tipo di corrispondenza     |Indirizzo IP|
   |Variabile corrispondente|IndirizzoRemoto|
   |Operazione|Non contiene|
   |Indirizzo IP o intervallo|rete 10.10.10.0/24|
   |Risultato|Nega traffico|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regola personalizzata":::

   Selezionare **Aggiungi**.
6. Selezionare **Avanti: associazione**.
7. Selezionare **Aggiungi host** front-end.
8. Per **host** front-end selezionare l'host front-end e selezionare **Aggiungi**.
9. Selezionare **Rivedi e crea**.
10. Dopo aver superato la convalida dei criteri, selezionare **Crea**.

### <a name="test-your-waf-policy"></a>Testare i criteri di WAF

1. Al termine della distribuzione del criterio WAF, passare al nome host front-end.
2. Verrà visualizzato il messaggio di blocco personalizzato.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test regola WAF":::

   > [!NOTE]
   > Un indirizzo IP privato è stato intenzionalmente usato nella regola personalizzata per garantire che la regola venga attivata. In una distribuzione effettiva creare regole di *autorizzazione* e di *negazione* usando indirizzi IP per una situazione specifica.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurare un criterio WAF con l'interfaccia della riga di comando di Azure

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di restrizione IP, configurare l'ambiente dell'interfaccia della riga di comando e creare un profilo di sportello anteriore di Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Configurare l'ambiente dell'interfaccia della riga di comando di Azure
1. Installare l' [interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure o usare Azure cloud Shell. Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **prova** nei comandi dell'interfaccia della riga di comando seguenti e quindi accedere al proprio account Azure nella sessione cloud Shell visualizzata. Dopo l'avvio della sessione, immettere `az extension add --name front-door` per aggiungere l'estensione per la porta anteriore di Azure.
 2. Se si usa l'interfaccia della riga di comando in locale in bash, accedere ad Azure usando `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Creare un profilo di sportello anteriore di Azure
Creare un profilo di porta anteriore di Azure seguendo le istruzioni descritte nella [Guida introduttiva: creare una porta anteriore per un'applicazione Web globale a disponibilità elevata](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Creare un criterio WAF

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
-  Sostituire *IP-address-range-1* , *IP-address-range-2* con il proprio intervallo.

Per prima cosa, creare una regola IP allow per i criteri creati nel passaggio precedente. 
> [!NOTE]
> **--il rinvio** è obbligatorio perché una regola deve avere una condizione di corrispondenza da aggiungere nel passaggio successivo.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Successivamente, aggiungere la condizione di corrispondenza alla regola:

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
Trovare l'ID di un criterio WAF usando il comando [AZ Network front-door WAF-Policy Show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . Sostituire *IPAllowPolicyExampleCLI* nell'esempio seguente con il criterio univoco creato in precedenza.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Collegare un criterio WAF a un host front-end di Azure front-end

Impostare l'ID *WebApplicationFirewallPolicyLink* di Azure front door sull'ID criterio usando il comando [AZ Network front-door Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Sostituire *IPAllowPolicyExampleCLI* con il criterio univoco creato in precedenza.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In questo esempio, il criterio WAF viene applicato a **FrontendEndpoints [0]**. È possibile collegare il criterio WAF ai front-end.
> [!Note]
> È necessario impostare la proprietà **WebApplicationFirewallPolicyLink** una sola volta per collegare un criterio WAF a un front-end di Azure front-end. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurare un criterio WAF con Azure PowerShell

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di restrizione IP, configurare l'ambiente di PowerShell e creare un profilo di sportello anteriore di Azure.

#### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell fornisce un set di cmdlet che usano il modello di [Azure Resource Manager](../../azure-resource-manager/management/overview.md) per la gestione delle risorse di Azure.

È possibile installare [Azure PowerShell](/powershell/azure/) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere a PowerShell usando le credenziali di Azure e quindi installare il modulo AZ.

1. Connettersi ad Azure usando il comando seguente e quindi usare una finestra di dialogo interattiva per accedere.
    ```
    Connect-AzAccount
    ```
 2. Prima di installare un modulo di Azure front door, verificare che sia installata la versione corrente del modulo PowerShellGet. Eseguire il comando seguente e quindi riaprire PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installare il modulo AZ. FrontDoor usando il comando seguente. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Creare un profilo di sportello anteriore di Azure
Creare un profilo di porta anteriore di Azure seguendo le istruzioni descritte nella [Guida introduttiva: creare una porta anteriore per un'applicazione Web globale a disponibilità elevata](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definire una condizione di corrispondenza IP
Usare il comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) per definire una condizione di corrispondenza IP.
Nell'esempio seguente sostituire *IP-address-range-1* , *IP-address-range-2* con il proprio intervallo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Creare una regola di autorizzazione IP personalizzata

Usare il comando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) per definire un'azione e impostare una priorità. Nell'esempio seguente, le richieste provenienti da indirizzi IP client che corrispondono all'elenco verranno bloccate.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurare un criterio WAF
Trovare il nome del gruppo di risorse che contiene il profilo di porta anteriore di Azure usando `Get-AzResourceGroup` . Configurare quindi un criterio WAF con la regola IP usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Collegare un criterio WAF a un host front-end di Azure front-end

Collegare un oggetto Criteri WAF a un host front-end esistente e aggiornare le proprietà della porta anteriore di Azure. Per prima cosa, recuperare l'oggetto front door di Azure usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Impostare quindi la proprietà **WebApplicationFirewallPolicyLink** sull'ID risorsa di *$IPAllowPolicyExamplePS* , creato nel passaggio precedente, usando il comando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In questo esempio, il criterio WAF viene applicato a **FrontendEndpoints [0]**. È possibile collegare un criterio WAF ai front-end. È necessario impostare la proprietà **WebApplicationFirewallPolicyLink** una sola volta per collegare un criterio WAF a un front-end di Azure front-end. Gli aggiornamenti dei criteri successivi vengono applicati automaticamente al front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurare un criterio WAF con un modello di Gestione risorse
Per visualizzare il modello che crea un criterio di Azure front door e un criterio WAF con regole di restrizione IP personalizzate, passare a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di sportello anteriore di Azure](../../frontdoor/quickstart-create-front-door.md).