---
title: Automatizzare l'applicazione di patch al sistema operativo e al framework con ACR Build
description: Introduzione ad ACR Build, un gruppo di funzionalità in Registro contenitori di Azure che consente di eseguire la compilazione di immagini dei contenitori e l'applicazione di patch nel cloud in modo automatico e sicuro.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: 63bbd9b5711330207c34ac4aa05aac3a71304653
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413580"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework con ACR Build

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. È comunque necessario definire, tuttavia, come applicare patch alla virtualizzazione delle applicazioni.

**ACR Build** è una suite di funzionalità nel Registro contenitori di Azure. Fornisce funzionalità per la creazione di immagini di contenitore basate su cloud per Linux, Windows e ARM e ed è in grado di automatizzare l'[applicazione di patch a sistemi operativi e framework](#automate-os-and-framework-patching) per i contenitori Docker.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Informazioni su ACR Build

La funzionalità di compilazione di Registro contenitori di Azure è un servizio nativo di Azure per la compilazione di immagini dei contenitori. ACR Build supporta il ciclo interno di sviluppo nel cloud con compilazioni di immagini dei contenitori su richiesta e compilazioni automatiche in caso di commit del codice sorgente e aggiornamento dell'immagine di base.

È possibile attivare compilazioni delle immagini dei contenitori quando viene eseguito il commit di codice in un repository Git oppure quando viene aggiornata l'immagine di base di un contenitore. Con i trigger correlati all'aggiornamento dell'immagine di base, è possibile automatizzare il flusso di lavoro dell'applicazione di patch al sistema operativo e al framework applicazioni mantenendo gli ambienti sicuri e rispettando le entità di sicurezza di contenitori non modificabili.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Compilazione rapida: estensione del ciclo interno al cloud

La gestione del ciclo di vita ha inizio prima che gli sviluppatori eseguano il commit delle prime righe di codice. La funzionalità di [compilazione rapida](container-registry-tutorial-quick-build.md) di ACR Build consente un'esperienza di sviluppo integrata a livello di ciclo interno locale con offload delle compilazioni in Azure. Con questa funzionalità, è possibile verificare le definizioni delle compilazioni automatiche prima di eseguire il commit di codice.

Usando il noto formato `docker build`, il comando [az acr build][az-acr-build] dell'interfaccia della riga di comando di Azure accetta un **contesto** (l'insieme di file da compilare), lo invia al servizio ACR Build e al termine, per impostazione predefinita, esegue il push dell'immagine compilata nel registro.

La tabella seguente mostra alcuni esempi di percorsi di contesto supportati per ACR Build:

| Posizione contesto | DESCRIZIONE | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo master GitHub | File nel master (o altra impostazione predefinita) di un repository GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Ramo specifico di un repository GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Richiesta pull GitHub | Richiesta pull in un repository GitHub. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub. L'esempio mostra una combinazione di richieste pull e specifiche della sottocartella. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |

ACR Build segue anche i registri con replica geografica, consentendo a team di sviluppo in località diverse di sfruttare il registro replicato più vicino.

ACR Build è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Build in una soluzione di integrazione continua/distribuzione continua. Eseguendo [az login][az-login] con un' [entità servizio][az-login-service-principal], la soluzione di integrazione continua/distribuzione continua potrà quindi eseguire comandi [az acr build][az-acr-build] per avviare compilazioni delle immagini.

Per informazioni su come usare la compilazione rapida, vedere la prima esercitazione su ACR Build, [Compilare immagini dei contenitori nel cloud con ACR Build](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Compilazione automatica in caso di commit del codice sorgente

Usare ACR Build per attivare automaticamente una compilazione dell'immagine del contenitore quando viene eseguito il commit di codice in un repository Git. Le attività di compilazione, configurabili con il comando [az acr build-task][az-acr-build-task] dell'interfaccia della riga di comando di Azure, consentono di specificare un repository Git e, facoltativamente, un ramo e un Dockerfile. Quando il team esegue il commit di codice nel repository, un webhook creato da ACR Build attiva una compilazione dell'immagine del contenitore definita nel repository.

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su ACR Build, [Automatizzare la compilazione di immagini dei contenitori con ACR Build](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

ACR Build può realmente migliorare la pipeline di compilazione dei contenitori grazie alla capacità di rilevare un aggiornamento di un'immagine di base. Quando viene eseguito il push dell'immagine di base aggiornata nel registro, ACR Build può compilare automaticamente tutte le immagini di applicazioni basate su di essa.

Le immagini dei contenitori possono essere suddivise in linea di massima in immagini di *base* e immagini di *applicazioni*. Le immagini di base includono in genere il sistema operativo e i framework applicazioni su cui è basata l'applicazione, insieme ad altre personalizzazioni. Tali immagini di base sono in genere basate a propria volta su immagini upstream pubbliche, ad esempio [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet] o [Node.js][base-node]. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Quando un'immagine del sistema operativo o del framework app viene aggiornata dal gestore upstream, ad esempio con una patch di sicurezza critica del sistema operativo, è necessario aggiornare anche le immagini di base in modo da includere l'aggiornamento critico. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

Individuando dinamicamente le dipendenze dell'immagine di base durante la compilazione di un'immagine del contenitore, ACR Build può rilevare l'aggiornamento dell'immagine di base di un'immagine di applicazione. Con un'[attività di compilazione](container-registry-tutorial-base-image-update.md#create-build-task) preconfigurata, ACR Build **ricompila quindi automaticamente ogni immagine di applicazione**. Grazie al rilevamento e alla ricompilazione automatici, ACR Build consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

Per informazioni sull'applicazione di patch al sistema operativo e al framework, vedere la terza esercitazione su ACR Build, [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Build](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Durante l'anteprima iniziale, gli aggiornamenti delle immagini di base attivano compilazioni solo quando sia l'immagine di base che quella dell'applicazione risiedono nello stesso registro contenitori di Azure o in repository dell'hub Docker accessibili pubblicamente.

## <a name="next-steps"></a>Passaggi successivi

Quando si è pronti ad automatizzare l'applicazione di patch al sistema operativo e al framework compilando le immagini dei contenitori nel cloud, vedere la serie di esercitazioni in tre parti su ACR Build.

> [!div class="nextstepaction"]
> [Compilare immagini dei contenitori nel cloud con ACR Build](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
