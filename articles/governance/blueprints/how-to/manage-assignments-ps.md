---
title: Come gestire le assegnazioni con PowerShell
description: Informazioni su come gestire le assegnazioni di progetto con il modulo di PowerShell ufficiale di Azure Blueprints, AZ. Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232896"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Come gestire le assegnazioni con PowerShell

Un'assegnazione di progetto può essere gestita usando il modulo **AZ. blueprint** Azure PowerShell. Il modulo supporta il recupero, la creazione, l'aggiornamento e la rimozione delle assegnazioni. Il modulo può anche recuperare i dettagli sulle definizioni esistenti del progetto. Questo articolo illustra come installare il modulo e iniziare a usarlo.

## <a name="add-the-azblueprint-module"></a>Aggiungere il modulo AZ. Blueprint

Per consentire Azure PowerShell di gestire le assegnazioni di progetto, è necessario aggiungere il modulo. Questo modulo può essere usato con PowerShell installato in locale, con [Azure Cloud Shell](https://shell.azure.com) o con l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisiti di base

Il modulo Blueprints di Azure richiede il seguente software:

- Azure PowerShell 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 o versione successiva. Se non è installato o aggiornato, seguire [queste istruzioni](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installare il modulo

Il modulo Blueprints per PowerShell è **AZ. Blueprint**.

1. Da un prompt di PowerShell **amministrativo** eseguire i comandi seguenti:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **AZ. Accounts** è già installato, potrebbe essere necessario usare `-AllowClobber` per forzare l'installazione.

1. Verificare che il modulo sia stato importato e che la versione sia corretta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Ottenere le definizioni del progetto

Il primo passaggio per l'utilizzo di un'assegnazione è spesso il recupero di un riferimento a una definizione di progetto.
Tramite `Get-AzBlueprint` il cmdlet vengono ottenute una o più definizioni di progetto. Il cmdlet può ottenere le definizioni del progetto da un gruppo `-ManagementGroupId {mgId}` di gestione con o `-SubscriptionId {subId}`una sottoscrizione con. Il parametro **Name** ottiene una definizione di progetto, ma deve essere utilizzata con **managementgroupid nelle** o **SubscriptionId**. La **versione** può essere usata con il **nome** per essere più esplicita sulla definizione del progetto da restituire. Invece della **versione**, l'opzione `-LatestPublished` acquisisce la versione pubblicata più di recente.

Nell'esempio seguente viene `Get-AzBlueprint` usato per ottenere tutte le versioni di una definizione di progetto denominata ' 101-Blueprints-Definition-Subscription ' da una `{subId}`sottoscrizione specifica rappresentata come:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

L'output di esempio per una definizione di progetto con più versioni è simile al seguente:

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

È possibile espandere i [parametri del progetto](../concepts/parameters.md#blueprint-parameters) nella definizione del progetto per fornire altre informazioni.

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

Se l'assegnazione del progetto esiste già, è possibile ottenere un riferimento con il `Get-AzBlueprintAssignment` cmdlet. Il cmdlet accetta **SubscriptionId** e il **nome** come parametri facoltativi. Se **SubscriptionId** non è specificato, viene utilizzato il contesto della sottoscrizione corrente.

Nell'esempio seguente viene `Get-AzBlueprintAssignment` usato per ottenere una singola assegnazione di progetto denominata ' assegnazione-blocco-Resource-Groups ' da una sottoscrizione `{subId}`specifica rappresentata come:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

L'output di esempio per l'assegnazione di un progetto è simile al seguente:

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

## <a name="create-blueprint-assignments"></a>Creare assegnazioni di progetto

Se l'assegnazione del progetto non esiste ancora, è possibile crearla con `New-AzBlueprintAssignment` il cmdlet. Questo cmdlet usa i parametri seguenti:

- **Nome** necessaria
  - Specifica il nome dell'assegnazione del progetto
  - Deve essere univoco e non esiste già in **SubscriptionId**
- **Progetto** necessaria
  - Specifica la definizione del progetto da assegnare
  - Usare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
- **Posizione** necessaria
  - Specifica l'area per l'oggetto di distribuzione delle identità gestite e delle sottoscrizioni gestite dal sistema da creare in
- **Sottoscrizione** di opzionale
  - Specifica la sottoscrizione in cui viene distribuita l'assegnazione
  - Se non viene specificato, il valore predefinito è il contesto della sottoscrizione corrente
- **Blocca** opzionale
  - Definisce il [blocco di risorse del progetto](../concepts/resource-locking.md) da usare per le risorse distribuite
  - Opzioni supportate: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se non viene specificato, il valore predefinito è _None_
- **SystemAssignedIdentity** opzionale
  - Selezionare questa finestra per creare un'identità gestita assegnata dal sistema per l'assegnazione e per distribuire le risorse
  - Impostazione predefinita per il set di parametri "Identity"
  - Non può essere usato con **UserAssignedIdentity**
- **UserAssignedIdentity** opzionale
  - Specifica l'identità gestita assegnata dall'utente da usare per l'assegnazione e per distribuire le risorse.
  - Parte del set di parametri "Identity"
  - Non può essere usato con **SystemAssignedIdentity**
- **Parametro** di opzionale
  - [Tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di coppie chiave/valore per l'impostazione di [parametri dinamici](../concepts/parameters.md#dynamic-parameters) nell'assegnazione del progetto
  - Il valore predefinito per un parametro dinamico è **DefaultValue** nella definizione
  - Se un parametro non è specificato e non ha **DefaultValue**, il parametro non è facoltativo

    > [!NOTE]
    > Il **parametro** non supporta le SecureString.

- **ResourceGroupParameter** opzionale
  - [Tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di elementi del gruppo di risorse
  - Ogni segnaposto di elemento del gruppo di risorse avrà una coppia chiave/valore per impostare in modo dinamico il **nome** e/o la **posizione** nell'artefatto del gruppo di risorse
  - Se non viene fornito un parametro del gruppo di risorse e non è presente alcun valore **DefaultValue**, il parametro del gruppo di risorse non è facoltativo

L'esempio seguente crea una nuova assegnazione della versione ' 1,1' della definizione di progetto ' My-Blueprint ' recuperata con `Get-AzBlueprint`, imposta il percorso dell'oggetto di assegnazione e dell'identità gestita su' westus2', blocca le risorse con _AllResourcesReadOnly_ e imposta le tabelle hash per il **parametro** e **ResourceGroupParameter** per una sottoscrizione specifica rappresentata `{subId}`come:

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

L'output di esempio per la creazione di un'assegnazione di progetto ha un aspetto simile al seguente:

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

A volte è necessario aggiornare un'assegnazione di progetto già creata. Tramite `Set-AzBlueprintAssignment` il cmdlet viene gestita questa azione. Il cmdlet accetta la maggior parte degli stessi parametri `New-AzBlueprintAssignment` del cmdlet, consentendo l'aggiornamento di qualsiasi elemento impostato sull'assegnazione. Le eccezioni sono il _nome_, il _progetto_e il _SubscriptionId_. Vengono aggiornati solo i valori specificati.

Per comprendere cosa accade quando si aggiorna un'assegnazione di progetto, vedere [regole per l'aggiornamento delle assegnazioni](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** necessaria
  - Specifica il nome dell'assegnazione del progetto da aggiornare
  - Usato per individuare l'assegnazione da aggiornare, non per modificare l'assegnazione
- **Progetto** necessaria
  - Specifica la definizione del progetto dell'assegnazione del progetto
  - Usare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
  - Usato per individuare l'assegnazione da aggiornare, non per modificare l'assegnazione
- **Posizione** opzionale
  - Specifica l'area per l'oggetto di distribuzione delle identità gestite e delle sottoscrizioni gestite dal sistema da creare in
- **Sottoscrizione** di opzionale
  - Specifica la sottoscrizione in cui viene distribuita l'assegnazione
  - Se non viene specificato, il valore predefinito è il contesto della sottoscrizione corrente
  - Usato per individuare l'assegnazione da aggiornare, non per modificare l'assegnazione
- **Blocca** opzionale
  - Definisce il [blocco di risorse del progetto](../concepts/resource-locking.md) da usare per le risorse distribuite
  - Opzioni supportate: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** opzionale
  - Selezionare questa finestra per creare un'identità gestita assegnata dal sistema per l'assegnazione e per distribuire le risorse
  - Impostazione predefinita per il set di parametri "Identity"
  - Non può essere usato con **UserAssignedIdentity**
- **UserAssignedIdentity** opzionale
  - Specifica l'identità gestita assegnata dall'utente da usare per l'assegnazione e per distribuire le risorse.
  - Parte del set di parametri "Identity"
  - Non può essere usato con **SystemAssignedIdentity**
- **Parametro** di opzionale
  - [Tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di coppie chiave/valore per l'impostazione di [parametri dinamici](../concepts/parameters.md#dynamic-parameters) nell'assegnazione del progetto
  - Il valore predefinito per un parametro dinamico è **DefaultValue** nella definizione
  - Se un parametro non è specificato e non ha **DefaultValue**, il parametro non è facoltativo

    > [!NOTE]
    > Il **parametro** non supporta le SecureString.

- **ResourceGroupParameter** opzionale
  - [Tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) di elementi del gruppo di risorse
  - Ogni segnaposto di elemento del gruppo di risorse avrà una coppia chiave/valore per impostare in modo dinamico il **nome** e/o la **posizione** nell'artefatto del gruppo di risorse
  - Se non viene fornito un parametro del gruppo di risorse e non è presente alcun valore **DefaultValue**, il parametro del gruppo di risorse non è facoltativo

Nell'esempio seguente viene aggiornata l'assegnazione della versione ' 1,1' della definizione di progetto ' My-Blueprint ' recuperata `Get-AzBlueprint` con modificando la modalità di blocco:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

L'output di esempio per la creazione di un'assegnazione di progetto ha un aspetto simile al seguente:

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

Quando è il momento di rimuovere l'assegnazione di un progetto, il `Remove-AzBlueprintAssignment` cmdlet gestisce questa azione. Il cmdlet prende il **nome** o **InputObject** per specificare quale assegnazione di progetto rimuovere. **SubscriptionId** è _obbligatorio_ e deve essere specificato in tutti i casi.

Nell'esempio seguente viene recuperata un'assegnazione di progetto `Get-AzBlueprintAssignment` esistente con e quindi viene rimossa dalla sottoscrizione specifica `{subId}`rappresentata come:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Esempio di codice end-to-end

Unendo tutti i passaggi, nell'esempio seguente viene ottenuta la definizione del progetto, quindi viene creata, aggiornata e rimossa un'assegnazione di progetto nella sottoscrizione `{subId}`specifica rappresentata come:

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