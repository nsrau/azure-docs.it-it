---
title: Guida introduttiva di Azure - Creare un account di archiviazione con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Apprendere a creare rapidamente un nuovo account di archiviazione con l'interfaccia della riga di comando di Azure.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: ed956e3d27d315e0ce4901c2c38d50652f77c09a
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Creare un account di archiviazione usando l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa Guida introduttiva descrive in dettaglio l'uso dell'interfaccia della riga di comando di Azure per creare un account di archiviazione di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

In caso di dubbi su quale area per specificare per il parametro `--location` è possibile recuperare un elenco di aree supportate per la sottoscrizione tramite il comando [az account list-locations](/cli/azure/account#list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-storage-account"></a>Creare un account di archiviazione per utilizzo generico

Esistono diversi tipi di account di archiviazione appropriati per diversi scenari d'uso, ognuno dei quali supporta uno o più servizi di archiviazione (BLOB, file, tabelle o code). La tabella seguente illustra i tipi di account di archiviazione disponibili.

|**Tipo di account di archiviazione**|**Utilizzo generico Standard**|**Utilizzo generico Premium**|**Archiviazione BLOB, livelli ad accesso frequente e ad accesso sporadico**|
|-----|-----|-----|-----|
|**Servizi supportati**| Servizi BLOB, file, tabella, coda | Servizio BLOB | Servizio BLOB|
|**Tipi di BLOB supportati**|BLOB in blocchi, BLOB di pagine e BLOB di aggiunta | BLOB di pagine | BLOB in blocchi e BLOB di aggiunta|

Creare un account di archiviazione per utilizzo generico con il comando [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --kind Storage \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse del gruppo risorse non sono più necessarie, incluso l'account di archiviazione creato in questa Guida rapida, è possibile eliminare il gruppo di risorse con il comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva sono stati creati un gruppo di risorse e un account di archiviazione standard per uso generico. Per informazioni su come trasferire dati da e verso l'account di archiviazione, vedere la guida introduttiva dell'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Trasferire oggetti da e verso la risorsa di archiviazione BLOB con l'interfaccia della riga di comando di Azure](../blobs/storage-quickstart-blobs-cli.md)