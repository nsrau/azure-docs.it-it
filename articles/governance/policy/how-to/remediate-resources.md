---
title: Correggere le risorse non conformi
description: Questa guida descrive come correggere le risorse che non sono conformi ai criteri di Criteri di Azure.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 52d8ef6dd66c52edd574b2ccfa51da16623a1afb
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651351"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Correggere le risorse non conformi con Criteri di Azure

Le risorse che non sono conformi ai criteri **deployIfNotExists** o **modify** possono essere messe in stato di conformità attraverso una **correzione**. La correzione viene eseguita indicando a criteri di Azure di eseguire l'effetto **deployIfNotExists** o le **operazioni di modifica** del criterio assegnato nelle risorse esistenti, sia che si tratti di un gruppo di gestione, una sottoscrizione, un gruppo di risorse o una singola risorsa. Questo articolo illustra i passaggi necessari per comprendere ed eseguire il processo di correzione con i Criteri di Azure.

## <a name="how-remediation-security-works"></a>Funzionamento della sicurezza della correzione

Quando il servizio Criteri di Azure esegue il modello nella definizione dei criteri **deployIfNotExists**, usa un'[identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md).
Il servizio Criteri di Azure crea un'identità gestita per ogni assegnazione, ma è necessario specificare i dettagli su quali ruoli concedere all'identità gestita. Se all'identità gestita non sono assegnati ruoli, viene visualizzato questo errore durante l'assegnazione dei criteri o un'iniziativa. Quando si usa il portale, il servizio Criteri di Azure concede automaticamente all'identità gestita i ruoli elencati dopo l'inizio dell'assegnazione. Il _percorso_ dell'identità gestita non ha alcun effetto sull'operazione con i criteri di Azure.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Screenshot di un criterio deployIfNotExists in cui manca un'autorizzazione definita per l'identità gestita." border="false":::

