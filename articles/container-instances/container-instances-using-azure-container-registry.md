---
title: Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure
description: Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 4205b47dc67920021812c1e573a98de64ad198ec
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questo articolo illustra come distribuire in Istanze di contenitore di Azure immagini del contenitore archiviate in Registro contenitori di Azure.

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure include i comandi per la creazione e la gestione dei contenitori in Istanze di contenitore di Azure. Se si specifica un'immagine privata di [contenitore az creare] [ az-container-create] comando, è inoltre possibile specificare la password di immagine del Registro di sistema necessaria per l'autenticazione con il Registro di sistema del contenitore.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

Il [contenitore az creare] [ az-container-create] comando supporta anche specificando `--registry-login-server` e `--registry-username`. Il server di accesso di Registro contenitori di Azure è tuttavia sempre *nomeregistro*.azurecr.io e il nome utente predefinito è *nomeregistro*, quindi questi valori vengono dedotti dal nome dell'immagine, se non specificati in modo esplicito.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuire con il modello di gestione risorse di Azure

È possibile specificare le proprietà di Registro contenitori di Azure in un modello di Azure Resource Manager includendo la proprietà `imageRegistryCredentials` nella definizione del gruppo di contenitori:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Per evitare di archiviare la password del registro contenitori direttamente nel modello, è consigliabile archiviarla come segreto in [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) e farvi riferimento nel modello usando l'[integrazione nativa tra Azure Resource Manager e Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuire con il portale di Azure

Se si conservano le immagini del contenitore in Registro contenitori di Azure, è possibile creare facilmente un contenitore in Istanze di contenitore di Azure usando il portale di Azure.

1. Nel portale di Azure passare al registro contenitori.

2. Selezionare **repository**, quindi selezionare il repository che si desidera distribuire, fare doppio clic su per l'immagine contenitore che si desidera distribuire e selezionare il tag **eseguire istanza**.

    !["Esegui istanza" nel Registro di sistema contenitore di Azure nel portale di Azure][acr-runinstance-contextmenu]

3. Immettere un nome per il contenitore e un nome per il gruppo di risorse. Se si vuole, è anche possibile cambiare i valori predefiniti.

    ![Menu di creazione per Istanze di contenitore di Azure][acr-create-deeplink]

4. Al termine della distribuzione, è possibile passare al gruppo di contenitori dal riquadro delle notifiche per trovare l'indirizzo IP e le altre proprietà.

    ![Visualizzazione dei dettagli del gruppo di contenitori in Istanze di contenitore di Azure][aci-detailsview]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare contenitori, effettuarne il push in un registro contenitori privato e distribuirli in Istanze di contenitore di Azure [completando l'esercitazione](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create