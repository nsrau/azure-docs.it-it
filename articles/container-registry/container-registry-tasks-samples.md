---
title: Esempi di attività ACR
description: Attività del Registro di sistema del contenitore di Azure di esempio (attività ACR) per compilare, eseguire e applicare patch alle immagini del contenitore
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456089"
---
# <a name="acr-tasks-samples"></a>Esempi di attività ACR

Questo articolo contiene `task.yaml` collegamenti a file di esempio e dockerfile associati per diversi scenari di attività del Registro di [sistema contenitore](container-registry-tasks-overview.md) di Azure.This article links to example files and associated Dockerfiles for several Azure Container Registry Tasks (ACR Tasks) scenarios. 

Per altri esempi, vedere il repository degli esempi di [Azure.For][task-examples] additional examples, see the Azure samples repo.

## <a name="scenarios"></a>Scenari

* **Immagine di compilazione**[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)  - 

* **Contenitore di esecuzione** - [YAMLRun container YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Compilare e spingere l'immagine** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare ed eseguire l'immagine** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare e spingere più immagini** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare e testare le immagini in parallelo** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Creare e inviare** - immagini a più registri[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle attività ACR:

* [Attività in più passaggi:](container-registry-tasks-multi-step.md) flussi di lavoro basati su attività ACR per la creazione, il test e l'applicazione di patch alle immagini dei contenitori nel cloud.
* [Task reference](container-registry-tasks-reference-yaml.md) (Riferimenti alle attività): tipi, proprietà e utilizzo dei passaggi di attività.
* [Cmd repo](https://github.com/AzureCR/cmd) : raccolta di contenitori come comandi per le attività ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
