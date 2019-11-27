---
title: Formati di contenuto supportati
description: Informazioni sui formati di contenuto supportati da Azure Container Registry, incluse le immagini del contenitore compatibili con Docker, i grafici Helm, le immagini OCI e gli artefatti OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455013"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formati di contenuto supportati in Registro Azure Container

Usare un repository privato in Registro Azure Container per gestire uno dei formati di contenuto seguenti. 

## <a name="docker-compatible-container-images"></a>Immagini di contenitori compatibili con Docker

Sono supportati i seguenti formati di immagini del contenitore docker:

* [Manifesto immagine Docker V2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto immagine Docker V2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - Include elenchi di manifesti che consentono ai registri di archiviare immagini multipiattaforma con un unico riferimento "image:tag"

## <a name="oci-images"></a>Immagini OCI

Azure Container Registry supporta le immagini che soddisfano la [specifica del formato di immagine OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md). I formati di packaging includono il [formato SIF (Singularity Image Format)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Elementi OCI

Azure Container Registry supporta la [specifica di distribuzione OCI](https://github.com/opencontainers/distribution-spec), una specifica indipendente dal fornitore e indipendente dal cloud per archiviare, condividere, proteggere e distribuire immagini del contenitore e altri tipi di contenuto (elementi). La specifica consente a un registro di archiviare un'ampia gamma di elementi oltre alle immagini del contenitore. Si usano gli strumenti appropriati per l'artefatto per eseguire il push e il pull degli artefatti. Per un esempio, vedere effettuare il [push e il pull di un elemento OCI usando un registro contenitori di Azure](container-registry-oci-artifacts.md).

Per ulteriori informazioni sugli artefatti OCI, vedere il repository [OCI Registry As Storage (Oras)](https://github.com/deislabs/oras) e il repository di [artefatti OCI](https://github.com/opencontainers/artifacts) su GitHub.

## <a name="helm-charts"></a>Grafici Helm

Azure Container Registry può ospitare repository per i [grafici Helm](https://helm.sh/), un formato di creazione di pacchetti usato per gestire e distribuire rapidamente le applicazioni per Kubernetes. Il [client Helm](https://docs.helm.sh/using_helm/#installing-helm) versione 2 (2.11.0 o versione successiva) è supportato.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [eseguire il push e il pull](container-registry-get-started-docker-cli.md) delle immagini con Registro Azure Container.

* Usare [Attività di Registro Azure Container](container-registry-tasks-overview.md) per creare e testare immagini dei contenitori. 

* Usare [Moby BuildKit](https://github.com/moby/buildkit) per la compilazione e la creazione di pacchetti dei contenitori in formato OCI.

* Configurare un [repository Helm](container-registry-helm-repos.md) ospitato in Registro Azure Container. 


