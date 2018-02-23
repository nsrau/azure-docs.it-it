---
title: Eseguire l'autenticazione con Registro contenitori di Azure dalle istanze di contenitore di Azure
description: "Informazioni su come fornire l'accesso alle immagini nel registro del contenitore privato dalle istanze contenitore di Azure usando un'entità del servizio Azure Active Directory."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 00d9632a5d0c42eceee1b412f8963bbadbea651f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Eseguire l'autenticazione con Registro contenitori di Azure dalle istanze di contenitore di Azure

È possibile usare un'entità servizio Azure Active Directory (Azure AD) per fornire l'accesso ai registri contenitori privati in Registro contenitori di Azure.

Questo articolo illustra come creare e configurare un'entità servizio Azure AD con le autorizzazioni *pull* al registro. Si avvia un contenitore nelle istanze di contenitore di Azure che esegue il pull delle immagini dal registro privato usando l'entità servizio per l'autenticazione.

## <a name="when-to-use-a-service-principal"></a>Quando usare un'entità servizio

Usare un'entità servizio per l'autenticazione dalle istanze di contenitore di Azure negli **scenari headless**, ad esempio nelle applicazioni o nei servizi che creano delle istanze del contenitore in modo automatico.

Ad esempio, se uno script automatico viene eseguito nelle ore notturne e crea un'[istanza del contenitore basata sull'attività](../container-instances/container-instances-restart-policy.md) per elaborare alcuni dati, può usare un'entità servizio con le autorizzazioni pull-only (lettore) per l'autenticazione al registro. È possibile ruotare le credenziali dell'entità servizio o revocarne completamente l'accesso senza alcun impatto sugli altri servizi o applicazioni.

Le entità servizio dovrebbero essere usate anche quando l'[utente amministratore](container-registry-authentication.md#admin-account) del registro è disabilitato.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Eseguire l'autenticazione con l'entità servizio

Per avviare un contenitore nelle istanze di contenitore di Azure usando un'entità servizio, specificare l'ID per `--registry-username` e la password per `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti contengono altre informazioni sull'uso delle entità servizio e del Registro contenitori di Azure:

* [Autenticazione al Registro contenitori di Azure con entità servizio](container-registry-auth-service-principal.md)
* [Eseguire l'autenticazione con Registro contenitori di Azure dal servizio contenitore di Azure](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->
