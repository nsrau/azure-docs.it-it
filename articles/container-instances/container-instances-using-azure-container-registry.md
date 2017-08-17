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
ms.service: 
ms.devlang: na
ms.topic: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: fcb21785584c3c5fb41f5ceb70346e3e05a30d93
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questo articolo illustra come distribuire in Istanze di contenitore di Azure immagini del contenitore archiviate in Registro contenitori di Azure.

## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure include i comandi per la creazione e la gestione dei contenitori in Istanze di contenitore di Azure. Se si specifica un'immagine privata nel comando `create`, è anche possibile specificare la password del registro dell'immagine necessaria per l'autenticazione con il registro contenitori.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

Il comando `create` consente anche di specificare `image-registry-login-server` e `image-registry-username`. Per impostazione predefinita, il server di accesso di Registro contenitori di Azure è tuttavia semplicemente *registryname*.azurecr.io e il nome utente è *registryname*, quindi questi valori vengono dedotti dal nome dell'immagine, se non specificati in modo esplicito.

## <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

È possibile specificare le proprietà di Registro contenitori di Azure in un modello di Azure Resource Manager. È sufficiente includere la proprietà `imageRegistryCredentials` nella definizione del gruppo di contenitori:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Per evitare di archiviare la password per il registro contenitori direttamente nel modello, è consigliabile archiviarla come segreto in [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) e farvi riferimento nel modello usando l'[integrazione nativa tra Azure Resource Manager e Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare contenitori, effettuarne il push in un registro contenitori privato e distribuirli in Istanze di contenitore di Azure [completando l'esercitazione](container-instances-tutorial-prepare-app.md).
