---
title: Formati di contenuto di Registro Azure Container
description: Informazioni sui formati di contenuto supportati in Registro Azure Container.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310685"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formati di contenuto supportati in Registro Azure Container

Usare un repository privato in Registro Azure Container per gestire uno dei formati di contenuto seguenti. 

## <a name="docker-compatible-container-images"></a>Immagini di contenitori compatibili con Docker

Sono supportati i seguenti formati di immagini del contenitore docker:

* [Manifesto immagine Docker V2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto immagine Docker V2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - Include elenchi di manifesti che consentono ai registri di archiviare immagini multipiattaforma con un unico riferimento "image:tag"

## <a name="oci-images"></a>Immagini OCI

Azure Container Registry supporta anche immagini che soddisfano la [specifica del formato di immagine OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md). I formati di packaging includono il [formato SIF (Singularity Image Format)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Grafici Helm

Azure Container Registry può ospitare repository per i [grafici Helm](https://helm.sh/), un formato di creazione di pacchetti usato per gestire e distribuire rapidamente le applicazioni per Kubernetes. Sono supportati i [client Helm](https://docs.helm.sh/using_helm/#installing-helm) versione 2.11.0 o successiva.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [eseguire il push e il pull](container-registry-get-started-docker-cli.md) delle immagini con Registro contenitori di Azure.

* Usare [Attività di Registro Azure Container](container-registry-tasks-overview.md) per creare e testare immagini dei contenitori. 

* Usare [Moby BuildKit](https://github.com/moby/buildkit) per la compilazione e la creazione di pacchetti dei contenitori in formato OCI.

* Configurare un [repository Helm](container-registry-helm-repos.md) ospitato in Registro Azure Container. 


