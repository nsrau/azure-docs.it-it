---
title: Resource providers and resource types
description: Descrive i provider di risorse che supportano Gestione risorse, i relativi schemi e le versioni API disponibili, nonché le aree che possono ospitare le risorse.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 73cc053ab2ca19f42e3c45b8350d1e2baedfcc7a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422223"
---
# <a name="azure-resource-providers-and-types"></a>Provider e tipi di risorse di Azure

Quando si distribuiscono risorse, è spesso necessario recuperare informazioni sui provider e i tipi di risorse. For example, if you want to store keys and secrets, you work with the Microsoft.KeyVault resource provider. This resource provider offers a resource type called vaults for creating the key vault.

Il nome di un tipo di risorsa è nel formato: **{resource-provider}/{resource-type}** . Il tipo di risorsa per un insieme di credenziali delle chiavi è **Microsoft.KeyVault/vaults**.

In questo articolo viene spiegato come:

* Visualizzare tutti i provider di risorse in Azure
* Controllare lo stato di registrazione di un provider di risorse
* Registrare un provider di risorse
* Visualizzare i tipi di risorse per un provider di risorse
* Visualizzare le località valide per un tipo di risorsa
* Visualizzare le versioni API valide per un tipo di risorsa

You can do these steps through the Azure portal, Azure PowerShell, or Azure CLI.

For a list that maps resource providers to Azure services, see [Resource providers for Azure services](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Portale di Azure

Per visualizzare tutti i provider di risorse e lo stato di registrazione della propria sottoscrizione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. On the Azure portal menu, select **All services**.

    ![selezionare sottoscrizioni](./media/resource-manager-supported-services/select-all-services.png)

3. Nella casella **Tutti i servizi** immettere **sottoscrizione** e quindi selezionare **Sottoscrizioni**.
4. Selezionare la sottoscrizione dall'elenco per visualizzarla.
5. Selezionare **Provider di risorse** e visualizzare l'elenco dei provider di risorse disponibili.

    ![visualizzare i provider di risorse](./media/resource-manager-supported-services/show-resource-providers.png)

6. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per impostazione predefinita, molti provider di risorse vengono registrati automaticamente. Potrebbe essere tuttavia necessario registrare manualmente alcuni provider di risorse. To register a resource provider, you must have permission to do the `/register/action` operation for the resource provider. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Per registrare un provider di risorse, selezionare **Registra**. Nello screenshot precedente il collegamento **Registra** è evidenziato per **Microsoft.Blueprint**.

    You can't unregister a resource provider when you still have resource types from that resource provider in your subscription.

Per visualizzare le informazioni relative uno specifico provider di risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. On the Azure portal menu, select **All services**.
3. Nella casella **Tutti i servizi** immettere **esplora risorse** e quindi selezionare **Esplora risorse**.

    ![Selezionare Tutti i servizi](./media/resource-manager-supported-services/select-resource-explorer.png)

4. Espandere **Provider** selezionando la freccia rivolta verso destra.

    ![Selezionare i provider](./media/resource-manager-supported-services/select-providers.png)

5. Espandere un provider di risorse e un tipo di risorsa da visualizzare.

    ![Selezionare il tipo di risorsa](./media/resource-manager-supported-services/select-resource-type.png)

6. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero essere anche presenti limitazioni sulla sottoscrizione che impediscono l'uso di alcune aree che supportano la risorsa. Resource Explorer visualizza le località valide per il tipo di risorsa.

    ![Visualizzare le località](./media/resource-manager-supported-services/show-locations.png)

7. La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST. Resource Explorer visualizza le versioni API valide per il tipo di risorsa.

    ![Visualizzare le versioni API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per visualizzare tutti i provider di risorse in Azure e lo stato di registrazione di una sottoscrizione, usare il comando seguente:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Che restituisce risultati simili a:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per impostazione predefinita, molti provider di risorse vengono registrati automaticamente. Potrebbe essere tuttavia necessario registrare manualmente alcuni provider di risorse. To register a resource provider, you must have permission to do the `/register/action` operation for the resource provider. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Che restituisce risultati simili a:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

You can't unregister a resource provider when you still have resource types from that resource provider in your subscription.

Per visualizzare informazioni su un provider di risorse specifico, usare il comando seguente:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Che restituisce risultati simili a:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Per visualizzare i tipi di risorse per un provider di risorse, usare il comando seguente:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Che restituisce:

```powershell
batchAccounts
operations
locations
locations/quotas
```

La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST.

Per ottenere le versioni dell'API disponibili per un tipo di risorsa, usare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Che restituisce:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero essere anche presenti limitazioni sulla sottoscrizione che impediscono l'uso di alcune aree che supportano la risorsa.

Per ottenere le località supportate per un tipo di risorsa, usare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Che restituisce:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare tutti i provider di risorse in Azure e lo stato di registrazione di una sottoscrizione, usare il comando seguente:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Che restituisce risultati simili a:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per impostazione predefinita, molti provider di risorse vengono registrati automaticamente. Potrebbe essere tuttavia necessario registrare manualmente alcuni provider di risorse. To register a resource provider, you must have permission to do the `/register/action` operation for the resource provider. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario.

```azurecli
az provider register --namespace Microsoft.Batch
```

Che restituisce un messaggio di registrazione in corso.

You can't unregister a resource provider when you still have resource types from that resource provider in your subscription.

Per visualizzare informazioni su un provider di risorse specifico, usare il comando seguente:

```azurecli
az provider show --namespace Microsoft.Batch
```

Che restituisce risultati simili a:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Per visualizzare i tipi di risorse per un provider di risorse, usare il comando seguente:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Che restituisce:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST.

Per ottenere le versioni dell'API disponibili per un tipo di risorsa, usare il comando seguente:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Che restituisce:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero essere anche presenti limitazioni sulla sottoscrizione che impediscono l'uso di alcune aree che supportano la risorsa.

Per ottenere le località supportate per un tipo di risorsa, usare il comando seguente:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Che restituisce:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md). 
* Per visualizzare gli schemi dei modelli dei provider di risorse, vedere le [informazioni di riferimento sui modelli](/azure/templates/).
* For a list that maps resource providers to Azure services, see [Resource providers for Azure services](azure-services-resource-providers.md).
* Per visualizzare le operazioni di un provider di risorse, vedere [Azure REST API](/rest/api/) (API REST di Azure).
