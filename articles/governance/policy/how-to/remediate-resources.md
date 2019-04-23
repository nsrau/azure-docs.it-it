---
title: Correggere le risorse non conformi
description: Questa guida procedurale descrive come correggere le risorse che non sono conformi ai criteri di Criteri di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fe06e7081e4e3691aeb054985f9f2f3f6dc7d19e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794990"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Correggere le risorse non conformi con Criteri di Azure

Le risorse che non sono conformi ai criteri **deployIfNotExists** possono essere messe in stato di conformità attraverso una **correzione**. La correzione avviene indicando a Criteri di eseguire l'effetto **deployIfNotExists** dei criteri assegnati sulle risorse esistenti. Questo articolo illustra i passaggi necessari per comprendere ed eseguire il processo di correzione con i Criteri.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="how-remediation-security-works"></a>Funzionamento della sicurezza della correzione

Quando il servizio Criteri esegue il modello nella definizione dei criteri **deployIfNotExists**, usa un'[identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md).
Il servizio Criteri crea un'identità gestita per ogni assegnazione, ma è necessario specificare i dettagli su quali ruoli concedere all'identità gestita. Se all'identità gestita non sono assegnati ruoli, viene visualizzato questo errore durante l'assegnazione dei criteri o un'iniziativa. Quando si usa il portale, il servizio Criteri concederà automaticamente all'identità gestita i ruoli elencati dopo che è iniziata l'assegnazione.

