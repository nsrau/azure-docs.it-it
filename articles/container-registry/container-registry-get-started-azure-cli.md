---
title: Creare un registro per contenitori Docker privati in Azure - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Introduzione alla creazione e gestione dei registri per contenitori Docker privati con l'interfaccia della riga di comando di Azure 2.0
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2875f4089231ed12a0312b2c2e077938440365c6
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Creare un registro per contenitori Docker privati usando l'interfaccia della riga di comando di Azure 2.0
Usare i comandi dell'[interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-cli) per creare un registro di contenitori e gestirne le impostazioni dal computer Linux, Mac o Windows. È anche possibile creare e gestire registri di contenitori usando il [portale di Azure](container-registry-get-started-portal.md) oppure, a livello di codice, con l'[API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) del servizio Container Registry.


* Per informazioni di base e concetti, vedere la [panoramica](container-registry-intro.md)
* Per informazioni sui comandi dell'interfaccia della riga di comando del servizio Container Registry (comandi `az acr`), passare il parametro `-h` a qualsiasi comando.


## <a name="prerequisites"></a>Prerequisiti
* **Interfaccia della riga di comando di Azure 2.0**: per installare e iniziare a usare l'interfaccia della riga di comando di Azure 2.0, vedere le [istruzioni di installazione](/cli/azure/install-azure-cli). Accedere alla sottoscrizione di Azure usando `az login`. Per altre informazioni, vedere [Get started with the CLI 2.0](/cli/azure/get-started-with-azure-cli) (Introduzione all'interfaccia della riga di comando di Azure 2.0).
* **Gruppo di risorse**: creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) prima di creare un registro di contenitori o usare un gruppo di risorse esistente. Verificare che il gruppo di risorse si trovi in una posizione in cui il servizio Container Registry è [disponibile](https://azure.microsoft.com/regions/services/). Per creare un gruppo di risorse usando l'interfaccia della riga di comando di Azure 2.0, vedere le [informazioni di riferimento sull'interfaccia della riga di comando 2.0](/cli/azure/group).
* **Account di archiviazione** (facoltativo): creare un [account di archiviazione](../storage/common/storage-introduction.md) di Azure standard per eseguire il backup del registro di contenitori nella stessa posizione. Se non si specifica un account di archiviazione durante la creazione di un registro con `az acr create`, l'account viene creato automaticamente. Per creare un account di archiviazione usando l'interfaccia della riga di comando 2.0, vedere le [informazioni di riferimento sull'interfaccia della riga di comando 2.0](/cli/azure/storage/account). Archiviazione Premium non è attualmente supportata.
* **Entità servizio** (facoltativo): quando si crea un registro con l'interfaccia della riga di comando, per impostazione predefinita il registro non è configurato per l'accesso. A seconda delle esigenze, è possibile assegnare a un registro un'entità servizio Azure Active Directory esistente (oppure crearne e assegnarne una nuova) oppure abilitare l'account di utente amministratore del registro. Vedere le sezioni più avanti in questo articolo. Per altre informazioni sull'accesso al registro, vedere [Authenticate with the container registry](container-registry-authentication.md) (Eseguire l'autenticazione al registro di contenitori).

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
Eseguire il comando `az acr create` per creare un registro di contenitori.

> [!TIP]
> Quando si crea un registro, specificare un nome di dominio univoco globale di primo livello, contenente solo lettere e numeri. Il nome del registro negli esempi è `myRegistry1`, ma è necessario sostituirlo con un nome univoco personalizzato.
>
>

Il comando seguente usa i parametri minimi per creare il registro contenitori `myRegistry1` nel gruppo di risorse `myResourceGroup` usando lo SKU *Basic*:

```azurecli
az acr create --name myRegistry1 --resource-group myResourceGroup --sku Basic
```

* `--storage-account-name` è facoltativo. Se non è specificato, verrà creato un account di archiviazione con un nome costituito dal nome registro e un timestamp nel gruppo di risorse specificato.

Quando viene creato il registro, l'output è simile al seguente:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T18:36:29.124842+00:00",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry
/registries/myRegistry1",
  "location": "southcentralus",
  "loginServer": "myregistry1.azurecr.io",
  "name": "myRegistry1",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myregistry123456789"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```


Considerare con attenzione le seguenti voci:

* `id`: identificatore del registro nella sottoscrizione, necessario se si vuole assegnare un'entità servizio.
* `loginServer`: nome completo specificato per [accedere al registro](container-registry-authentication.md). In questo esempio il nome è `myregistry1.exp.azurecr.io` (tutto in lettere minuscole).

## <a name="assign-a-service-principal"></a>Assegnare un'entità servizio
Usare i comandi dell'interfaccia della riga di comando di Azure 2.0 per assegnare a un registro un'entità servizio Azure Active Directory. All'entità servizio in questi esempi è assegnato il ruolo di proprietario, ma è possibile assegnare [altri ruoli](../active-directory/role-based-access-control-configure.md).

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Creare un'entità servizio e assegnare l'accesso al registro
Nel comando seguente, a una nuova entità servizio viene assegnato il ruolo di proprietario per l'accesso all'identificatore di registro passato con il parametro `--scopes`. Specificare una password complessa con il parametro `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Assegnare un'entità servizio esistente
Se è già configurata un'entità servizio e si vuole assegnare a tale entità il ruolo di proprietario per l'accesso al registro, eseguire un comando simile all'esempio seguente. Passare l'ID app dell'entità servizio usando il parametro `--assignee`:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Gestire le credenziali di amministratore
Per ogni registro di contenitori viene creato automaticamente un account amministratore che è disabilitato per impostazione predefinita. Gli esempi seguenti mostrano i comandi dell'interfaccia della riga di comando `az acr` che consentono di gestire le credenziali di amministratore per il registro di contenitori.

### <a name="obtain-admin-user-credentials"></a>Ottenere le credenziali di utente amministratore
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Abilitare l'utente amministratore per un registro esistente
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Disabilitare l'utente amministratore per un registro esistente
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>Elencare immagini e tag
Usare i comandi dell'interfaccia della riga di comando `az acr` per eseguire query su immagini e tag in un repository.

> [!NOTE]
> Attualmente, il servizio Container Registry non supporta il comando `docker search` per eseguire query relative a immagini e tag.


### <a name="list-repositories"></a>Elencare repository
L'esempio seguente elenca i repository in un registro, in formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>Elencare tag
L'esempio seguente elenca i tag sul repository **samples/nginx**, in formato JSON:

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)

