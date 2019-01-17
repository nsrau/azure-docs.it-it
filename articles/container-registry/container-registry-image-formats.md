---
title: Formati di contenuto di Registro Azure Container
description: Informazioni sui formati di contenuto supportati in Registro Azure Container.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634845"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formati di contenuto supportati in Registro Azure Container

Usare un repository privato in Registro Azure Container per gestire uno dei formati di contenuto seguenti. 

## <a name="docker-compatible-container-images"></a>Immagini di contenitori compatibili con Docker

* [Manifesto immagine Docker V2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto immagine Docker V2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - Include elenchi di manifesti che consentono ai registri di archiviare immagini multipiattaforma con un unico riferimento "image:tag"

* [Specifica di formato di immagine OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Grafici Helm

Registro Azure Container può anche ospitare i repository per i [grafici Helm](https://helm.sh/), un formato di creazione di pacchetti usato per gestire e distribuire rapidamente le applicazioni per Kubernetes. Sono supportati i [client Helm](https://docs.helm.sh/using_helm/#installing-helm) versione 2.11.0 o successiva.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [eseguire il push e il pull](container-registry-get-started-docker-cli.md) delle immagini con Registro Azure Container.

* Usare [Attività di Registro Azure Container](container-registry-tasks-overview.md) per creare e testare immagini dei contenitori. 

* Usare [Moby BuildKit](https://github.com/moby/buildkit) per la compilazione e la creazione di pacchetti dei contenitori in formato OCI.

* Configurare un [repository Helm](container-registry-helm-repos.md) ospitato in Registro Azure Container. 


