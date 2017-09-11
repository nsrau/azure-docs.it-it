---
title: Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure | Azure Docs
description: Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: aa1c4ea379c10dff246e2f924a345f9fa444aa64
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questo articolo illustra come distribuire in Istanze di contenitore di Azure immagini del contenitore archiviate in Registro contenitori di Azure.

## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure include i comandi per la creazione e la gestione dei contenitori in Istanze di contenitore di Azure. Se si specifica un'immagine privata nel comando `create`, è anche possibile specificare la password del registro dell'immagine necessaria per l'autenticazione con il registro contenitori.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

Il comando `create` consente anche di specificare `registry-login-server` e `registry-username`. Il server di accesso di Registro contenitori di Azure è tuttavia sempre *nomeregistro*.azurecr.io e il nome utente predefinito è *nomeregistro*, quindi questi valori vengono dedotti dal nome dell'immagine, se non specificati in modo esplicito.

## <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

È possibile specificare le proprietà di Registro contenitori di Azure in un modello di Azure Resource Manager includendo la proprietà `imageRegistryCredentials` nella definizione del gruppo di contenitori:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Per evitare di archiviare la password del registro contenitori direttamente nel modello, è consigliabile archiviarla come segreto in [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) e farvi riferimento nel modello usando l'[integrazione nativa tra Azure Resource Manager e Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Se si conservano le immagini del contenitore in Registro contenitori di Azure, è possibile creare facilmente un contenitore in Istanze di contenitore di Azure usando il portale di Azure.

1. Nel portale di Azure passare al registro contenitori.

2. Scegliere Repository.

    ![Menu di Registro contenitori di Azure nel portale di Azure][acr-menu]

3. Scegliere il repository da cui si vuole eseguire la distribuzione.

4. Fare clic con il pulsante destro del mouse sul tag dell'immagine del contenitore che si vuole distribuire.

    ![Menu di scelta rapida per avviare il contenitore con Istanze di contenitore di Azure][acr-runinstance-contextmenu]

5. Immettere un nome per il contenitore e un nome per il gruppo di risorse. Se si vuole, è anche possibile cambiare i valori predefiniti.

    ![Menu di creazione per Istanze di contenitore di Azure][acr-create-deeplink]

6. Al termine della distribuzione, è possibile passare al gruppo di contenitori dal riquadro delle notifiche per trovare l'indirizzo IP e le altre proprietà.

    ![Visualizzazione dei dettagli del gruppo di contenitori in Istanze di contenitore di Azure][aci-detailsview]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare contenitori, effettuarne il push in un registro contenitori privato e distribuirli in Istanze di contenitore di Azure [completando l'esercitazione](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png

