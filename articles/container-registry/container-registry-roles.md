---
title: Ruoli e autorizzazioni RBAC
description: Usare il controllo degli accessi in base al ruolo di Azure e gestione delle identità e degli accessi per concedere autorizzazioni con granularità fine alle risorse in un Registro Azure Container.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893485"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Ruoli e autorizzazioni di Registro Azure Container

Il servizio Azure Container Registry supporta un set di [ruoli di Azure predefiniti](../role-based-access-control/built-in-roles.md) che forniscono diversi livelli di autorizzazioni per un registro contenitori di Azure. Usare il [controllo degli accessi in base al ruolo](../role-based-access-control/index.yml) (RBAC) di Azure per assegnare autorizzazioni specifiche a utenti, entità servizio o altre identità che devono interagire con un registro. 

| Ruolo/autorizzazione       | [Accedere ad Azure Resource Manager](#access-resource-manager) | [Creare/eliminare registro di sistema](#create-and-delete-registry) | [Eseguire il push dell'immagine](#push-image) | [Eseguire il pull dell'immagine](#pull-image) | [Elimina dati immagine](#delete-image-data) | [Modificare i criteri](#change-policies) |   [Firma delle immagini](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietario | X | X | X | X | X | X |  |  
| Collaboratore | X | X | X |  X | X | X |  |  
| Reader | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Distinguere gli utenti e i servizi

Ogni volta che vengono applicate le autorizzazioni, una procedura consigliata è quella di fornire il set più limitato di autorizzazioni a una persona, o a un servizio, per svolgere un compito. I set di autorizzazioni seguenti rappresentano un set di funzionalità che può essere usato da persone e servizi headless.

### <a name="cicd-solutions"></a>Soluzioni CI/CD

Quando si automatizzano i comandi `docker build` dalle soluzioni CI/CD, sono necessarie le funzionalità `docker push`. Per questi scenari di servizio headless, è consigliabile assegnare il ruolo **AcrPush**. Questo ruolo, a differenza del ruolo più ampio **Collaboratore**, impedisce all'account di eseguire altre operazioni del registro di sistema o l'accesso ad Azure Resource Manager.

### <a name="container-host-nodes"></a>Nodi dell'host del contenitore

Allo stesso modo, i nodi che eseguono i contenitori necessitano il ruolo **AcrPull**, ma richiedono funzionalità **Lettore**.

### <a name="visual-studio-code-docker-extension"></a>Estensione Visual Studio Code Docker

Per gli strumenti come [estensione Docker](https://code.visualstudio.com/docs/azure/docker) di Visual Studio Code, è richiesto l'accesso aggiuntivo al provider di risorse per elencare i registri contenitori Azure disponibili. In questo caso, fornire agli utenti l'accesso al ruolo **Lettore** oppure **Collaboratore**. Questi ruoli consentono `docker pull`, `docker push`, `az acr list`, `az acr build` e altre funzionalità. 

## <a name="access-resource-manager"></a>Accedere ad Azure Resource Manager

L'accesso ad Azure Resource Manager è necessario per il portale di Azure e per la gestione del registro di sistema con l'[interfaccia della riga di comando di Azure](/cli/azure/). Ad esempio, per ottenere un elenco dei registri usando il comando `az acr list`, è necessario questo set di autorizzazioni. 

## <a name="create-and-delete-registry"></a>Creare ed eliminare un registro di sistema

La possibilità di creare ed eliminare i registri contenitori di Azure.

## <a name="push-image"></a>Immagine di push

La possibilità di `docker push` un'immagine o di eseguire il push su un altro [artefatto supportato](container-registry-image-formats.md), ad esempio un grafico Helm, in un registro di sistema. Richiede l'[autenticazione](container-registry-authentication.md) con il registro di sistema usando l'identità autorizzata. 

## <a name="pull-image"></a>Eseguire il pull dell'immagine

La possibilità di `docker pull` un'immagine non in quarantena o di eseguire il pull su un altro [artefatto supportato](container-registry-image-formats.md), ad esempio un grafico Helm, in un registro di sistema. Richiede l'[autenticazione](container-registry-authentication.md) con il registro di sistema usando l'identità autorizzata.

## <a name="delete-image-data"></a>Eliminare i dati di immagini

La possibilità di [eliminare immagini del contenitore](container-registry-delete.md)o di eliminare altri [elementi supportati](container-registry-image-formats.md) , ad esempio i grafici Helm, da un registro di sistema.

## <a name="change-policies"></a>Modificare i criteri

La possibilità di configurare i criteri in un registro di sistema. I criteri includono l'eliminazione di immagini, l'abilitazione della quarantena e la firma di immagini.

## <a name="sign-images"></a>Firma di immagini

La possibilità di firmare immagini, in genere assegnate a un processo automatizzato, che utilizza un'entità di servizio. Questa autorizzazione viene in genere combinata con un'[immagine push](#push-image) per consentire l'inserimento di un'immagine attendibile a un registro di sistema. Per informazioni dettagliate, vedere [Attendibilità dei contenuti in Registro Azure Container](container-registry-content-trust.md).

## <a name="custom-roles"></a>Ruoli personalizzati

Come per le altre risorse di Azure, è possibile creare [ruoli personalizzati](../role-based-access-control/custom-roles.md) con le autorizzazioni con granularità fine per Azure container Registry. Assegnare quindi i ruoli personalizzati a utenti, entità servizio o altre identità che devono interagire con un registro. 

Per determinare le autorizzazioni da applicare a un ruolo personalizzato, vedere l'elenco di [azioni](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft. ContainerRegistry, esaminare le azioni consentite dei [ruoli predefiniti di ACR](../role-based-access-control/built-in-roles.md)oppure eseguire il comando seguente:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Per definire un ruolo personalizzato, vedere [passaggi per la creazione di un ruolo personalizzato](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> In un ruolo personalizzato, Azure Container Registry attualmente non supporta i caratteri jolly, ad esempio `Microsoft.ContainerRegistry/*` o `Microsoft.ContainerRegistry/registries/*` che concedono l'accesso a tutte le azioni corrispondenti. Specificare un'azione obbligatoria singolarmente nel ruolo.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'assegnazione dei ruoli RBAC a un'identità di Azure usando il [portale di Azure](../role-based-access-control/role-assignments-portal.md), l'[interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md), o altri strumenti di Azure.

* Informazioni su [opzioni di autenticazione](container-registry-authentication.md) per il Registro Azure Container.

* Informazioni su come abilitare le [autorizzazioni con ambito repository](container-registry-repository-scoped-permissions.md) (anteprima) in un registro contenitori.