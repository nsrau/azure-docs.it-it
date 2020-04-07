---
title: Come gestire le assegnazioni con PowerShellHow to manage assignments with PowerShell
description: Informazioni su come gestire le assegnazioni dei blueprint con il modulo ufficiale di Azure Blueprints PowerShell, Az.Blueprint.Learn how to manage blueprint assignments with the official Azure Blueprints PowerShell module, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677434"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Come gestire le assegnazioni con PowerShellHow to manage assignments with PowerShell

Un'assegnazione di blueprint può essere gestita tramite il modulo Az.Blueprint Azure PowerShell.A blueprint assignment can be managed using the **Az.Blueprint** Azure PowerShell module. Il modulo supporta il recupero, la creazione, l'aggiornamento e la rimozione delle assegnazioni. Il modulo può anche recuperare i dettagli sulle definizioni di blueprint esistenti. In questo articolo viene illustrato come installare il modulo e iniziare a utilizzarlo.

## <a name="add-the-azblueprint-module"></a>Aggiungere il modulo Az.Blueprint

Per consentire ad Azure PowerShell di gestire le assegnazioni del blueprint, è necessario aggiungere il modulo. Questo modulo può essere usato con PowerShell installato in locale, con [Azure Cloud Shell](https://shell.azure.com) o con l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisiti di base

Il modulo Azure Blueprints richiede il software seguente:The Azure Blueprints module requires the following software:

- Azure PowerShell 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 o versione successiva. Se non è installato o aggiornato, seguire [queste istruzioni](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Installare il modulo

Il modulo Azure Blueprints per PowerShell è **Az.Blueprint**.

1. Da un prompt di PowerShell **amministrativo** eseguire i comandi seguenti:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **Az.Accounts** è già installato, potrebbe `-AllowClobber` essere necessario utilizzare per forzare l'installazione.

1. Verificare che il modulo sia stato importato ed è la versione corretta (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Ottenere le definizioni dei blueprintGet blueprint definitions

Il primo passaggio per lavorare con un'assegnazione è spesso ottenere un riferimento a una definizione di blueprint.
Il `Get-AzBlueprint` cmdlet ottiene una o più definizioni di blueprint. Il cmdlet può ottenere le definizioni del blueprint da un gruppo di gestione con `-ManagementGroupId {mgId}` o una sottoscrizione con `-SubscriptionId {subId}`. Il parametro **Name** ottiene una definizione del blueprint, ma deve essere utilizzato con **ManagementGroupId** o **SubscriptionId**. **La versione** può essere utilizzata con **Name** per essere più esplicita sulla definizione del blueprint restituita. Invece **Version**di Versione `-LatestPublished` , l'interruttore acquisisce la versione pubblicata più di recente.

L'esempio `Get-AzBlueprint` seguente viene utilizzato per ottenere tutte le versioni di una definizione di blueprint `{subId}`denominata '101-blueprints-definition-subscription' da una sottoscrizione specifica rappresentata come:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

L'output di esempio per una definizione di blueprint con più versioni è simile al seguente:The example output for a blueprint definition with multiple versions looks like this:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

I [parametri del blueprint](../concepts/parameters.md#blueprint-parameters) nella definizione del blueprint possono essere espansi per fornire ulteriori informazioni.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Ottenere le assegnazioni del blueprintGet blueprint assignments

Se l'assegnazione del blueprint esiste già, `Get-AzBlueprintAssignment` è possibile ottenere un riferimento ad essa con il cmdlet. Il cmdlet accetta **SubscriptionId** e **Name** come parametri facoltativi. Se **SubscriptionId** non è specificato, viene utilizzato il contesto di sottoscrizione corrente.

L'esempio `Get-AzBlueprintAssignment` seguente viene utilizzato per ottenere una singola assegnazione di blueprint denominata "Assignment-lock-resource-groups" da una sottoscrizione specifica rappresentata come: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

L'output di esempio per un'assegnazione di blueprint è simile al seguente:The example output for a blueprint assignment looks like this:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Creare assegnazioni di blueprint

Se l'assegnazione del blueprint non esiste ancora, è possibile crearla con il `New-AzBlueprintAssignment` cmdlet. Questo cmdlet utilizza i parametri seguenti:

- **Nome** [obbligatorio]
  - Specifica il nome dell'assegnazione del blueprint
  - Deve essere univoco e non esiste già in **SubscriptionId**
- **Blueprint** [obbligatorio]
  - Specifica la definizione del blueprint da assegnare
  - Utilizzare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
- **Ubicazione** [obbligatorio]
  - Specifica l'area per l'identità gestita assegnata dal sistema e l'oggetto di distribuzione della sottoscrizione da creare in
- **Abbonamento** (opzionale)
  - Specifica la sottoscrizione a cui viene distribuita l'assegnazione
  - Se non viene specificato, il valore predefinito è il contesto di sottoscrizione correnteIf not provided, defaults to the current subscription context
- **Blocco** (opzionale)
  - Definisce il [blocco delle risorse](../concepts/resource-locking.md) del blueprint da utilizzare per le risorse distribuite
  - Opzioni _supportate: None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se non viene specificato, il valore predefinito è _Nessuno_
- **SystemAssignedIdentity** (facoltativo)
  - Selezionare questa opzione per creare un'identità gestita assegnata dal sistema per l'assegnazione e distribuire le risorse
  - Impostazione predefinita per il set di parametri "identity"
  - Non può essere utilizzato con **UserAssignedIdentity**
- **UserAssignedIdentity (facoltativo)UserAssignedIdentity** (optional)
  - Specifica l'identità gestita assegnata dall'utente da utilizzare per l'assegnazione e per distribuire le risorse
  - Parte del set di parametri "identità"
  - Non può essere utilizzato con **SystemAssignedIdentity**
- **Parametro** (facoltativo)
  - Una [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di coppie chiave/valore per l'impostazione di parametri [dinamici](../concepts/parameters.md#dynamic-parameters) nell'assegnazione del blueprint
  - Il valore predefinito per un parametro dinamico è defaultValue nella definizioneDefault for a dynamic parameter is the **defaultValue** in the definition
  - Se un parametro non viene fornito e non ha **defaultValue**, il parametro non è facoltativo

    > [!NOTE]
    > **Il parametro** non supporta secureStrings.

- **ResourceGroupParameter (facoltativo)ResourceGroupParameter** (optional)
  - Una [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) degli elementi del gruppo di risorse
  - Ogni segnaposto elemento del gruppo di risorse ha coppie chiave/valore per l'impostazione dinamica **di nome** e posizione su tale elemento del gruppo di risorseEach resource group artifact placeholder has key/value pairs for dynamically setting Name and **Location** on that resource group artifact
  - Se non viene fornito un parametro del gruppo di risorse e non è **presente defaultValue**, il parametro del gruppo di risorse non è facoltativo
- **AssignmentFile** (facoltativo)
  - Percorso di una rappresentazione di file JSON di un'assegnazione di blueprint
  - Questo parametro fa parte di un set di parametri di PowerShell che include solo **Name**, **Blueprint**e **SubscriptionId**, oltre ai parametri comuni.

### <a name="example-1-provide-parameters"></a>Esempio 1: Fornire parametriExample 1: Provide parameters

Nell'esempio seguente viene creata una nuova assegnazione della versione '1.1' `Get-AzBlueprint`della definizione del blueprint 'my-blueprint' recuperata con , imposta l'identità gestita e la posizione dell'oggetto `{subId}`di assegnazione su 'westus2', blocca le risorse con _AllResourcesReadOnly_e imposta le tabelle hash per **Parameter** e **ResourceGroupParameter** su una sottoscrizione specifica rappresentata come :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

L'output di esempio per la creazione di un'assegnazione di blueprint è simile al seguente:The example output for creating a blueprint assignment looks like this:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Esempio 2: Usare un file di definizione delle assegnazioni JSONExample 2: Use a JSON assignment definition file

Nell'esempio seguente viene creata quasi la stessa assegnazione [dell'esempio 1.](#example-1-provide-parameters)
Anziché passare parametri al cmdlet, nell'esempio viene illustrato l'utilizzo di un file di definizione delle assegnazioni JSON e del parametro **AssignmentFile.** Inoltre, la proprietà **excludedPrincipals** viene configurata come parte dei **blocchi**. Non esiste un parametro di PowerShell per **excludedPrincipals** e la proprietà può essere configurata solo impostandolo tramite il file di definizione dell'assegnazione JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Per un esempio del file di definizione delle assegnazioni JSON per un'identità gestita assegnata dall'utente, vedere il corpo della richiesta in [Esempio: assegnazione con identità gestita assegnata dall'utente](/rest/api/blueprints/assignments/createorupdate#examples) per l'API REST.

## <a name="update-blueprint-assignments"></a>Aggiornare le assegnazioni dei blueprint

A volte è necessario aggiornare un'assegnazione del blueprint che è già stata creata. Il `Set-AzBlueprintAssignment` cmdlet gestisce questa azione. Il cmdlet accetta la maggior `New-AzBlueprintAssignment` parte degli stessi parametri del cmdlet, consentendo l'aggiornamento di tutto ciò che è stato impostato sull'assegnazione. Le eccezioni sono _Nome_, _Blueprint_e _SubscriptionId_. Vengono aggiornati solo i valori forniti.

Per comprendere cosa accade durante l'aggiornamento di un'assegnazione di blueprint, vedere [Regole per l'aggiornamento delle assegnazioni](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** [obbligatorio]
  - Specifica il nome dell'assegnazione del blueprint da aggiornare
  - Utilizzato per individuare l'assegnazione da aggiornare, non per modificare l'assegnazione
- **Blueprint** [obbligatorio]
  - Specifica la definizione del blueprint dell'assegnazione del blueprint
  - Utilizzare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
  - Utilizzato per individuare l'assegnazione da aggiornare, non per modificare l'assegnazione
- **Posizione** (opzionale)
  - Specifica l'area per l'identità gestita assegnata dal sistema e l'oggetto di distribuzione della sottoscrizione da creare in
- **Abbonamento** (opzionale)
  - Specifica la sottoscrizione a cui viene distribuita l'assegnazione
  - Se non viene specificato, il valore predefinito è il contesto di sottoscrizione correnteIf not provided, defaults to the current subscription context
  - Utilizzato per individuare l'assegnazione da aggiornare, non per modificare l'assegnazione
- **Blocco** (opzionale)
  - Definisce il [blocco delle risorse](../concepts/resource-locking.md) del blueprint da utilizzare per le risorse distribuite
  - Opzioni _supportate: None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (facoltativo)
  - Selezionare questa opzione per creare un'identità gestita assegnata dal sistema per l'assegnazione e distribuire le risorse
  - Impostazione predefinita per il set di parametri "identity"
  - Non può essere utilizzato con **UserAssignedIdentity**
- **UserAssignedIdentity (facoltativo)UserAssignedIdentity** (optional)
  - Specifica l'identità gestita assegnata dall'utente da utilizzare per l'assegnazione e per distribuire le risorse
  - Parte del set di parametri "identità"
  - Non può essere utilizzato con **SystemAssignedIdentity**
- **Parametro** (facoltativo)
  - Una [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di coppie chiave/valore per l'impostazione di parametri [dinamici](../concepts/parameters.md#dynamic-parameters) nell'assegnazione del blueprint
  - Il valore predefinito per un parametro dinamico è defaultValue nella definizioneDefault for a dynamic parameter is the **defaultValue** in the definition
  - Se un parametro non viene fornito e non ha **defaultValue**, il parametro non è facoltativo

    > [!NOTE]
    > **Il parametro** non supporta secureStrings.

- **ResourceGroupParameter (facoltativo)ResourceGroupParameter** (optional)
  - Una [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) degli elementi del gruppo di risorse
  - Ogni segnaposto elemento del gruppo di risorse ha coppie chiave/valore per l'impostazione dinamica **di nome** e posizione su tale elemento del gruppo di risorseEach resource group artifact placeholder has key/value pairs for dynamically setting Name and **Location** on that resource group artifact
  - Se non viene fornito un parametro del gruppo di risorse e non è **presente defaultValue**, il parametro del gruppo di risorse non è facoltativo

L'esempio seguente aggiorna l'assegnazione della versione '1.1' della `Get-AzBlueprint` definizione del blueprint "my-blueprint" recuperata modificando la modalità di blocco:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

L'output di esempio per la creazione di un'assegnazione di blueprint è simile al seguente:The example output for creating a blueprint assignment looks like this:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Rimuovere le assegnazioni del blueprint

Quando è il momento per un'assegnazione `Remove-AzBlueprintAssignment` di blueprint da rimuovere, il cmdlet gestisce questa azione. Il cmdlet accetta **Name** o **InputObject** per specificare l'assegnazione del blueprint da rimuovere. **SubscriptionId** è _obbligatorio_ e deve essere fornito in tutti i casi.

L'esempio seguente recupera un'assegnazione di blueprint esistente con `Get-AzBlueprintAssignment` `{subId}`e quindi la rimuove dalla sottoscrizione specifica rappresentata come:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Esempio di codice end-to-end

Riunendo tutti i passaggi, nell'esempio seguente viene ottenuta la definizione del `{subId}`blueprint, quindi viene creata, aggiornata e rimossa un'assegnazione del blueprint nella sottoscrizione specifica rappresentata come:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul ciclo di vita del [blueprint.](../concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).