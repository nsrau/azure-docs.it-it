---
title: Correggere le risorse non conformi
description: Questa guida illustra la correzione di risorse non conformi ai criteri in criteri di Azure.
ms.date: 09/09/2019
ms.topic: conceptual
ms.openlocfilehash: 53ca21e4b8a1f3e7973706acd10601593efc3448
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959493"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Correggere le risorse non conformi con Criteri di Azure

Le risorse non conformi a un criterio di **deployIfNotExists** o di **modifica** possono essere inserite in uno stato conforme tramite **correzione**. La correzione viene eseguita indicando a criteri di Azure di eseguire l'effetto **deployIfNotExists** o le **operazioni** di tag del criterio assegnato nelle risorse esistenti. Questo articolo illustra i passaggi necessari per comprendere ed eseguire la correzione con criteri di Azure.

## <a name="how-remediation-security-works"></a>Funzionamento della sicurezza della correzione

Quando i criteri di Azure eseguono il modello nella definizione dei criteri **deployIfNotExists** , viene usata un' [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md).
Criteri di Azure crea un'identità gestita per ogni assegnazione, ma deve avere informazioni dettagliate sui ruoli da concedere all'identità gestita. Se all'identità gestita non sono assegnati ruoli, viene visualizzato questo errore durante l'assegnazione dei criteri o un'iniziativa. Quando si usa il portale, i criteri di Azure concedono automaticamente all'identità gestita i ruoli elencati dopo l'avvio dell'assegnazione.

