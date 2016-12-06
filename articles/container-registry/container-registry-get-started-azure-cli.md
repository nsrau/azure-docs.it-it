---
title: Creare un registro di contenitori con l&quot;interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Iniziare a creare e gestire i registri di contenitori di Azure con l&quot;interfaccia della riga di comando di Azure 2.0 (anteprima)
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: d806564056390ea2db74eebbbe89e1b20a350f33

---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Creare un registro di contenitori usando l'interfaccia della riga di comando di Azure
Usare i comandi dell'[interfaccia della riga di comando di Azure 2.0 (anteprima)](https://github.com/Azure/azure-cli) per creare un registro di contenitori e gestirne le impostazioni dal computer Linux, Mac o Windows. È anche possibile creare e gestire registri di contenitori usando il [portale di Azure](container-registry-get-started-portal.md) oppure, a livello di codice, con l'[API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) del servizio Container Registry.


* Per concetti e informazioni di base, vedere [What is Azure Container Registry?](container-registry-intro.md) (Che cos'è Azure Container Registry?).
* Per informazioni sui comandi dell'interfaccia della riga di comando del servizio Container Registry (comandi `az acr`), passare il parametro `-h` a qualsiasi comando.

> [!NOTE]
> Il servizio Container Registry è attualmente disponibile in anteprima.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* **Interfaccia della riga di comando di Azure 2.0 (anteprima)**: per installare e iniziare a usare l'interfaccia della riga di comando di Azure 2.0 (anteprima), vedere le [istruzioni di installazione](https://github.com/Azure/azure-cli/blob/master/README.rst). Accedere alla sottoscrizione di Azure usando `az login`.
* **Gruppo di risorse**: definire un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) prima di creare un registro di contenitori o usare un gruppo di risorse esistente. Verificare che il gruppo di risorse si trovi in una posizione in cui il servizio Container Registry è [disponibile](https://azure.microsoft.com/regions/services/). Per creare un gruppo di risorse usando l'interfaccia della riga di comando di Azure 2.0 (anteprima), vedere [questi esempi](https://github.com/Azure/azure-cli-samples/tree/master/arm). 
* **Account di archiviazione** (facoltativo): creare un [account di archiviazione](../storage/storage-introduction.md) di Azure standard per eseguire il backup del registro di contenitori nella stessa posizione. Se non si specifica un account di archiviazione durante la creazione di un registro con `az acr create`, l'account viene creato automaticamente. Per creare un account di archiviazione usando l'interfaccia della riga di comando di Azure 2.0 (anteprima), vedere [questi esempi](https://github.com/Azure/azure-cli-samples/tree/master/storage).
* **Entità servizio** (facoltativo): quando si crea un registro con l'interfaccia della riga di comando, per impostazione predefinita il registro non è configurato per l'accesso. A seconda delle esigenze, è possibile assegnare a un registro un'entità servizio Azure Active Directory esistente (oppure crearne e assegnarne una nuova) oppure abilitare l'account di utente amministratore del registro. Vedere le sezioni più avanti in questo articolo. Per altre informazioni sull'accesso al registro, vedere [Authenticate with the container registry](container-registry-authentication.md) (Eseguire l'autenticazione al registro di contenitori). 

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
Eseguire il comando `az acr create` per creare un registro di contenitori. 

> [!TIP]
> Quando si crea un registro, specificare un nome di dominio univoco globale di primo livello, contenente solo lettere e numeri. Il nome del registro negli esempi è `myRegistry`, ma è necessario sostituirlo con un nome univoco personalizzato. 
> 
> 

Il comando seguente usa i parametri minimi per creare il registro di contenitori `myRegistry` nel gruppo di risorse `myResourceGroup` nell'area South Central US:

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* `--storage-account-name` o `-s` è facoltativo. Se non configurato, viene creato un account di archiviazione con un nome casuale nel gruppo di risorse specificato.

L'output è simile al seguente:

![az acr create output](./media/container-registry-get-started-azure-cli/acr_create.png)


Considerare con attenzione le seguenti voci:

* `id`: identificatore del registro nella sottoscrizione, necessario se si vuole assegnare un'entità servizio. 
* `loginServer`: nome completo specificato per [accedere al registro](container-registry-authentication.md). In questo esempio il nome è `myregistry-contoso.exp.azurecr.io` (tutto in lettere minuscole).

## <a name="assign-a-service-principal"></a>Assegnare un'entità servizio
Usare i comandi dell'interfaccia della riga di comando di Azure 2.0 (anteprima) per assegnare a un registro un'entità servizio Azure Active Directory. All'entità servizio in questi esempi è assegnato il ruolo di proprietario, ma è possibile assegnare [altri ruoli](../active-directory/role-based-access-control-configure.md).

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Creare un'entità servizio e assegnare l'accesso al registro
Nel comando seguente, a una nuova entità servizio viene assegnato il ruolo di proprietario per l'accesso all'identificatore di registro passato con il parametro `--scopes`. Specificare una password complessa con il parametro `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Assegnare un'entità servizio esistente
Se è già configurata un'entità servizio e si vuole assegnare a tale entità il ruolo di proprietario per l'accesso al registro, eseguire un comando simile all'esempio seguente. Passare l'ID app dell'entità servizio usando il parametro `--assignee`:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Gestire le credenziali di amministratore
Per ogni registro di contenitori viene creato automaticamente un account amministratore che è disabilitato per impostazione predefinita. Gli esempi seguenti mostrano i comandi dell'interfaccia della riga di comando `az acr` che consentono di gestire le credenziali di amministratore per il registro di contenitori.

### <a name="obtain-admin-user-credentials"></a>Ottenere le credenziali di utente amministratore
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Abilitare l'utente amministratore per un registro esistente
```azurecli
az acr update -n myRegistry --enable-admin
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Disabilitare l'utente amministratore per un registro esistente
```azurecli
az acr update -n myRegistry --disable-admin
```

## <a name="list-images-and-tags"></a>Elencare immagini e tag
Usare i comandi dell'interfaccia della riga di comando `az acr` per eseguire query su immagini e tag in un repository. 

> [!NOTE]
> Attualmente, il servizio Container Registry non supporta il comando `docker search` per eseguire query relative a immagini e tag.


### <a name="list-repositories"></a>Elencare repository
L'esempio seguente elenca i repository in un registro, in formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>Elencare tag
L'esempio seguente elenca i tag sul repository **samples/nginx**, in formato JSON:

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)




<!--HONumber=Nov16_HO3-->


