---
title: Registro Azure Container - ruoli e autorizzazioni
description: Usare il controllo degli accessi in base al ruolo di Azure (RBAC) e gestione delle identità e degli accessi (IAM) per concedere autorizzazioni con granularità fine alle risorse in un registro contenitori di Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/20/2019
ms.author: danlep
ms.openlocfilehash: 0148894bb013dc9f8cce595f14919f87d6292df8
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593625"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Ruoli e autorizzazioni di Registro Azure Container

Il servizio Registro Azure Container supporta un set di ruoli di Azure che offrono livelli diversi di autorizzazioni a un registro contenitori di Azure. Usare il [controllo degli accessi in base al ruolo](../role-based-access-control/index.yml) (RBAC) di Azure per assegnare autorizzazioni specifiche agli utenti o a entità che devono interagire con un registro di sistema.

| Ruolo/autorizzazione       | [Accedere ad Azure Resource Manager](#access-resource-manager) | [Creare/eliminare registro di sistema](#create-and-delete-registry) | [Eseguire il push dell'immagine](#push-image) | [Eseguire il pull dell'immagine](#pull-image) | [Elimina dati di immagine](#delete-image-data) | [Modificare i criteri](#change-policies) |   [Firma delle immagini](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietario | X | X | X | X | X | X |  |  
| Collaboratore | X | X | X |  X | X | X |  |  
| Reader | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
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

La possibilità [eliminare le immagini del contenitore o un repository](container-registry-delete.md).

## <a name="change-policies"></a>Modificare i criteri

La possibilità di configurare i criteri in un registro di sistema. I criteri includono l'eliminazione di immagini, l'abilitazione della quarantena e la firma di immagini.

## <a name="sign-images"></a>Firma di immagini

La possibilità di firmare immagini, in genere assegnate a un processo automatizzato, che utilizza un'entità di servizio. Questa autorizzazione viene in genere combinata con un'[immagine push](#push-image) per consentire l'inserimento di un'immagine attendibile a un registro di sistema. Per informazioni dettagliate, vedere [Attendibilità dei contenuti in Registro Azure Container](container-registry-content-trust.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'assegnazione dei ruoli RBAC a un'identità di Azure usando il [portale di Azure](../role-based-access-control/role-assignments-portal.md), l'[interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md), o altri strumenti di Azure.

* Informazioni su [opzioni di autenticazione](container-registry-authentication.md) per il Registro Azure Container.
