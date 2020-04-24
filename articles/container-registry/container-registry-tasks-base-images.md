---
title: Aggiornamenti di immagini di base-attività
description: Informazioni sulle immagini di base per le immagini del contenitore di applicazioni e sul modo in cui un aggiornamento di un'immagine di base può attivare un'attività di Container Registry di Azure.
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

Questo articolo fornisce informazioni generali sugli aggiornamenti dell'immagine di base di un'applicazione e sul modo in cui questi aggiornamenti possono attivare un'attività Container Registry di Azure.

## <a name="what-are-base-images"></a>Che cosa sono le immagini di base?

Dockerfile che definiscono la maggior parte delle immagini del contenitore specificano un'immagine padre da cui si basa l'immagine, spesso definita *immagine di base*. Le immagini di base contengono in genere il sistema operativo, ad esempio [Alpine Linux][base-alpine] o [Windows Server Nano][base-windows], su cui vengono applicati i restanti livelli del contenitore. Questi possono includere anche framework applicazioni come [Node.js][base-node] o [.NET Core][base-dotnet]. Queste immagini di base sono in genere basate su immagini upstream pubbliche. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Un'immagine di base viene spesso aggiornata dal gestore delle immagini per includere nuove funzionalità o nuovi miglioramenti del sistema operativo o del framework. Un'altra comune causa di aggiornamento dell'immagine di base è costituita dalle patch di sicurezza. Quando si verificano questi aggiornamenti upstream, è necessario aggiornare anche le immagini di base per includere la correzione critica. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

In alcuni casi, ad esempio un team di sviluppo privato, un'immagine di base potrebbe specificare più del sistema operativo o del Framework. Un'immagine di base, ad esempio, può essere un'immagine del componente del servizio condivisa che deve essere rilevata. I membri di un team potrebbero dover tenere traccia dell'immagine di base per il test o aggiornare periodicamente l'immagine durante lo sviluppo di immagini dell'applicazione.

## <a name="track-base-image-updates"></a>Tenere traccia degli aggiornamenti delle immagini di base

Attività del Registro Azure Container include la possibilità di compilare automaticamente le immagini quando viene aggiornata l'immagine di base di un contenitore.

Le attività ACR individuano dinamicamente le dipendenze delle immagini di base durante la compilazione di un'immagine del contenitore. Di conseguenza, può rilevare quando viene aggiornata l'immagine di base di un'immagine dell'applicazione. Con un'attività di compilazione preconfigurata, le attività ACR possono ricompilare automaticamente ogni immagine dell'applicazione che fa riferimento all'immagine di base. Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

## <a name="base-image-locations"></a>Percorsi delle immagini di base

Per la creazione di immagini da un documento Dockerfile, un'attività di Registro Azure Container rileva le dipendenze da immagini di base nelle posizioni seguenti:

* La stessa istanza di Registro Azure Container in cui viene eseguita l'attività
* Un altro registro contenitori di Azure privato nella stessa area o in un'area diversa 
* Un repository pubblico in Docker Hub 
* Un repository pubblico in Registro contenitori di Microsoft

Se l'immagine di base specificata nell' `FROM` istruzione risiede in uno di questi percorsi, l'attività ACR aggiunge un hook per assicurarsi che l'immagine venga ricompilata ogni volta che viene aggiornata la base.

## <a name="additional-considerations"></a>Considerazioni aggiuntive

* **Immagini di base per le immagini dell'applicazione** : attualmente, un'attività ACR tiene traccia solo degli aggiornamenti delle immagini di base per le immagini dell'applicazione (*Runtime*). Non tiene traccia degli aggiornamenti delle immagini di base per le immagini intermedie (*buildtime*) usate in Dockerfile in più fasi.  

* **Abilitata per impostazione predefinita** : quando si crea un'attività ACR con il comando [AZ ACR task create][az-acr-task-create] , per impostazione predefinita l'attività è *abilitata* per il trigger da un aggiornamento dell'immagine di base. Ciò significa che la proprietà `base-image-trigger-enabled` è impostata su True. Se si vuole disabilitare questo comportamento in un'attività, aggiornare la proprietà impostandola su False. Eseguire ad esempio il comando [az acr task update][az-acr-task-update] seguente:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Trigger per tenere traccia delle dipendenze** : per consentire a un'attività ACR di determinare e tenere traccia delle dipendenze di un'immagine del contenitore, tra cui l'immagine di base, è necessario innanzitutto attivare l'attività per compilare l'immagine almeno **una volta**. Attivare ad esempio l'attività manualmente usando il comando [az acr task run][az-acr-task-run].

* **Tag stabile per l'immagine di base** : per attivare un'attività nell'aggiornamento di un'immagine di base, è necessario che l'immagine di `node:9-alpine`base disponga di un tag *stabile* , ad esempio. Questo tag è tipico per un'immagine di base che viene aggiornata con le patch di sistema operativo e framework all'ultima versione stabile. Se l'immagine di base viene aggiornata con un nuovo tag di versione, l'attività non viene attivata. Per altre informazioni sull'uso di tag per le immagini, vedere le [procedure consigliate](container-registry-image-tag-version.md). 

* **Altri trigger di attività** : in un'attività attivata dagli aggiornamenti di un'immagine di base, è anche possibile abilitare i trigger in base al [commit del codice sorgente](container-registry-tutorial-build-task.md) o [a una pianificazione](container-registry-tasks-scheduled.md). Un aggiornamento di un'immagine di base può inoltre attivare un'attività in più [passaggi](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere le esercitazioni seguenti per gli scenari per automatizzare le compilazioni di immagini dell'applicazione dopo l'aggiornamento di un'immagine di base:

* [Automatizza le compilazioni di immagini del contenitore quando un'immagine di base viene aggiornata nello stesso registro](container-registry-tutorial-base-image-update.md)

* [Automatizza le compilazioni di immagini del contenitore quando un'immagine di base viene aggiornata in un registro di sistema diverso](container-registry-tutorial-base-image-update.md)


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
