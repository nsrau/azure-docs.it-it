---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un servizio SignalR
description: Questo script di esempio illustra come creare una nuova istanza di Servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 17b8df919c5059da3f3abd7a4f06aca1eadd0817
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563971"
---
# <a name="create-a-signalr-service"></a>Servizio Azure SignalR 

Questo script di esempio crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio

Questo script crea una nuova risorsa servizio SignalR e un nuovo gruppo di risorse. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

echo "$primaryConnectionString"
```

Annotare il nome effettivo generato per il nuovo gruppo di risorse. Il nome del gruppo di risorse viene usato quando si vuole eliminare tutte le risorse di gruppo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Crea una risorsa servizio Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Elencare le chiavi che saranno usate dall'applicazione per il push in tempo reale degli aggiornamenti del contenuto con SignalR. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio Azure SignalR sono disponibili nella [documentazione del servizio Azure SignalR](../signalr-reference-cli.md).
