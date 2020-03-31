---
title: Panoramica delle Attività del Registro Azure Container
description: Introduzione ad Attività ACR, una suite di funzionalità in Azure Container Registry che offre la compilazione, la gestione e l'applicazione di patch alle immagini dei contenitori sicure e automatizzate nel cloud.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087279"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizza le compilazioni e la manutenzione delle immagini dei contenitori con le attività ACRAutomate container image builds and maintenance with ACR Tasks

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. Ciò che rimane, tuttavia, è la necessità di affrontare il modo in cui la virtualizzazione dell'applicazione viene gestita e patchata nel ciclo di vita del contenitore.

## <a name="what-is-acr-tasks"></a>Informazioni su ACR Tasks

**ACR Tasks** è una suite di funzionalità in Registro Azure Container. Fornisce la creazione di immagini di contenitori basate su cloud per [piattaforme](#image-platforms) quali Linux, Windows e ARM e può automatizzare l'applicazione di patch a sistemi di applicazioni [e framework](#automate-os-and-framework-patching) per i contenitori Docker. Le attività ACR non solo estendono il ciclo di sviluppo "inner-loop" al cloud con le compilazioni di immagini del contenitore su richiesta, ma consentono anche compilazioni automatiche attivate da aggiornamenti del codice sorgente, aggiornamenti all'immagine di base di un contenitore o timer. Ad esempio, con i trigger di aggiornamento delle immagini di base, è possibile automatizzare il flusso di lavoro di applicazione delle patch del sistema operativo e del framework dell'applicazione, mantenendo ambienti protetti rispettando i principi dei contenitori non modificabili.

## <a name="task-scenarios"></a>Scenari di attività

Attività ACR supporta diversi scenari per compilare e gestire immagini contenitore e altri elementi. Per informazioni dettagliate, vedere le sezioni seguenti in questo articolo.

* **[Attività rapida:](#quick-task)** compilare e eseguire il push di una singola immagine del contenitore in un registro contenitori su richiesta, in Azure, senza che sia necessaria un'installazione locale del motore Docker.Quick task - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. Pensare `docker build`, `docker push` nel cloud.
* **Attività attivate automaticamente:** abilitare uno o più trigger per creare un'immagine:Automatically triggered tasks - Enable one or more *triggers* to build an image:
  * **[Attivare l'aggiornamento del codice sorgenteTrigger on source code update](#trigger-task-on-source-code-update)** 
  * **[Trigger sull'aggiornamento dell'immagine di baseTrigger on base image update](#automate-os-and-framework-patching)** 
  * **[Attivare in base a una pianificazioneTrigger on a schedule](#schedule-a-task)** 
* **[Attività in più passaggi:](#multi-step-tasks)** consente di estendere la funzionalità di compilazione e push di un'unica immagine delle attività ACR con flussi di lavoro multi-passaggio basati su più contenitori. 

A ogni attività ACR è associato un contesto di [codice sorgente,](#context-locations) ovvero il percorso di un set di file di origine usati per compilare un'immagine del contenitore o un altro elemento. I contesti di esempio includono un repository Git o un file system locale.

Le attività possono inoltre sfruttare le variabili di [esecuzione,](container-registry-tasks-reference-yaml.md#run-variables)in modo da poter riutilizzare le definizioni delle attività e standardizzare i tag per immagini ed elementi.

## <a name="quick-task"></a>Attività rapida

Il ciclo di sviluppo interno, processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente, è in realtà l'inizio della gestione del ciclo di vita del contenitore.

Prima di eseguire il commit della prima riga di codice, la funzione [attività rapida](container-registry-tutorial-quick-task.md) di Attività del Registro Azure Container può offrire un'esperienza di sviluppo integrato eseguendo l'offload delle build di un'immagine del contenitore in Azure. Con questa funzionalità è possibile verificare le definizioni delle compilazioni automatiche e rilevare problemi potenziali prima di eseguire il commit di codice.

Usando il noto formato `docker build`, il comando [az acr build][az-acr-build] nell'interfaccia della riga di comando di Azure accetta un [contesto](#context-locations) (l'insieme di file da compilare), lo invia al servizio ACR Tasks e al termine, per impostazione predefinita, esegue il push dell'immagine compilata nel registro.

Per un'introduzione, vedere la guida introduttiva per [compilare ed eseguire un'immagine](container-registry-quickstart-task-cli.md) del contenitore nel Registro di sistema del contenitore di Azure.For an introduction, see the quickstart to build and run a container image in Azure Container Registry.  

ACR Tasks è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Tasks in una soluzione CI/CD. Eseguendo [az login][az-login] con un' [entità servizio][az-login-service-principal], la soluzione di integrazione continua/distribuzione continua potrà quindi eseguire comandi [az acr build][az-acr-build] per avviare compilazioni delle immagini.

Per informazioni su come usare le attività rapide, vedere la prima esercitazione su Attività del Registro Azure Container, [Compilare immagini dei contenitori nel cloud con Attività del Registro Azure Container](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se si vuole compilare e eseguire il push di un'immagine direttamente dal codice sorgente, senza un Dockerfile, il Registro di sistema del contenitore di Azure fornisce il comando [az acr pack build][az-acr-pack-build] (anteprima). Questo strumento compila ed esegue il push di un'immagine dal codice sorgente dell'applicazione utilizzando [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Attivare l'attività nell'aggiornamento del codice sorgenteTrigger task on source code update

Attivare una compilazione dell'immagine contenitore o un'attività a più passaggi quando viene eseguito il commit del codice oppure una richiesta pull viene effettuata o aggiornata, in un repository Git pubblico o privato in GitHub o DevOps di Azure.Trigger a container image build or multi-step task when code is committed, or a pull request is made or updated, to a public or private Git repository in GitHub or Azure DevOps. Ad esempio, configurare un'attività di compilazione con il comando dell'interfaccia della riga di comando di Azure az acr task create specificando un repository Git e, facoltativamente, un ramo e Dockerfile.For example, configure a build task with the Azure CLI [center az acr task create][az-acr-task-create] by specifying a Git repository and optionally a branch and Dockerfile. Quando il team aggiorna il codice nel repository, un webhook creato da attività ACR attiva una compilazione dell'immagine contenitore definita nel repository. 

Attività ACR supporta i trigger seguenti quando si imposta un repository Git come contesto dell'attività:ACR Tasks supports the following triggers when you set a Git repo as the task's context:

| Trigger | Abilitato per impostazione predefinita |
| ------- | ------------------ |
| Commit | Sì |
| Richiesta pull | No |

Per configurare un trigger di aggiornamento del codice sorgente, è necessario fornire all'attività un token di accesso personale (PAT) per impostare il webhook nel repository pubblico o privato GitHub o DevOps di Azure.To configure a source code update trigger, you need to provide the task a personal access token (PAT) to set the webhook in the public or private GitHub or Azure DevOps repo.

> [!NOTE]
> Attualmente, Attività del Registro Azure Container non supporta i trigger per il commit o le richieste pull nei repository di GitHub Enterprise.

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su Attività del Registro Azure Container, [Automatizzare la compilazione di immagini dei contenitori con Attività del Registro Azure Container](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

La potenza delle attività ACR di migliorare realmente il flusso di lavoro di compilazione del contenitore deriva dalla sua capacità di rilevare un aggiornamento a *un'immagine*di base. Funzionalità della maggior parte delle immagini contenitore, un'immagine di base è un'immagine padre su cui si basano una o più immagini dell'applicazione. Le immagini di base contengono in genere il sistema operativo e talvolta i framework dell'applicazione. 

È possibile impostare un'attività ACR per tenere traccia di una dipendenza da un'immagine di base quando viene compilata un'immagine dell'applicazione. Quando l'immagine di base aggiornata viene inserita nel Registro di sistema o un'immagine di base viene aggiornata in un repository pubblico, ad esempio in Docker Hub, le attività ACR possono compilare automaticamente tutte le immagini dell'applicazione in base a essa.
Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

Ulteriori informazioni sui trigger di [aggiornamento delle immagini](container-registry-tasks-base-images.md) di base per le attività ACR. Informazioni su come attivare una compilazione di un'immagine quando un'immagine di base viene inserita in un registro contenitori nell'esercitazione [Automatizzare le compilazioni dell'immagine del contenitore quando un'immagine di base viene aggiornata in un registro del contenitore](container-registry-tutorial-base-image-update.md) di Azure

## <a name="schedule-a-task"></a>Pianificare un'attività

Facoltativamente, pianificare un'attività impostando uno o più *trigger timer* quando si crea o si aggiorna l'attività. La pianificazione di un'attività è utile per l'esecuzione di carichi di lavoro contenitore in base a una pianificazione definita o per l'esecuzione di operazioni di manutenzione o test su immagini inviate regolarmente nel Registro di sistema. Per informazioni dettagliate, vedere [Eseguire un'attività ACR in base](container-registry-tasks-scheduled.md)a una pianificazione definita.

## <a name="multi-step-tasks"></a>Attività in più passaggi

Le attività in più passi consentono di definire ed eseguire attività basate su passaggi per creare, testare e correggere le immagini del contenitore nel cloud. I passaggi dell'attività definiti in un [file YAML](container-registry-tasks-reference-yaml.md) specificano singole operazioni di compilazione e push per immagini del contenitore o altri elementi. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

Ad esempio, è possibile creare un'attività in più passaggi che consenta di automatizzare le operazioni seguenti:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Eseguire il contenitore di test dell'applicazione Web, che esegue test nel contenitore dell'applicazione in esecuzione
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Le attività in più passaggi consentono di dividere la creazione, l'esecuzione e il test di un'immagine in più passaggi componibili, con il supporto della dipendenza tra i passaggi. Con le attività in più passaggi in ACR Tasks si dispone di un controllo più granulare sulla compilazione delle immagini, sui test e sui flussi di lavoro di applicazione di patch al framework e al sistema operativo.

Altre informazioni sulle attività in più passaggi in [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi con ACR Tasks).

## <a name="context-locations"></a>Posizioni di contesto

La tabella seguente mostra alcuni esempi di percorsi di contesto supportati per ACR Tasks:

| Posizione contesto | Descrizione | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo master GitHub | File all'interno del ramo master (o di altro valore predefinito) di un repository GitHub pubblico o privato.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Branch specifico di un repository GitHub pubblico o privato.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub pubblico o privato. L'esempio mostra la combinazione di una specifica di diramazione e di sottocartella. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Commit GitHub | Commit specifico in un repository GitHub pubblico o privato. L'esempio mostra la combinazione di un hash di commit (SHA) e la specifica della sottocartella. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Sottocartella DevOps di AzureAzure DevOps subfolder | File all'interno di una sottocartella in un repository di Azure pubblico o privato. L'esempio mostra la combinazione di specifica di diramazione e sottocartella. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Quando si usa un repository Git privato come contesto per un'attività, è necessario fornire un token di accesso personale (PAT).

## <a name="image-platforms"></a>Piattaforme di immagini

Per impostazione predefinita, Attività ACR crea immagini per il sistema operativo Linux e l'architettura amd64. Specificare `--platform` il tag per creare immagini Windows o immagini Linux per altre architetture. Specificare il sistema operativo e, facoltativamente, un'architettura `--platform Linux/arm`supportata nel formato OS/architettura (ad esempio, ). Per le architetture ARM, specificare facoltativamente una variante nel `--platform Linux/arm64/v8`formato OS/architecture/variant (ad esempio, ):

| OS | Architecture|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>braccio64<br/>386 |
| WINDOWS | amd64 |

## <a name="view-task-output"></a>Visualizzare l'output dell'attività

Ogni esecuzione di attività genera l'output del log che è possibile esaminare per determinare se i passaggi dell'attività sono stati eseguiti correttamente. Quando si attiva un'attività manualmente, registrare l'output per l'esecuzione dell'attività viene trasmesso alla console e archiviato anche per un successivo recupero. Quando un'attività viene attivata automaticamente, ad esempio da un commit del codice sorgente o da un aggiornamento dell'immagine di base, i log delle attività vengono archiviati solo. Visualizzare i log di esecuzione nel portale di Azure o usare il comando [az acr task logs.](/cli/azure/acr/task#az-acr-task-logs)

Per ulteriori informazioni sulla visualizzazione e la gestione dei registri attività, vedere [Visualizzazione e gestione dei registri attività.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Passaggi successivi

Quando si è pronti per automatizzare le compilazioni e la manutenzione delle immagini contenitore nel cloud, vedere la serie di esercitazioni Attività [ACR](container-registry-tutorial-quick-task.md).

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
