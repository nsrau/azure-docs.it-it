---
title: File di inclusione
description: File di inclusione
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9ecb07a2cb278f6cde4ffdc3b252cb9e816d08da
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414638"
---
## <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

È prima necessario creare un account di Servizi multimediali. Questa sezione illustra i requisiti per la creazione dell'account con l'interfaccia della riga di comando di Azure.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente. Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come gli account di Servizi multimediali di Azure e gli account di archiviazione associati.

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. 

È necessario avere un account di archiviazione **primario**, quindi è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). Gli account solo BLOB non sono consentiti come **primari**. Per altre informazioni sugli account di archiviazione, vedere [Opzioni di account di archiviazione di Azure](../articles/storage/common/storage-account-options.md). 

Per altre informazioni sull'uso degli account di archiviazione in Servizi multimediali, vedere [Account di archiviazione](../articles/media-services/latest/storage-account-concept.md).

Il comando seguente consente di creare un account di archiviazione che verrà associato all'account di Servizi multimediali. Nello script seguente è possibile sostituire `storageaccountforams` con il valore personale. La lunghezza del nome dell'account non deve superare i 24 caratteri.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

Il comando seguente dell'interfaccia della riga di comando di Azure consente di creare un nuovo account di Servizi multimediali. È possibile sostituire i valori seguenti: `amsaccount` `storageaccountforams` (deve corrispondere al valore assegnato per l'account di archiviazione) e `amsResourceGroup` (deve corrispondere al valore assegnato per il gruppo di risorse).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
