---
title: Eseguire l'autenticazione con Registro contenitori di Azure dalle istanze di contenitore di Azure
description: Informazioni su come fornire l'accesso alle immagini nel registro del contenitore privato dalle istanze contenitore di Azure usando un'entità del servizio Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 32b1788d73e1c323d93b40b778bc64a1ba45c4ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855834"
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

## <a name="sample-scripts"></a>Script di esempio

Gli script di esempio precedenti per l'interfaccia della riga di comando di Azure sono disponibili in GitHub, così come le versioni per Azure PowerShell:

* [Interfaccia della riga di comando di Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti contengono altre informazioni sull'uso delle entità servizio e del Registro contenitori di Azure:

* [Autenticazione al Registro contenitori di Azure con entità servizio](container-registry-auth-service-principal.md)
* [Eseguire l'autenticazione con Registro contenitori di Azure da Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
