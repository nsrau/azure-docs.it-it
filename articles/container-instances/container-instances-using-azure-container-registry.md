---
title: Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure
description: Informazioni su come distribuire i contenitori nelle istanze di contenitore di Azure usando le immagini contenitore in un registro contenitori di Azure.
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questo articolo illustra come distribuire in Istanze di contenitore di Azure immagini del contenitore archiviate in Registro contenitori di Azure.

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure include i comandi per la creazione e la gestione dei contenitori in Istanze di contenitore di Azure. Se si specifica un'immagine privata nel comando [az container create][az-container-create], è anche possibile specificare la password del registro dell'immagine necessaria per l'autenticazione con il registro contenitori.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

Il comando [az container create][az-container-create] consente anche di specificare `--registry-login-server` e `--registry-username`. Il server di accesso di Registro contenitori di Azure è tuttavia sempre *nomeregistro*.azurecr.io e il nome utente predefinito è *nomeregistro*, quindi questi valori vengono dedotti dal nome dell'immagine, se non specificati in modo esplicito.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuire con un modello di Azure Resource Manager

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

1. Selezionare **Repository**, quindi selezionare il repository da cui si desidera eseguire la distribuzione, fare clic con il pulsante destro del mouse sul tag dell'immagine del contenitore che si vuole distribuire e selezionare **Esegui istanza**.

    !["Esegui istanza" nel Registro contenitori di Azure nel portale di Azure][acr-runinstance-contextmenu]

1. Immettere un nome per il contenitore e un nome per il gruppo di risorse. Se si vuole, è anche possibile cambiare i valori predefiniti.

    ![Menu di creazione per Istanze di contenitore di Azure][acr-create-deeplink]

1. Al termine della distribuzione, è possibile passare al gruppo di contenitori dal riquadro delle notifiche per trovare l'indirizzo IP e le altre proprietà.

    ![Visualizzazione dei dettagli del gruppo di contenitori in Istanze di contenitore di Azure][aci-detailsview]

## <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Se l'utente amministratore del Registro contenitori di Azure è disabilitato, è possibile usare un'[entità servizio](../container-registry/container-registry-auth-service-principal.md) di Azure Active Directory per eseguire l'autenticazione al registro quando si crea un'istanza del contenitore. È consigliabile usare un'entità servizio per l'autenticazione anche negli scenari headless, ad esempio uno script o un'applicazione che crea un'istanza del contenitore in maniera automatica.

Per altre informazioni, vedere [Eseguire l'autenticazione con Registro contenitori di Azure dalle istanze di contenitore di Azure](../container-registry/container-registry-auth-aci.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare contenitori, effettuarne il push in un registro contenitori privato e distribuirli in Istanze di contenitore di Azure [completando l'esercitazione](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create