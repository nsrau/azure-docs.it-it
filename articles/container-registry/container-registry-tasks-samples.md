---
title: Esempi di attività ACR
description: Attività di esempio di Azure Container Registry (attività ACR) per compilare, eseguire e applicare patch alle immagini del contenitore
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456089"
---
# <a name="acr-tasks-samples"></a>Esempi di attività ACR

Questo articolo contiene collegamenti ad esempi di file di `task.yaml` e Dockerfile associati per diversi scenari di [attività container Registry di Azure](container-registry-tasks-overview.md) (ACR). 

Per altri esempi, vedere il repository di [esempi di Azure][task-examples] .

## <a name="scenarios"></a>Scenari

* **Crea immagine** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Esegui contenitore** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Creazione e push di immagini** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare ed eseguire image** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Creazione e push di più immagini** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare e testare immagini in parallelo** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Creazione e push di immagini in più registri** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle attività di ACR:

* [Attività](container-registry-tasks-multi-step.md) in più passaggi: flussi di lavoro basati su attività ACR per la compilazione, il test e l'applicazione di patch alle immagini del contenitore nel cloud.
* [Task reference](container-registry-tasks-reference-yaml.md) (Riferimenti alle attività): tipi, proprietà e utilizzo dei passaggi di attività.
* [Repository cmd](https://github.com/AzureCR/cmd) : raccolta di contenitori come comandi per le attività ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
