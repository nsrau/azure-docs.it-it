---
title: Formati di contenuto di Registro Azure Container
description: Informazioni sui formati di contenuto supportati in Registro Azure Container.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827460"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formati di contenuto supportati in Registro Azure Container

Usare un repository privato in Registro Azure Container per gestire uno dei formati di contenuto seguenti. 

## <a name="docker-compatible-container-images"></a>Immagini di contenitori compatibili con Docker

Sono supportati i formati di immagine contenitore Docker seguenti:

* [Manifesto immagine Docker V2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto immagine Docker V2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - Include elenchi di manifesti che consentono ai registri di archiviare immagini multipiattaforma con un unico riferimento "image:tag"

## <a name="oci-images"></a>Immagini OCI

Registro contenitori di Azure supporta anche le immagini che soddisfano le [iniziativa OCI (Open Container Initiative) specifica del formato immagine](https://github.com/opencontainers/image-spec/blob/master/spec.md). Formato dei pacchetti include [Singolarità dell'immagine del formato (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Grafici Helm

Registro contenitori di Azure Puoi ospitare i repository per [i grafici Helm](https://helm.sh/), un formato di pacchetto consente di gestire e distribuire rapidamente applicazioni per Kubernetes. Sono supportati i [client Helm](https://docs.helm.sh/using_helm/#installing-helm) versione 2.11.0 o successiva.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [eseguire il push e il pull](container-registry-get-started-docker-cli.md) delle immagini con Registro contenitori di Azure.

* Usare [Attività di Registro Azure Container](container-registry-tasks-overview.md) per creare e testare immagini dei contenitori. 

* Usare [Moby BuildKit](https://github.com/moby/buildkit) per la compilazione e la creazione di pacchetti dei contenitori in formato OCI.

* Configurare un [repository Helm](container-registry-helm-repos.md) ospitato in Registro Azure Container. 