![Entità gestita - ruolo mancante](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se una risorsa modificata da **deployIfNotExists** o **Modify** esula dall'ambito dell'assegnazione di criteri oppure il modello accede alle proprietà delle risorse al di fuori dell'ambito dell'assegnazione dei criteri, l'identità gestita dell'assegnazione deve essere [concessa manualmente](#manually-configure-the-managed-identity) , altrimenti la distribuzione di monitoraggio e aggiornamento avrà esito negativo.

## <a name="configure-policy-definition"></a>Configurare la definizione dei criteri

Il primo passaggio consiste nel definire i ruoli che **deployIfNotExists** e **modificare** le esigenze nella definizione dei criteri per distribuire correttamente il contenuto del modello incluso. Nella proprietà **details** aggiungere una proprietà **roleDefinitionIds**. Questa proprietà è una matrice di stringhe che corrispondono ai ruoli nell'ambiente in uso. Per un esempio completo, vedere l' [esempio deployIfNotExists](../concepts/effects.md#deployifnotexists-example) o gli [esempi di modifica](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

La proprietà **roleDefinitionIds** usa l'identificatore di risorsa completo e non accetta il **ruolo** ShortName del ruolo. Per ottenere l'ID per il ruolo 'Collaboratore' nell'ambiente in uso, usare il codice seguente:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurare manualmente l'identità gestita

Quando si crea un'assegnazione usando il portale, i criteri di Azure generano l'identità gestita e le assegnano i ruoli definiti in **roleDefinitionIds**. Nelle condizioni seguenti è necessario eseguire manualmente i passaggi per creare l'identità gestita e assegnarle le autorizzazioni:

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

1. Nella definizione dei criteri selezionare il ruolo appropriato che corrisponde a un **roleDefinitionIds**.
   Lasciare **Assegna accesso a** impostato sul valore predefinito di 'utente, gruppo o applicazione di Azure AD'. Nella casella **Seleziona** incollare o digitare la parte dell'ID di risorsa di assegnazione individuata precedentemente. Al termine della ricerca fare clic sull'oggetto con lo stesso nome per selezionare l'ID e fare clic su **Salva**.

## <a name="create-a-remediation-task"></a>Creare un'attività di correzione

### <a name="create-a-remediation-task-through-portal"></a>Creare un'attività di correzione tramite il portale

Durante la valutazione, l'assegnazione dei criteri con **deployIfNotExists** o **modifica** effetti determina se sono presenti risorse non conformi. Quando vengono rilevate risorse non conformi, vengono visualizzati i dettagli nella pagina **Correzione**. Insieme all'elenco di criteri con risorse non conformi è disponibile l'opzione per attivare un'**attività di correzione**. Questa opzione consente di creare una distribuzione dal modello **deployIfNotExists** o dalle operazioni di **modifica** .

Per creare un'**attività di correzione**, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

   ![Cercare Criteri di Azure in Tutti i servizi](../media/remediate-resources/search-policy.png)

1. Nella parte sinistra della pagina di Criteri di Azure selezionare **Correzione**.

   ![Seleziona monitoraggio e aggiornamento nella pagina Criteri](../media/remediate-resources/select-remediation.png)

1. Tutti i **deployIfNotExists** e le assegnazioni **di criteri con** risorse non conformi sono inclusi nei **criteri per correggere** la scheda e la tabella dati. Fare clic sui criteri con risorse non conformi. Verrà visualizzata la pagina **Nuova attività di correzione**.

   > [!NOTE]
   > Un modo alternativo per aprire la pagina dell'**attività di correzione** consiste nell'individuare e selezionare i criteri nella pagina **Conformità** e quindi fare clic sul pulsante **Crea attività di correzione**.

1. Nella pagina **Nuova attività di correzione** filtrare le risorse da correggere facendo clic sui puntini di sospensione di **Ambito** per selezionare le risorse figlio da cui sono stati assegnati i criteri (fino ai singoli oggetti risorsa). Usare inoltre il menu a discesa **Percorsi** per filtrare ulteriormente le risorse. Verranno corrette solo le risorse elencate nella tabella.

   ![Correzione: selezionare le risorse da correggere](../media/remediate-resources/select-resources.png)

1. Avviare l'attività di correzione dopo che le risorse sono state filtrate facendo clic su **Correggi**. Verrà aperta la scheda **Attività di correzione** della pagina di conformità dei criteri per mostrare lo stato di avanzamento delle attività.

   ![Correzione-stato delle attività di correzione](../media/remediate-resources/task-progress.png)

1. Fare clic sull'**attività di correzione** nella pagina di conformità dei criteri per visualizzare i dettagli sull'avanzamento. Il filtro usato per l'attività viene visualizzato insieme a un elenco delle risorse da correggere.

1. Nella pagina dell'**attività di correzione** fare clic con il pulsante destro del mouse su una risorsa per visualizzare la distribuzione dell'attività di correzione o la risorsa. Alla fine della riga fare clic su **Eventi correlati** per visualizzare i dettagli, ad esempio un messaggio di errore.

   ![Correggere - menu di scelta rapida delle attività della risorsa](../media/remediate-resources/resource-task-context-menu.png)

Le risorse distribuite tramite un'**attività di correzione** vengono aggiunte nella scheda **Risorse distribuite** della pagina di conformità dei criteri.

### <a name="create-a-remediation-task-through-azure-cli"></a>Creare un'attività di correzione tramite l'interfaccia della riga di comando di Azure

Per creare un' **attività di correzione** con l'interfaccia della riga di comando di Azure, usare i comandi `az policy remediation`. Sostituire `{subscriptionId}` con l'ID sottoscrizione e `{myAssignmentId}` con il **deployIfNotExists** o **modificare** l'ID di assegnazione dei criteri.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Per altri comandi ed esempi di monitoraggio e aggiornamento, vedere i comandi [AZ Policy correttive](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Creare un'attività di correzione tramite Azure PowerShell

Per creare un' **attività di correzione** con Azure PowerShell, usare i comandi di `Start-AzPolicyRemediation`. Sostituire `{subscriptionId}` con l'ID sottoscrizione e `{myAssignmentId}` con il **deployIfNotExists** o **modificare** l'ID di assegnazione dei criteri.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Per altri cmdlet ed esempi di monitoraggio e aggiornamento, vedere il modulo [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](getting-compliance-data.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).