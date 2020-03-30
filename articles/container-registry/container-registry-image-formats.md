---
title: Formati di contenuto supportati
description: Informazioni sui formati di contenuto supportati dal Registro di sistema dei contenitori di Azure, incluse le immagini dei contenitori compatibili con Docker, i grafici Helm, le immagini OCI e gli elementi OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247007"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formati di contenuto supportati in Registro Azure Container

Usare un repository privato in Registro Azure Container per gestire uno dei formati di contenuto seguenti. 

## <a name="docker-compatible-container-images"></a>Immagini di contenitori compatibili con Docker

Sono supportati i seguenti formati di immagine del contenitore Docker:

* [Manifesto immagine Docker V2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto immagine Docker V2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - Include elenchi di manifesti che consentono ai registri di archiviare immagini multipiattaforma con un unico riferimento "image:tag"

## <a name="oci-images"></a>Immagini OCI

Azure Container Registry supporta immagini che soddisfano la specifica del [formato immagine Open Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md) I formati di creazione di pacchetti includono [Singularity Image Format (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefatti OCI

Azure Container Registry supporta la specifica di [distribuzione OCI](https://github.com/opencontainers/distribution-spec), una specifica indipendente dal fornitore e indipendente dal cloud per archiviare, condividere, proteggere e distribuire immagini di contenitori e altri tipi di contenuto (elementi). La specifica consente a un Registro di sistema di archiviare un'ampia gamma di elementi oltre alle immagini del contenitore. Utilizzare gli strumenti appropriati per l'elemento per eseguire il push e il pull degli artefatti. Per un esempio, vedere Eseguire il push ed eseguire il pull di [un elemento OCI usando un registro contenitori](container-registry-oci-artifacts.md)di Azure.For an example, see Push and pull an OCI artifact using an Azure container registry .

Per altre informazioni sugli elementi OCI, vedere il [repository OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) e il repository OCI Artifacts su GitHub.To learn more about OCI artifacts, see the OCI Registry as Storage (ORAS) repo and the [OCI Artifacts](https://github.com/opencontainers/artifacts) repo on GitHub.

## <a name="helm-charts"></a>Grafici Helm

Il Registro di sistema contenitori di Azure può ospitare repository per [i grafici Helm,](https://helm.sh/)un formato di creazione di pacchetti usato per gestire e distribuire rapidamente le applicazioni per Kubernetes. [Helm client](https://docs.helm.sh/using_helm/#installing-helm) versione 2 (2.11.0 o versione successiva) è supportato.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [eseguire il push e il pull](container-registry-get-started-docker-cli.md) delle immagini con Registro Azure Container.

* Usare [Attività di Registro Azure Container](container-registry-tasks-overview.md) per creare e testare immagini dei contenitori. 

* Usare [Moby BuildKit](https://github.com/moby/buildkit) per la compilazione e la creazione di pacchetti dei contenitori in formato OCI.

* Configurare un [repository Helm](container-registry-helm-repos.md) ospitato in Registro Azure Container. 


