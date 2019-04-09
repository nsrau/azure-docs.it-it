---
title: Come gestire le assegnazioni con PowerShell
description: Informazioni su come gestire le assegnazioni di progetto con il modulo PowerShell di progetti di architetture di Azure ufficiale, Az.Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256873"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Come gestire le assegnazioni con PowerShell

Un'assegnazione di progetto può essere gestita tramite il **Az.Blueprint** modulo Azure PowerShell. Il modulo supporta il recupero, creazione, l'aggiornamento e rimozione di assegnazioni. Il modulo può anche recuperare i dettagli su definizioni di progetto esistente. Questo articolo illustra come installare il modulo e iniziare a usarlo.

## <a name="add-the-azblueprint-module"></a>Aggiungere il modulo Az.Blueprint

Per abilitare Azure PowerShell per gestire le assegnazioni di progetto, è necessario aggiungere il modulo. Questo modulo può essere usato con PowerShell installato in locale, con [Azure Cloud Shell](https://shell.azure.com) o con l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisiti di base

Il modulo di progetti di architetture di Azure richiede il seguente software:

- Azure PowerShell versione 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 o versione successiva. Se non è installato o aggiornato, seguire [queste istruzioni](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installare il modulo

Il modulo di linee guida per PowerShell **Az.Blueprint**.

1. Da un prompt di PowerShell **amministrativo** eseguire i comandi seguenti:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **Az.Accounts** è già installato, potrebbe essere necessario usare `-AllowClobber` per forzare l'installazione.

1. Verificare che il modulo sia stato importato e che la versione sia corretta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Ottieni definizioni di progetto

Il primo passaggio per l'utilizzo di un'assegnazione è spesso di ottenere un riferimento a una definizione di progetto.
Il `Get-AzBlueprint` cmdlet recupera uno o più definizioni di progetto. Il cmdlet è possibile ottenere definizioni di progetto da un gruppo di gestione `-ManagementGroupId {mgId}` o una sottoscrizione con `-SubscriptionId {subId}`. Il **Name** parametro ottiene una definizione di progetto, ma deve essere utilizzato con **ManagementGroupId** oppure **SubscriptionId**. **Versione** può essere usato con **nome** per essere più esplicito viene restituita la definizione di progetto. Invece di **versione**, il commutatore `-LatestPublished` grabs pubblicato più recente versione.

L'esempio seguente usa `Get-AzBlueprint` per ottenere tutte le versioni di una definizione di progetto denominato "101-progetti-definition-subscription' da una sottoscrizione specifica rappresentata come `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

L'output di esempio per una definizione di progetto con le versioni più simile alla seguente:

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

Il [blueprint parametri](../concepts/parameters.md#blueprint-parameters) sulle linee guida per la definizione può essere espanso per fornire altre informazioni.

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

## <a name="get-blueprint-assignments"></a>Ottenere le assegnazioni di progetto

Se il progetto assegnazione esiste già, è possibile ottenere un riferimento a esso con il `Get-AzBlueprintAssignment` cmdlet. Il cmdlet accetta **SubscriptionId** e **nome** come parametri facoltativi. Se **SubscriptionId** viene omesso, viene usato il contesto della sottoscrizione corrente.

L'esempio seguente usa `Get-AzBlueprintAssignment` per ottenere un'assegnazione di singolo progetto denominata 'Assegnazione-lock-resource-groups' da una sottoscrizione specifica rappresentata come `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

L'output di esempio per un'assegnazione progetto è simile alla seguente:

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

## <a name="create-blueprint-assignments"></a>Creare assegnazioni di progetti

Se l'assegnazione di progetto non esiste ancora, è possibile crearlo con il `New-AzBlueprintAssignment` cmdlet. Questo cmdlet Usa i parametri seguenti:

- **Nome** [obbligatorio]
  - Specifica il nome dell'assegnazione progetto
  - Deve essere univoco e non esiste in **SubscriptionId**
- **Progetto** [obbligatorio]
  - Specifica la definizione di progetto da assegnare
  - Usare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
- **Percorso** [obbligatorio]
  - Specifica l'area per assegnato dal sistema identità e la sottoscrizione distribuzione oggetto gestito deve essere creato in
- **Sottoscrizione** (facoltativo)
  - Specifica la sottoscrizione che viene distribuito l'assegnazione
  - Se non specificato, per impostazione predefinita il contesto della sottoscrizione corrente
- **Blocco** (facoltativo)
  - Definisce i [blueprint blocco delle risorse](../concepts/resource-locking.md) da utilizzare per le risorse distribuite
  - Opzioni supportate: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se non specificato, per impostazione predefinita _None_
- **SystemAssignedIdentity** (optional)
  - Selezionare per creare un'identità gestita assegnato dal sistema per l'assegnazione e distribuire le risorse
  - Valore predefinito per il set di parametri "identity"
  - Non può essere usato con **UserAssignedIdentity**
- **UserAssignedIdentity** (optional)
  - Specifica l'identità gestita con codifica assegnata dall'utente da utilizzare per l'assegnazione e distribuire le risorse
  - Parte del set di parametri "identity"
  - Non può essere usato con **SystemAssignedIdentity**
- **Parametro** (facoltativo)
  - Oggetto [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di coppie chiave/valore per l'impostazione [i parametri dinamici](../concepts/parameters.md#dynamic-parameters) dell'assegnazione progetto
  - Impostazione predefinita per un parametro dinamico è il **defaultValue** nella definizione
  - Se un parametro non viene fornito e non ha alcun **defaultValue**, il parametro non è facoltativo

    > [!NOTE]
    > **Parametro** secureStrings nepodporuje funkci.

- **ResourceGroupParameter** (facoltativo)
  - Oggetto [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) degli elementi di gruppo di risorse
  - Ogni segnaposto dell'elemento gruppo di risorse avranno un coppie chiave/valore per l'impostazione in modo dinamico **Name** e/o **posizione** su tale elemento di gruppo di risorse
  - Se il parametro di un gruppo di risorse non è specificato e non ha alcun **defaultValue**, il parametro del gruppo di risorse non è facoltativo

L'esempio seguente crea una nuova assegnazione della versione "1.1" della definizione di progetto 'my blueprint' recuperata mediante `Get-AzBlueprint`, Imposta percorso di oggetto gestito di identità e l'assegnazione a 'westus2', che consente di bloccare le risorse con  _AllResourcesReadOnly_e i set di tabelle hash per entrambe **parametro** e **ResourceGroupParameter** di una sottoscrizione specifica rappresentata come `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

L'output di esempio per la creazione di un'assegnazione progetto è simile alla seguente:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Aggiornare le assegnazioni di progetto

In alcuni casi è necessario aggiornare un'assegnazione di progetto che è già stata creata. Il `Set-AzBlueprintAssignment` cmdlet gestisce questa azione. Il cmdlet accetta la maggior parte degli stessi parametri che il `New-AzBlueprintAssignment` eseguita dal cmdlet, che consente tutto ciò che è stata impostata per l'assegnazione da aggiornare. Le eccezioni sono le _Name_, _Blueprint_, e _SubscriptionId_. Vengono aggiornati solo i valori specificati.

Per capire cosa accade quando si aggiorna un'assegnazione di progetto, vedere [regole per l'aggiornamento delle assegnazioni](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** [obbligatorio]
  - Specifica il nome dell'assegnazione progetto da aggiornare
  - Consente di individuare l'assegnazione di aggiornamento, non per modificare l'assegnazione
- **Progetto** [obbligatorio]
  - Specifica la definizione di progetto dell'assegnazione progetto
  - Usare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
  - Consente di individuare l'assegnazione di aggiornamento, non per modificare l'assegnazione
- **Percorso** (facoltativo)
  - Specifica l'area per assegnato dal sistema identità e la sottoscrizione distribuzione oggetto gestito deve essere creato in
- **Sottoscrizione** (facoltativo)
  - Specifica la sottoscrizione che viene distribuito l'assegnazione
  - Se non specificato, per impostazione predefinita il contesto della sottoscrizione corrente
  - Consente di individuare l'assegnazione di aggiornamento, non per modificare l'assegnazione
- **Blocco** (facoltativo)
  - Definisce i [blueprint blocco delle risorse](../concepts/resource-locking.md) da utilizzare per le risorse distribuite
  - Opzioni supportate: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (optional)
  - Selezionare per creare un'identità gestita assegnato dal sistema per l'assegnazione e distribuire le risorse
  - Valore predefinito per il set di parametri "identity"
  - Non può essere usato con **UserAssignedIdentity**
- **UserAssignedIdentity** (optional)
  - Specifica l'identità gestita con codifica assegnata dall'utente da utilizzare per l'assegnazione e distribuire le risorse
  - Parte del set di parametri "identity"
  - Non può essere usato con **SystemAssignedIdentity**
- **Parametro** (facoltativo)
  - Oggetto [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di coppie chiave/valore per l'impostazione [i parametri dinamici](../concepts/parameters.md#dynamic-parameters) dell'assegnazione progetto
  - Impostazione predefinita per un parametro dinamico è il **defaultValue** nella definizione
  - Se un parametro non viene fornito e non ha alcun **defaultValue**, il parametro non è facoltativo

    > [!NOTE]
    > **Parametro** secureStrings nepodporuje funkci.

- **ResourceGroupParameter** (facoltativo)
  - Oggetto [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) degli elementi di gruppo di risorse
  - Ogni segnaposto dell'elemento gruppo di risorse avranno un coppie chiave/valore per l'impostazione in modo dinamico **Name** e/o **posizione** su tale elemento di gruppo di risorse
  - Se il parametro di un gruppo di risorse non è specificato e non ha alcun **defaultValue**, il parametro del gruppo di risorse non è facoltativo

L'esempio seguente aggiorna l'assegnazione della versione "1.1" della definizione di progetto 'my blueprint' recuperata mediante `Get-AzBlueprint` modificando la modalità di blocco:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

L'output di esempio per la creazione di un'assegnazione progetto è simile alla seguente:

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

## <a name="remove-blueprint-assignments"></a>Rimuovere le assegnazioni di progetto

Quando si è pronti per un'assegnazione di progetto da rimuovere, il `Remove-AzBlueprintAssignment` cmdlet gestisce questa azione. Il cmdlet accetta uno **Name** oppure **InputObject** per specificare quali blueprint assegnazione da rimuovere. **SubscriptionId** viene _obbligatorio_ e deve essere specificata in tutti i casi.

Nell'esempio seguente recupera un'assegnazione di progetto esistente con `Get-AzBlueprintAssignment` e quindi lo rimuove dalla sottoscrizione specifica rappresentata come `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Esempio di codice end-to-end

Riunire tutti i passaggi, l'esempio seguente ottiene la definizione di progetto, quindi crea, Aggiorna e rimuove un'assegnazione di progetto nella sottoscrizione specifica rappresentata come `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
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

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).