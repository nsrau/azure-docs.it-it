---
title: File di inclusione
description: File di inclusione
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155741"
---
## <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

È prima necessario creare un account di Servizi multimediali. Questa sezione illustra i requisiti per la creazione dell'account con l'interfaccia della riga di comando di Azure.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente. Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come gli account di Servizi multimediali di Azure e gli account di archiviazione associati.

È possibile sostituire `amsResourceGroup` con il valore.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. Per altre informazioni sull'uso degli account di archiviazione in Servizi multimediali, vedere [Account di archiviazione](../articles/media-services/latest/storage-account-concept.md).

È necessario avere un account di archiviazione **primario**, quindi è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). Gli account solo BLOB non sono consentiti come **primari**. Per altre informazioni sugli account di archiviazione, vedere [Opzioni di account di archiviazione di Azure](../articles/storage/common/storage-account-options.md). 

In questo esempio viene creato un account di archiviazione con ridondanza locale Standard per utilizzo generico v2. Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Tuttavia, quando si sceglie SKU per la produzione, è consigliabile usare `--sku Standard_RAGRS` che offre la replica geografica per la continuità aziendale. Per altre informazioni, vedere [account di archiviazione](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Il comando seguente consente di creare un account di archiviazione che verrà associato all'account di Servizi multimediali. Nello script seguente è possibile sostituire `storageaccountforams` con il valore personale. `amsResourceGroup` deve corrispondere al valore che scelto per il gruppo di risorse nel passaggio precedente. Il nome di account di archiviazione deve avere lunghezza minore di 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

Il comando seguente dell'interfaccia della riga di comando di Azure consente di creare un nuovo account di Servizi multimediali. È possibile sostituire i valori seguenti: `amsaccount` `storageaccountforams` (deve corrispondere al valore assegnato per l'account di archiviazione) e `amsResourceGroup` (deve corrispondere al valore assegnato per il gruppo di risorse).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
