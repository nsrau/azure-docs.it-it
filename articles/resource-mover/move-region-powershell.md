---
title: Spostare le risorse tra aree usando PowerShell in Azure Resource Mover
description: Informazioni su come spostare le risorse tra aree usando PowerShell in Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89670543"
---
# <a name="move-resources-across-regions-in-powershell"></a>Spostare le risorse tra le aree in PowerShell

Informazioni su come spostare le risorse di Azure in un'area diversa usando PowerShell in Azure Resource Mover. 

> [!NOTE]
> Spostamento risorse di Azure è attualmente disponibile in anteprima.



## <a name="before-you-start"></a>Prima di iniziare

- La sottoscrizione di Azure deve avere accesso a Resource Mover ed è necessario disporre delle autorizzazioni di [amministratore di accesso utente](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) o [proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) per la sottoscrizione.
- Il motore di risorse non tiene traccia delle modifiche e degli aggiornamenti, quindi apportare le modifiche necessarie alle risorse prima di iniziare a spostarli.
- Quando si spostano le risorse con PowerShell, non è attualmente possibile modificare le impostazioni dell'area di destinazione. Modificare queste impostazioni direttamente nel portale.
- Quando si aggiungono risorse a una raccolta di spostamento, in preparazione per spostarle in un'altra area, i metadati relativi allo spostamento vengono archiviati in un gruppo di risorse creato per lo scopo. Attualmente questo gruppo di risorse può trovarsi nelle aree Stati Uniti orientali 2 o Europa settentrionale. Le risorse di Azure possono essere spostate tra le aree pubbliche usando i metadati presenti in una di queste aree.

## <a name="sample-values"></a>Valori di esempio

Questi valori vengono utilizzati negli esempi di script:

**Impostazione** | **Valore** 
--- | ---
ID sottoscrizione | subscription-id
Posizione del servizio del motore di risorse | Stati Uniti orientali 2
Gruppo di risorse dei metadati | RegionMoveRG-centralus-westcentralus
Percorso del gruppo di risorse dei metadati | Stati Uniti orientali 2
Sposta nome raccolta | MoveCollection-centralus-westcentralus
Area di origine |  centralus
Gruppo di risorse di origine | PSDemoRM
Area di destinazione | westcentralus
Gruppo di risorse di destinazione | PSDemoRMtgt
VM da spostare | PSDemoVM

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Creare il gruppo di risorse per i metadati

Creare un gruppo di risorse che contenga i metadati della raccolta di spostamento e le informazioni di configurazione.

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Output previsto**:

![Testo di output dopo la creazione del gruppo di risorse](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Registrare il provider di risorse Microsoft. migrate, in modo che sia possibile creare la risorsa MoveCollection.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Creare la raccolta di spostamento

L'oggetto MoveCollection archivia i metadati e le informazioni di configurazione sulle risorse che si desidera spostare.

- Per l'accesso dell'oggetto MoveCollection alla sottoscrizione in cui si trova il servizio Azure Resource Mover, è necessaria un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) , in precedenza denominata identità del servizio gestito (MSI), considerata attendibile dalla sottoscrizione.
- All'identità viene assegnato il ruolo Collaboratore o amministratore accesso utenti per la sottoscrizione di origine.
- Per assegnare un ruolo all'identità, è necessario un ruolo nella sottoscrizione, ad esempio proprietario o amministratore accesso utenti, con le autorizzazioni seguenti:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Output previsto**:

