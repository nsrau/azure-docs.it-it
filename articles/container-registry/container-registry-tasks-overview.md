---
title: Panoramica delle Attività del Registro Azure Container
description: An introduction to ACR Tasks, a suite of features in Azure Container Registry that provides secure, automated container image build, management, and patching in the cloud.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456147"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automate container image builds and maintenance with ACR Tasks

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. What remains, however, is the need to address how this application virtualization is managed and patched over the container lifecycle.

## <a name="what-is-acr-tasks"></a>Informazioni su ACR Tasks

**ACR Tasks** è una suite di funzionalità in Registro Azure Container. It provides cloud-based container image building for [platforms](#image-platforms) including Linux, Windows, and ARM, and can automate [OS and framework patching](#automate-os-and-framework-patching) for your Docker containers. ACR Tasks not only extends your "inner-loop" development cycle to the cloud with on-demand container image builds, but also enables automated builds triggered by source code updates, updates to a container's base image, or timers. For example, with base image update triggers, you can automate your OS and application framework patching workflow, maintaining secure environments while adhering to the principles of immutable containers.

## <a name="task-scenarios"></a>Task scenarios

ACR Tasks supports several scenarios to build and maintain container images and other artifacts. See the following sections in this article for details.

* **[Quick task](#quick-task)** - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. Pensare `docker build`, `docker push` nel cloud.
* **Automatically triggered tasks** - Enable one or more *triggers* to build an image:
  * **[Trigger on source code update](#trigger-task-on-source-code-update)** 
  * **[Trigger on base image update](#automate-os-and-framework-patching)** 
  * **[Trigger on a schedule](#schedule-a-task)** 
* **[Multi-step task](#multi-step-tasks)** - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows. 

Each ACR Task has an associated [source code context](#context-locations) - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.

Tasks can also take advantage of [run variables](container-registry-tasks-reference-yaml.md#run-variables), so you can reuse task definitions and standardize tags for images and artifacts.

## <a name="quick-task"></a>Attività rapida

Il ciclo di sviluppo interno, processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente, è in realtà l'inizio della gestione del ciclo di vita del contenitore.

Prima di eseguire il commit della prima riga di codice, la funzione [attività rapida](container-registry-tutorial-quick-task.md) di Attività del Registro Azure Container può offrire un'esperienza di sviluppo integrato eseguendo l'offload delle build di un'immagine del contenitore in Azure. Con questa funzionalità è possibile verificare le definizioni delle compilazioni automatiche e rilevare problemi potenziali prima di eseguire il commit di codice.

Using the familiar `docker build` format, the [az acr build][az-acr-build] command in the Azure CLI takes a [context](#context-locations) (the set of files to build), sends it ACR Tasks and, by default, pushes the built image to its registry upon completion.

For an introduction, see the quickstart to [build and run a container image](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

ACR Tasks è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Tasks in una soluzione CI/CD. By executing [az login][az-login] with a [service principal][az-login-service-principal], your CI/CD solution could then issue [az acr build][az-acr-build] commands to kick off image builds.

Per informazioni su come usare le attività rapide, vedere la prima esercitazione su Attività del Registro Azure Container, [Compilare immagini dei contenitori nel cloud con Attività del Registro Azure Container](container-registry-tutorial-quick-task.md).

> [!TIP]
> If you want to build and push an image directly from source code, without a Dockerfile, Azure Container Registry provides the [az acr pack build][az-acr-pack-build] command (preview). This tool builds and pushes an image from application source code using [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Trigger task on source code update

Trigger a container image build or multi-step task when code is committed, or a pull request is made or updated, to a Git repository in GitHub or Azure DevOps. For example, configure a build task with the Azure CLI command [az acr task create][az-acr-task-create] by specifying a Git repository and optionally a branch and Dockerfile. When your team updates code in the repository, an ACR Tasks-created webhook triggers a build of the container image defined in the repo. 

ACR Tasks supports the following triggers when you set a Git repo as the task's context:

| Trigger | Enabled by default |
| ------- | ------------------ |
| Commit | SÌ |
| Pull request | No |

To configure the trigger, you provide the task a personal access token (PAT) to set the webhook in the GitHub or Azure DevOps repo.

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su Attività del Registro Azure Container, [Automatizzare la compilazione di immagini dei contenitori con Attività del Registro Azure Container](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

ACR Tasks può realmente migliorare il flusso di lavoro di compilazione dei contenitori grazie alla capacità di rilevare un aggiornamento di un'immagine di base. When the updated base image is pushed to your registry, or a base image is updated in a public repo such as in Docker Hub, ACR Tasks can automatically build any application images based on it.

Le immagini dei contenitori possono essere suddivise in linea di massima in immagini di *base* e immagini di *applicazioni*. Le immagini di base includono in genere il sistema operativo e i framework applicazioni su cui è basata l'applicazione, insieme ad altre personalizzazioni. These base images are themselves typically based on public upstream images, for example: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], or [Node.js][base-node]. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Quando un'immagine del sistema operativo o del framework app viene aggiornata dal gestore upstream, ad esempio con una patch di sicurezza critica del sistema operativo, è necessario aggiornare anche le immagini di base in modo da includere l'aggiornamento critico. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

Individuando dinamicamente le dipendenze dell'immagine di base durante la compilazione di un'immagine del contenitore, Attività del Registro Azure Container può rilevare l'aggiornamento dell'immagine di base di un'immagine di applicazione. Con un'[attività di compilazione](container-registry-tutorial-base-image-update.md#create-a-task) preconfigurata, ACR Tasks **ricompila quindi automaticamente ogni immagine di applicazione**. Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

For image builds from a Dockerfile, an ACR task tracks a base image update when the base image is in one of the following locations:

* La stessa istanza di Registro Azure Container in cui viene eseguita l'attività
* Un'altra istanza di Registro Azure Container nella stessa area 
* Un repository pubblico in Docker Hub
* Un repository pubblico in Registro contenitori di Microsoft

> [!NOTE]
> * The base image update trigger is enabled by default in an ACR task. 
> * Currently, ACR Tasks only tracks base image updates for application (*runtime*) images. ACR Tasks doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles. 

Learn more about OS and framework patching in the third ACR Tasks tutorial, [Automate image builds on base image update with Azure Container Registry Tasks](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Pianificare un'attività

Optionally schedule a task by setting up one or more *timer triggers* when you create or update the task. Scheduling a task is useful for running container workloads on a defined schedule, or running maintenance operations or tests on images pushed regularly to your registry. For details, see [Run an ACR task on a defined schedule](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Attività in più passaggi

Multi-step tasks provide step-based task definition and execution for building, testing, and patching container images in the cloud. Task steps defined in a [YAML file](container-registry-tasks-reference-yaml.md) specify individual build and push operations for container images or other artifacts. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

Ad esempio, è possibile creare un'attività in più passaggi che consenta di automatizzare le operazioni seguenti:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Run the web application test container, which performs tests against the running application container
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Le attività in più passaggi consentono di dividere la creazione, l'esecuzione e il test di un'immagine in più passaggi componibili, con il supporto della dipendenza tra i passaggi. Con le attività in più passaggi in ACR Tasks si dispone di un controllo più granulare sulla compilazione delle immagini, sui test e sui flussi di lavoro di applicazione di patch al framework e al sistema operativo.

Altre informazioni sulle attività in più passaggi in [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi con ACR Tasks).

## <a name="context-locations"></a>Context locations

La tabella seguente mostra alcuni esempi di percorsi di contesto supportati per ACR Tasks:

| Posizione contesto | Description | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo master GitHub | File nel master (o altra impostazione predefinita) di un repository GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Ramo specifico di un repository GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub. Example shows combination of a branch and subfolder specification. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps subfolder | Files within a subfolder in an Azure repo. Example shows combination of branch and subfolder specification. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Image platforms

By default, ACR Tasks builds images for the Linux OS and the amd64 architecture. Specify the `--platform` tag to build Windows images or Linux images for other architectures. Specify the OS and optionally a supported architecture in OS/architecture format (for example, `--platform Linux/arm`). For ARM architectures, optionally specify a variant in OS/architecture/variant format (for example, `--platform Linux/arm64/v8`):

| Sistema operativo | Architecture|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>View task logs

Each task run generates log output that you can inspect to determine whether the task steps ran successfully. If you use the [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run), or [az acr task run](/cli/azure/acr/task#az-acr-task-run) command to trigger the task, log output for the task run is streamed to the console and also stored for later retrieval. When a task is automatically triggered, for example by a source code commit or a base image update, task logs are only stored. View the logs for a task run in the Azure portal, or use the [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) command.

By default, data and logs for task runs in a registry are retained for 30 days and then automatically purged. If you want to archive the data for a task run, enable archiving using the [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) command. The following example enables archiving for the task run *cf11* in registry *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Passaggi successivi

When you're ready to automate container image builds and maintenance in the cloud, check out the [ACR Tasks tutorial series](container-registry-tutorial-quick-task.md).

Installare eventualmente l'[estensione Docker per Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e l'estensione [Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per l'uso dei registri contenitori di Azure. Eseguire il pull e il push delle immagini in un registro contenitori di Azure o eseguire Attività del Registro Azure Container, il tutto all'interno di Visual Studio Code.

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
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