![Entità gestita - ruolo mancante](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se una risorsa modificata da **deployIfNotExists** non rientra nell'ambito dell'assegnazione di criteri o il modello accede a proprietà in risorse che non rientrano nell'ambito dell'assegnazione di criteri, all'identità gestita dell'assegnazione deve essere [concesso l'accesso manualmente](#manually-configure-the-managed-identity), altrimenti la distribuzione della correzione avrà esito negativo.

## <a name="configure-policy-definition"></a>Configurare la definizione dei criteri

Il primo passaggio consiste nel definire i ruoli che devono essere presenti nella definizione dei criteri affinché **deployIfNotExists** distribuisca correttamente il contenuto del modello incluso. Nella proprietà **details** aggiungere una proprietà **roleDefinitionIds**. Questa proprietà è una matrice di stringhe che corrispondono ai ruoli nell'ambiente in uso. Per un esempio completo, vedere l'[esempio di deployIfNotExists](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

La proprietà **roleDefinitionIds** usa l'identificatore di risorsa completo e non accetta il **roleName** breve del ruolo. Per ottenere l'ID per il ruolo 'Collaboratore' nell'ambiente in uso, usare il codice seguente:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurare manualmente l'identità gestita

Quando si crea un'assegnazione tramite il portale, il servizio Criteri genera l'identità gestita e le concede i ruoli definiti in **roleDefinitionIds**. Nelle condizioni seguenti è necessario eseguire manualmente i passaggi per creare l'identità gestita e assegnarle le autorizzazioni:

- Quando si usa l'SDK (ad esempio, Azure PowerShell)
- Quando una risorsa esterna all'ambito di assegnazione viene modificata dal modello
- Quando una risorsa esterna all'ambito di assegnazione viene letta dal modello

> [!NOTE]
> Azure PowerShell e .NET sono gli unici SDK che attualmente supportano questa funzionalità.

### <a name="create-managed-identity-with-powershell"></a>Creare un'identità gestita con PowerShell

Per creare un'identità gestita durante l'assegnazione dei criteri, è necessario definire **Location** e usare **AssignIdentity**. L'esempio seguente ottiene la definizione dei criteri predefiniti **Distribuisci Transparent Data Encryption nel database SQL**, imposta il gruppo di risorse di destinazione e quindi crea l'assegnazione.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

La variabile `$assignment` contiene ora l'ID dell'entità di sicurezza dell'identità gestita insieme ai valori standard restituiti durante la creazione di un'assegnazione di criteri. È possibile accedervi tramite `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Concedere ruoli definiti con PowerShell

La nuova identità gestita deve completare la replica tramite Azure Active Directory prima di poter ottenere i ruoli necessari. Al termine della replica, l'esempio seguente esegue l'iterazione della definizione dei criteri in `$policyDef` per la proprietà **roleDefinitionIds** e usa [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) per concedere i ruoli alla nuova identità gestita.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Concedere ruoli definiti tramite il portale

Esistono due modi per concedere a un'identità gestita di un'assegnazione i ruoli definiti tramite il portale: usando il collegamento **Controllo di accesso (IAM)** o modificando l'assegnazione dei criteri o dell'iniziativa e facendo clic su **Salva**.

Per aggiungere un ruolo all'identità gestita dell'assegnazione, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure.

1. Individuare l'assegnazione con un'identità gestita e fare clic sul nome.

1. Individuare la proprietà **ID assegnazione** nella pagina di modifica. L'ID assegnazione sarà simile al seguente:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Il nome dell'identità gestita è l'ultima parte dell'ID di risorsa dell'assegnazione, in questo esempio `2802056bfc094dfb95d4d7a5`. Copiare questa parte dell'ID di risorsa di assegnazione.

1. Passare alla risorsa o al contenitore padre delle risorse (gruppo di risorse, sottoscrizione, gruppo di gestione) per il quale è necessario aggiungere la definizione di ruolo manualmente.

1. Fare clic sul collegamento **Controllo di accesso (IAM)** nella pagina delle risorse e quindi su **+ Aggiungi assegnazione di ruolo** nella parte superiore della pagina del controllo di accesso.

1. Nella definizione dei criteri selezionare il ruolo appropriato che corrisponde a un **roleDefinitionIds**. Lasciare **Assegna accesso a** impostato sul valore predefinito di 'utente, gruppo o applicazione di Azure AD'. Nella casella **Seleziona** incollare o digitare la parte dell'ID di risorsa di assegnazione individuata precedentemente. Al termine della ricerca fare clic sull'oggetto con lo stesso nome per selezionare l'ID e fare clic su **Salva**.

## <a name="create-a-remediation-task"></a>Creare un'attività di correzione

### <a name="create-a-remediation-task-through-portal"></a>Creare un'attività di monitoraggio e aggiornamento mediante il portale

Durante la valutazione l'assegnazione dei criteri con effetto **deployIfNotExists** determina se sono presenti risorse non conformi. Quando vengono rilevate risorse non conformi, vengono visualizzati i dettagli nella pagina **Correzione**. Insieme all'elenco di criteri con risorse non conformi è disponibile l'opzione per attivare un'**attività di correzione**. Questa opzione crea una distribuzione dal modello **deployIfNotExists**.

Per creare un'**attività di correzione**, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

   ![Cercare Criteri di Azure in Tutti i servizi](../media/remediate-resources/search-policy.png)

1. Nella parte sinistra della pagina di Criteri di Azure selezionare **Correzione**.

   ![Selezionare monitoraggio e aggiornamento nella pagina dei criteri](../media/remediate-resources/select-remediation.png)

1. Tutte le assegnazioni dei criteri **deployIfNotExists** con risorse non conformi vengono incluse nella scheda **Criteri da correggere** nella tabella di dati. Fare clic sui criteri con risorse non conformi. Verrà visualizzata la pagina **Nuova attività di correzione**.

   > [!NOTE]
   > Un modo alternativo per aprire la pagina dell'**attività di correzione** consiste nell'individuare e selezionare i criteri nella pagina **Conformità** e quindi fare clic sul pulsante **Crea attività di correzione**.

1. Nella pagina **Nuova attività di correzione** filtrare le risorse da correggere facendo clic sui puntini di sospensione di **Ambito** per selezionare le risorse figlio da cui sono stati assegnati i criteri (fino ai singoli oggetti risorsa). Usare inoltre il menu a discesa **Percorsi** per filtrare ulteriormente le risorse. Verranno corrette solo le risorse elencate nella tabella.

   ![Monitora e aggiorna, selezionare le risorse da risolvere](../media/remediate-resources/select-resources.png)

1. Avviare l'attività di correzione dopo che le risorse sono state filtrate facendo clic su **Correggi**. Verrà aperta la scheda **Attività di correzione** della pagina di conformità dei criteri per mostrare lo stato di avanzamento delle attività.

   ![Monitora e Aggiorna - stato di avanzamento dell'attività di monitoraggio e aggiornamento](../media/remediate-resources/task-progress.png)

1. Fare clic sull'**attività di correzione** nella pagina di conformità dei criteri per visualizzare i dettagli sull'avanzamento. Il filtro usato per l'attività viene visualizzato insieme a un elenco delle risorse da correggere.

1. Nella pagina dell'**attività di correzione** fare clic con il pulsante destro del mouse su una risorsa per visualizzare la distribuzione dell'attività di correzione o la risorsa. Alla fine della riga fare clic su **Eventi correlati** per visualizzare i dettagli, ad esempio un messaggio di errore.

   ![Correggere - menu di scelta rapida delle attività della risorsa](../media/remediate-resources/resource-task-context-menu.png)

Le risorse distribuite tramite un'**attività di correzione** vengono aggiunte nella scheda **Risorse distribuite** della pagina di conformità dei criteri.

### <a name="create-a-remediation-task-through-azure-cli"></a>Creare un'attività di monitoraggio e aggiornamento tramite la CLI di Azure

Per creare un **attività di correzione** con il comando di Azure, usare il `az policy remediation` comandi. Sostituire `{subscriptionId}` con l'ID sottoscrizione e `{myAssignmentId}` con il **deployIfNotExists** ID di assegnazione dei criteri.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Per altri comandi di monitoraggio e aggiornamento e gli esempi, vedere la [monitoraggio e aggiornamento dei criteri di az](/cli/azure/policy/remediation) comandi.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Creare un'attività di monitoraggio e aggiornamento tramite Azure PowerShell

Per creare un **attività di correzione** con Azure PowerShell, usare il `Start-AzPolicyRemediation` comandi. Sostituire `{subscriptionId}` con l'ID sottoscrizione e `{myAssignmentId}` con il **deployIfNotExists** ID di assegnazione dei criteri.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Per altri cmdlet di correzione ed esempi, vedere la [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modulo.

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md)
- Vedere la [struttura delle definizioni dei criteri](../concepts/definition-structure.md)
- Vedere [Informazioni sugli effetti di Criteri](../concepts/effects.md)
- Informazioni su come [creare criteri a livello di programmazione](programmatically-create.md)
- Informazioni su come [ottenere dati sulla conformità](getting-compliance-data.md)
- Scoprire le caratteristiche di un gruppo di gestione con [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md)