![Testo di output dopo la creazione della raccolta di spostamento](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Assegnare un'identità gestita 

Impostare l'ID sottoscrizione, recuperare l'entità Identity dell'oggetto MoveCollection e assegnarvi i ruoli di Azure.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>Aggiungere risorse alla raccolta di spostamento

Recuperare l'ID della risorsa di origine che si desidera spostare. Quindi aggiungerlo alla raccolta Move.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Output previsto**

![Testo di output dopo il recupero dell'ID risorsa](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Output previsto** 
 ![ Testo di output dopo l'aggiunta della risorsa](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

Convalidare le risorse aggiunte e risolvere eventuali dipendenze da altre risorse.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Output se esistono dipendenze**

Se la risorsa nella raccolta di spostamento presenta dipendenze da altre risorse, viene emesso un messaggio non riuscito.

![Testo di output dopo il controllo delle dipendenze](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Recuperare le dipendenze

Se la risorsa che si desidera spostare presenta dipendenze da altre risorse, è possibile recuperare le informazioni sulle dipendenze mancanti.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Output previsto**

![Testo di output dopo il recupero delle dipendenze](./media/move-region-powershell/missing-dependencies.png)

In questo esempio, due dipendenze vengono identificate come mancanti:

- Gruppo di risorse di origine: PSDemoRM
- SCHEDA di interfaccia di rete nella macchina virtuale: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Aggiungi dipendenze alla raccolta


Utilizzando gli ID risorsa recuperati, identificare i nomi delle risorse mancanti e aggiungerli alla raccolta di spostamento.

### <a name="add-the-nic"></a>Aggiungere la scheda di interfaccia di rete

Recuperare il nome e il tipo della scheda di interfaccia di rete e aggiungerlo alla raccolta.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**Output previsto**

![Testo di output dopo il recupero della scheda di interfaccia di rete](./media/move-region-powershell/output-retrieve-nic.png)

A questo punto, aggiungere la scheda di interfaccia di rete alla raccolta Move. Questo esempio restituisce lo stesso nome alla scheda di interfaccia di rete di destinazione. Mantieni le impostazioni e la coerenza del caso.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Output previsto**

![Testo di output dopo l'aggiunta della scheda di interfaccia di rete alla raccolta](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Aggiungere il gruppo di risorse di origine

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Output previsto**

![Testo di output dopo l'aggiunta del gruppo di risorse alla raccolta](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Controlla di più le dipendenze

Verificare di nuovo la presenza di dipendenze mancanti.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Output**

Sono presenti altre dipendenze mancanti.

![Testo di output dopo il controllo per le dipendenze](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Recuperare dipendenze aggiuntive

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Output previsto**


![Testo di output dopo il recupero di dipendenze aggiuntive](./media/move-region-powershell/additional-missing-dependencies.png)

In questo esempio, altre tre dipendenze vengono identificate come mancanti:

- Indirizzo IP pubblico: psdemovm-IP
- Rete virtuale di Azure: psdemorm-VNET
- Gruppo di sicurezza di rete (NSG): psdemovm-NSG

## <a name="add-additional-dependencies"></a>Aggiungi dipendenze aggiuntive

1. [Seguire le istruzioni](#add-dependencies-to-collection) per aggiungere queste risorse alla raccolta di spostamento.
2. Dopo l'aggiunta di risorse, eseguire nuovamente la convalida finché non vengono aggiunte tutte le dipendenze.


## <a name="prepare-and-move-the-source-resource-group"></a>Preparare e spostare il gruppo di risorse di origine

Nell'area di origine preparare le risorse prima di spostarle. Il processo di preparazione dipende dalle risorse che si stanno muovendo. Ad esempio, per preparare le risorse senza stato, è possibile preparare ed esportare un modello di Azure Resource Manager (ARM). In alternativa, per le risorse con stato è possibile che la replica venga avviata. 

Prima di poter preparare le risorse di origine, è necessario preparare e spostare il gruppo di risorse di origine.

### <a name="prepare"></a>Preparazione

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**Output previsto**

![Testo di output dopo la preparazione del gruppo di risorse di origine](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Avvia spostamento

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Output previsto**

![Testo di output dopo il commit del gruppo di risorse di origine](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Preparare le risorse

Dopo che il gruppo di risorse di origine è stato spostato nell'area di destinazione, recuperare un elenco delle risorse nella raccolta di spostamento e prepararsi a spostarle.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Output previsto**

![Testo di output dopo il recupero delle risorse nella raccolta](./media/move-region-powershell/collection-resources.png)

A questo punto, preparare le risorse.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
In alternativa, è possibile preparare e spostare le risorse usando l'ID risorsa, anziché il nome:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Output previsto**

![Testo di output dopo la preparazione delle risorse nella raccolta](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Avviare lo spostamento delle risorse

Dopo aver preparato le risorse, avviare lo spostamento.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Output previsto** 
 ![ Testo di output dopo l'avvio dello spostamento delle risorse](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Rimuovere o eseguire il commit dello spostamento

Dopo lo spostamento iniziale è possibile decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 

- **Rimozione**: è possibile eliminare uno spostamento se si sta eseguendo un test e non si vuole realmente spostare la risorsa di origine. In seguito alla rimozione dello spostamento, la risorsa torna allo stato *Avvio spostamento in sospeso*. Se necessario, è possibile avviare nuovamente lo spostamento.
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo il commit una risorsa di origine si troverà nello stato *Eliminazione origine in sospeso* e sarà possibile decidere se eliminarla.

### <a name="discard"></a>Discard

Per annullare lo spostamento:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Output previsto** 
 ![ Testo di output dopo l'eliminazione dello spostamento](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

Per eseguire il commit dello spostamento:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Output previsto**

![Testo di output dopo il commit dello spostamento](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Elimina risorse di origine

Dopo aver eseguito il commit dello spostamento e aver verificato che le risorse funzionino come previsto nell'area di destinazione, è possibile eliminare le risorse di origine nell' [portale di Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [usando PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)o l'interfaccia della riga di comando di [Azure](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](remove-move-resources.md) rimuovere le risorse da una raccolta di spostamento.