> [!IMPORTANT]
> Se una risorsa modificata tramite **deployIfNotExists** o **modify** non rientra nell'ambito dell'assegnazione di criteri o il modello accede a proprietà in risorse che non rientrano nell'ambito dell'assegnazione di criteri, all'identità gestita dell'assegnazione deve essere [concesso l'accesso manualmente](#manually-configure-the-managed-identity), altrimenti la distribuzione della correzione avrà esito negativo.

## <a name="configure-policy-definition"></a>Configurare la definizione dei criteri

Il primo passaggio consiste nel definire i ruoli che devono essere presenti nella definizione dei criteri affinché **deployIfNotExists** e **modify** distribuisca correttamente il contenuto del modello incluso. Nella proprietà **details** aggiungere una proprietà **roleDefinitionIds**. Questa proprietà è una matrice di stringhe che corrispondono ai ruoli nell'ambiente in uso. Per un esempio completo, vedere gli esempi di [deployIfNotExists](../concepts/effects.md#deployifnotexists-example) o [modify](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

La proprietà **roleDefinitionIds** usa l'identificatore di risorsa completo e non accetta il **roleName** breve del ruolo. Per ottenere l'ID per il ruolo 'Collaboratore' nell'ambiente in uso, usare il codice seguente:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurare manualmente l'identità gestita

Quando si crea un'assegnazione tramite il portale, il servizio Criteri di Azure genera l'identità gestita e le concede i ruoli definiti in **roleDefinitionIds**. Nelle condizioni seguenti è necessario eseguire manualmente i passaggi per creare l'identità gestita e assegnarle le autorizzazioni:

- Quando si usa l'SDK (ad esempio, Azure PowerShell)
- Quando una risorsa esterna all'ambito di assegnazione viene modificata dal modello
- Quando una risorsa esterna all'ambito di assegnazione viene letta dal modello

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

Esistono due modi per concedere a un'identità gestita di un'assegnazione i ruoli definiti tramite il portale, usando il **controllo di accesso (IAM)** o modificando l'assegnazione di criteri o iniziative e selezionando **Salva**.

Per aggiungere un ruolo all'identità gestita dell'assegnazione, seguire questa procedura:

1. Avviare il servizio criteri di Azure nel portale di Azure selezionando **tutti i servizi**, quindi cercando e selezionando **criteri**.

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure.

1. Individuare l'assegnazione che ha un'identità gestita e selezionare il nome.

1. Individuare la proprietà **ID assegnazione** nella pagina di modifica. L'ID assegnazione sarà simile al seguente:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Il nome dell'identità gestita è l'ultima parte dell'ID di risorsa dell'assegnazione, in questo esempio `2802056bfc094dfb95d4d7a5`. Copiare questa parte dell'ID di risorsa di assegnazione.

1. Passare alla risorsa o al contenitore padre delle risorse (gruppo di risorse, sottoscrizione, gruppo di gestione) per il quale è necessario aggiungere la definizione di ruolo manualmente.

1. Selezionare il collegamento **controllo di accesso (IAM)** nella pagina risorse e quindi selezionare **+ Aggiungi assegnazione ruolo** nella parte superiore della pagina controllo di accesso.

1. Nella definizione dei criteri selezionare il ruolo appropriato che corrisponde a un **roleDefinitionIds**.
   Lasciare **Assegna accesso a** impostato sul valore predefinito di 'utente, gruppo o applicazione di Azure AD'. Nella casella **Seleziona** incollare o digitare la parte dell'ID di risorsa di assegnazione individuata precedentemente. Al termine della ricerca, selezionare l'oggetto con lo stesso nome per selezionare ID e selezionare **Salva**.

## <a name="create-a-remediation-task"></a>Creare un'attività di correzione

### <a name="create-a-remediation-task-through-portal"></a>Creare un'attività di correzione attraverso il portale

Durante la valutazione l'assegnazione dei criteri con gli effetti **deployIfNotExists** o **modify** determina se sono presenti risorse non conformi. Quando vengono rilevate risorse non conformi, vengono visualizzati i dettagli nella pagina **Correzione**. Insieme all'elenco di criteri con risorse non conformi è disponibile l'opzione per attivare un'**attività di correzione**. Questa opzione crea una distribuzione dal modello **deployIfNotExists** o dalle operazioni **modify**.

Per creare un'**attività di correzione**, seguire questa procedura:

1. Avviare il servizio criteri di Azure nel portale di Azure selezionando **tutti i servizi**, quindi cercando e selezionando **criteri**.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Screenshot della ricerca dei criteri in tutti i servizi." border="false":::

1. Nella parte sinistra della pagina di Criteri di Azure selezionare **Correzione**.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Screenshot del nodo di monitoraggio e aggiornamento nella pagina dei criteri." border="false":::

1. Tutte le assegnazioni dei criteri **deployIfNotExists** e **modify** con risorse non conformi vengono incluse nella scheda **Criteri da correggere** nella tabella di dati. Selezionare un criterio con risorse non conformi. Verrà visualizzata la pagina **Nuova attività di correzione**.

   > [!NOTE]
   > Un modo alternativo per aprire la pagina **attività di monitoraggio e aggiornamento** consiste nel trovare e selezionare i criteri nella pagina **conformità** , quindi selezionare il pulsante **Crea attività di correzione** .

1. Nella pagina **Nuova attività di correzione** filtrare le risorse da correggere facendo clic sui puntini di sospensione di **Ambito** per selezionare le risorse figlio da cui sono stati assegnati i criteri (fino ai singoli oggetti risorsa). Usare inoltre il menu a discesa **Percorsi** per filtrare ulteriormente le risorse. Verranno corrette solo le risorse elencate nella tabella.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Screenshot del nodo di correzione e della griglia delle risorse da correggere." border="false":::

1. Avviare l'attività di monitoraggio e aggiornamento dopo avere filtrato le risorse selezionando **monitora e aggiorna**. Si apre la scheda **Attività di correzione** della pagina di conformità dei criteri per mostrare lo stato di avanzamento delle attività. Le distribuzioni create dall'attività di correzione iniziano immediatamente.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Screenshot della scheda attività di correzione e stato di avanzamento delle attività di correzione esistenti." border="false":::

1. Selezionare l' **attività di monitoraggio e aggiornamento** nella pagina conformità criteri per ottenere informazioni dettagliate sullo stato di avanzamento. Il filtro usato per l'attività viene visualizzato insieme a un elenco delle risorse da correggere.

1. Nella pagina **attività di correzione** , fare clic con il pulsante destro del mouse su una risorsa per visualizzare la distribuzione dell'attività di correzione o la risorsa. Alla fine della riga selezionare **gli eventi correlati** per visualizzare i dettagli, ad esempio un messaggio di errore.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Screenshot del menu di scelta rapida per una risorsa nella scheda attività di correzione." border="false":::

Le risorse distribuite tramite un'**attività di correzione** vengono aggiunte nella scheda **Risorse distribuite** della pagina di conformità dei criteri.

### <a name="create-a-remediation-task-through-azure-cli"></a>Creare un'attività di correzione attraverso l'interfaccia della riga di comando di Azure

Per creare un'**attività di correzione** con l'interfaccia della riga di comando di Azure, usare i comandi `az policy remediation`. Sostituire `{subscriptionId}` con l'ID sottoscrizione e `{myAssignmentId}` con l'ID di assegnazione dei criteri **deployIfNotExists** o **modify**.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Per altri comandi ed esempi di correzione, vedere i comandi [az policy remediation](/cli/azure/policy/remediation).

### <a name="create-a-remediation-task-through-azure-powershell"></a>Creare un'attività di correzione attraverso Azure PowerShell

Per creare un'**attività di correzione** con Azure PowerShell, usare i comandi `Start-AzPolicyRemediation`. Sostituire `{subscriptionId}` con l'ID sottoscrizione e `{myAssignmentId}` con l'ID di assegnazione dei criteri **deployIfNotExists** o **modify**.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Per altri cmdlet ed esempi di correzione, vedere il modulo [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights).

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Creare un'attività di correzione durante l'assegnazione dei criteri nel portale di Azure

Un modo efficace per creare un'attività di correzione consiste nell'eseguire questa operazione dal portale di Azure durante l'assegnazione dei criteri. Se la definizione dei criteri da assegnare è un **deployIfNotExists** o un effetto di **modifica** , la procedura guidata nella scheda **monitoraggio e aggiornamento** offre un'opzione _Crea un'attività di correzione_ . Se questa opzione è selezionata, viene creata un'attività di monitoraggio e aggiornamento allo stesso tempo dell'assegnazione dei criteri.

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere dati sulla conformità](get-compliance-data.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
