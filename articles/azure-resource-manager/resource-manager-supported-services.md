---
title: Provider e tipi di risorse di Azure | Microsoft Docs
description: "Descrive i provider di risorse che supportano Gestione risorse, i relativi schemi e le versioni API disponibili, nonché le aree che possono ospitare le risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 6a9128f45d4199404019cee594842d59c7f1aaf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="resource-providers-and-types"></a>Provider e tipi di risorse

Quando si distribuiscono risorse, è spesso necessario recuperare informazioni sui provider e i tipi di risorse. Questo articolo illustra come:

* Visualizzare tutti i provider di risorse in Azure
* Controllare lo stato di registrazione di un provider di risorse
* Registrare un provider di risorse
* Visualizzare i tipi di risorse per un provider di risorse
* Visualizzare le località valide per un tipo di risorsa
* Visualizzare le versioni API valide per un tipo di risorsa

È possibile eseguire questi passaggi tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="powershell"></a>PowerShell

Per visualizzare tutti i provider di risorse in Azure e lo stato di registrazione di una sottoscrizione, usare il comando seguente:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
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

La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per impostazione predefinita, molti provider di risorse vengono registrati automaticamente. Potrebbe essere tuttavia necessario registrare manualmente alcuni provider di risorse. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione `/register/action` per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Che restituisce risultati simili a:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Non è possibile annullare la registrazione di un provider di risorse quando nella sottoscrizione sono ancora presenti tipi di risorsa di tale provider di risorse.

Per visualizzare informazioni su un provider di risorse specifico, usare il comando seguente:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
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

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
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

La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per impostazione predefinita, molti provider di risorse vengono registrati automaticamente. Potrebbe essere tuttavia necessario registrare manualmente alcuni provider di risorse. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione `/register/action` per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario.

```azurecli
az provider register --namespace Microsoft.Batch
```

Che restituisce un messaggio di registrazione in corso.

Non è possibile annullare la registrazione di un provider di risorse quando nella sottoscrizione sono ancora presenti tipi di risorsa di tale provider di risorse.

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

## <a name="portal"></a>di Microsoft Azure

Per visualizzare tutti i provider di risorse in Azure e lo stato di registrazione di una sottoscrizione, selezionare **Sottoscrizioni**.

![selezionare sottoscrizioni](./media/resource-manager-supported-services/select-subscriptions.png)

Scegliere la sottoscrizione da visualizzare.

![specificare la sottoscrizione](./media/resource-manager-supported-services/subscription.png)

Selezionare **Provider di risorse** e visualizzare l'elenco dei provider di risorse disponibili.

![visualizzare i provider di risorse](./media/resource-manager-supported-services/show-resource-providers.png)

La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per impostazione predefinita, molti provider di risorse vengono registrati automaticamente. Potrebbe essere tuttavia necessario registrare manualmente alcuni provider di risorse. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione `/register/action` per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Per registrare un provider di risorse, selezionare **Registra**.

![registrare un provider di risorse](./media/resource-manager-supported-services/register-provider.png)

Non è possibile annullare la registrazione di un provider di risorse quando nella sottoscrizione sono ancora presenti tipi di risorsa di tale provider di risorse.

Per visualizzare informazioni su un provider di risorse specifico, selezionare **Altri servizi**.

![selezionare altri servizi](./media/resource-manager-supported-services/more-services.png)

Cercare **Resource Explorer** e selezionarlo dalle opzioni disponibili.

![selezionare resource explorer](./media/resource-manager-supported-services/select-resource-explorer.png)

Selezionare **Provider**.

![Selezionare i provider](./media/resource-manager-supported-services/select-providers.png)

Selezionare il provider di risorse e il tipo di risorsa da visualizzare.

![Selezionare il tipo di risorsa](./media/resource-manager-supported-services/select-resource-type.png)

Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero essere anche presenti limitazioni sulla sottoscrizione che impediscono l'uso di alcune aree che supportano la risorsa. Resource Explorer visualizza le località valide per il tipo di risorsa.

![Visualizzare le località](./media/resource-manager-supported-services/show-locations.png)

La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST. Resource Explorer visualizza le versioni API valide per il tipo di risorsa.

![Visualizzare le versioni API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
* Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).
* Per visualizzare le operazioni di un provider di risorse, vedere [Azure REST API](/rest/api/) (API REST di Azure).

