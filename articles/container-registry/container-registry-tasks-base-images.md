---
title: Aggiornamenti delle immagini di base - Attività
description: Informazioni sulle immagini di base per le immagini del contenitore dell'applicazione e su come un aggiornamento delle immagini di base può attivare un'attività del Registro di sistema del contenitore di Azure.Learn about base images for application container images, and about how a base image update can trigger an Azure Container Registry task.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617931"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Informazioni sugli aggiornamenti delle immagini di base per le attività ACR

Questo articolo fornisce informazioni di base sugli aggiornamenti all'immagine di base di un'applicazione e su come questi aggiornamenti possono attivare un'attività del Registro di sistema del contenitore di Azure.This article provides background information about updates to an application's base image and how these updates can trigger an Azure Container Registry task.

## <a name="what-are-base-images"></a>Cosa sono le immagini di base?

Dockerfiles che definiscono la maggior parte delle immagini contenitore specificano un'immagine padre da cui si basa l'immagine, spesso definita immagine di *base.* Le immagini di base contengono in genere il sistema operativo, ad esempio [Alpine Linux][base-alpine] o [Windows Server Nano][base-windows], su cui vengono applicati i restanti livelli del contenitore. Questi possono includere anche framework applicazioni come [Node.js][base-node] o [.NET Core][base-dotnet]. Queste immagini di base sono in genere basate su immagini a monte pubbliche. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Un'immagine di base viene spesso aggiornata dal gestore delle immagini per includere nuove funzionalità o nuovi miglioramenti del sistema operativo o del framework. Un'altra comune causa di aggiornamento dell'immagine di base è costituita dalle patch di sicurezza. Quando si verificano questi aggiornamenti a monte, è necessario aggiornare anche le immagini di base per includere la correzione critica. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

In alcuni casi, ad esempio un team di sviluppo privato, un'immagine di base potrebbe specificare più di OS o framework. Ad esempio, un'immagine di base potrebbe essere un'immagine del componente del servizio condiviso che deve essere traccia. I membri di un team potrebbero dover tenere traccia di questa immagine di base per il test o dover aggiornare regolarmente l'immagine durante lo sviluppo di immagini dell'applicazione.

## <a name="track-base-image-updates"></a>Tenere traccia degli aggiornamenti delle immagini di base

Attività del Registro Azure Container include la possibilità di compilare automaticamente le immagini quando viene aggiornata l'immagine di base di un contenitore.

Attività ACR rileva dinamicamente le dipendenze dell'immagine di base quando crea un'immagine contenitore. Di conseguenza, è in grado di rilevare quando viene aggiornata l'immagine di base di un'immagine dell'applicazione. Con un'attività di compilazione preconfigurata, le attività ACR possono ricompilare automaticamente ogni immagine dell'applicazione che fa riferimento all'immagine di base. Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

## <a name="base-image-locations"></a>Posizioni delle immagini di base

Per la creazione di immagini da un documento Dockerfile, un'attività di Registro Azure Container rileva le dipendenze da immagini di base nelle posizioni seguenti:

* La stessa istanza di Registro Azure Container in cui viene eseguita l'attività
* Un altro registro contenitore di Azure privato nella stessa area o in un'area diversa 
* Un repository pubblico in Docker Hub 
* Un repository pubblico in Registro contenitori di Microsoft

Se l'immagine di `FROM` base specificata nell'istruzione si trova in una di queste posizioni, l'attività ACR aggiunge un hook per garantire che l'immagine venga ricompilata ogni volta che la relativa base viene aggiornata.

## <a name="additional-considerations"></a>Altre considerazioni

* Immagini di base per le **immagini dell'applicazione:** attualmente, un'attività ACR tiene traccia solo degli aggiornamenti delle immagini di base per le immagini dell'applicazione (*runtime).* Non tiene traccia degli aggiornamenti delle immagini di base per le immagini intermedie (*buildtime*) usate in Dockerfile in più fasi.  

* **Abilitato per impostazione predefinita:** quando si crea un'attività ACR con il comando [az acr task create,][az-acr-task-create] per impostazione predefinita l'attività viene *abilitata* per il trigger da un aggiornamento dell'immagine di base. Ciò significa che la proprietà `base-image-trigger-enabled` è impostata su True. Se si vuole disabilitare questo comportamento in un'attività, aggiornare la proprietà impostandola su False. Eseguire ad esempio il comando [az acr task update][az-acr-task-update] seguente:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Attivare per tenere traccia delle dipendenze:** per consentire a un'attività ACR di determinare e tenere traccia delle dipendenze di un'immagine contenitore, che includono l'immagine di base, è necessario innanzitutto attivare l'attività per compilare l'immagine **almeno una volta.** Attivare ad esempio l'attività manualmente usando il comando [az acr task run][az-acr-task-run].

* **Tag stabile per l'immagine** di base : per attivare un'attività durante `node:9-alpine`l'aggiornamento dell'immagine di base, l'immagine di base deve avere un tag *stabile,* ad esempio . Questo tag è tipico per un'immagine di base che viene aggiornata con le patch di sistema operativo e framework all'ultima versione stabile. Se l'immagine di base viene aggiornata con un nuovo tag di versione, l'attività non viene attivata. Per altre informazioni sull'uso di tag per le immagini, vedere le [procedure consigliate](container-registry-image-tag-version.md). 

* **Altri trigger di attività:** in un'attività attivata dagli aggiornamenti delle immagini di base, è anche possibile abilitare i trigger in base al commit del [codice sorgente](container-registry-tutorial-build-task.md) o a [una pianificazione.](container-registry-tasks-scheduled.md) Un aggiornamento dell'immagine di base può anche attivare [un'attività a più passaggi.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere le esercitazioni seguenti per gli scenari per automatizzare le compilazioni di immagini dell'applicazione dopo l'aggiornamento di un'immagine di base:See the following tutorials for scenarios to automate application image builds after a base image is updated:

* [Automatizzare le compilazioni di immagini del contenitore quando un'immagine di base viene aggiornata nello stesso Registro di sistemaAutomate container image builds when a base image is updated in the same registry](container-registry-tutorial-base-image-update.md)

* [Automatizzare le compilazioni di immagini del contenitore quando un'immagine di base viene aggiornata in un Registro di sistema diversoAutomate container image builds when a base image is updated in a different registry](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
