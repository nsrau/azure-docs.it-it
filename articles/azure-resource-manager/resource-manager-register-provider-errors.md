---
title: Errori di registrazione del provider di risorse di Azure | Microsoft Docs
description: Viene descritto come risolvere gli errori di registrazione del provider di risorse di Azure.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 303b3ae0ee7b4baeda974d2b3c62fefa0a68796f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Risoluzione degli errori di registrazione del provider di risorse

Questo articolo descrive gli errori che potrebbero verificarsi quando si usa un provider di risorse che non è già stato usato nella sottoscrizione.

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa, è possibile che venga visualizzato il codice di errore e il messaggio seguenti:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

In alternativa, si potrebbe ricevere un messaggio analogo che indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Il messaggio di errore dovrebbe fornire suggerimenti per le versioni di API e i percorsi supportati. È possibile modificare il modello impostando uno dei valori suggeriti. La maggior parte dei provider, ma non tutti, vengono registrati automaticamente dal portale di Azure o dall'interfaccia della riga di comando che si sta usando. Se non è mai stato usato un provider di risorse specifico, potrebbe essere necessario registrarlo.

## <a name="cause"></a>Causa

Questi errori vengono visualizzati per uno di questi tre motivi:

1. Il provider di risorse non è stato registrato per la sottoscrizione
1. La versione dell'API non è supportata per il tipo di risorsa
1. Il percorso non è supportato per il tipo di risorsa

## <a name="solution-1---powershell"></a>Soluzione 1: PowerShell

Per visualizzare lo stato della registrazione in PowerShell usare **Get-AzureRmResourceProvider**.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Per registrare un provider, usare **Register-AzureRmResourceProvider** e specificare il nome del provider di risorse da registrare.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Per ottenere i percorsi supportati per un tipo di risorsa particolare è possibile usare:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Per ottenere le versioni di API supportate per un tipo di risorsa particolare è possibile usare:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Soluzione 2: interfaccia della riga di comando di Azure

Per vedere se il provider è registrato, usare il comando `az provider list` .

```azurecli-interactive
az provider list
```

Per registrare un provider di risorse, usare il comando `az provider register` e specificare lo *spazio dei nomi* per la registrazione.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Per visualizzare le versioni di API e i percorsi supportati per un tipo di risorsa, usare:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Soluzione 3: portale di Azure

È possibile visualizzare lo stato di registrazione e registrare uno spazio dei nomi del provider di risorse tramite il portale.

1. Selezionare **Provider di risorse** per la propria sottoscrizione.

   ![selezionare i provider di risorse](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Esaminare l'elenco dei provider di risorse e, se necessario, selezionare il link **Registra** per registrare il provider di risorse del tipo che si intende distribuire.

   ![Elenco di provider di risorse](./media/resource-manager-register-provider-errors/list-resource-providers.png)
