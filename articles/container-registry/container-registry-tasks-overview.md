---
title: Automatizzare l'applicazione di patch al sistema operativo e al framework con Attività di Registro Azure Container (ACR Tasks)
description: Introduzione ad ACR Tasks, un gruppo di funzionalità in Registro Azure Container che consente di eseguire la compilazione di immagini dei contenitori e l'applicazione di patch nel cloud in modo automatico e sicuro.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 05/20/2019
ms.author: danlep
ms.openlocfilehash: cc182743c3879ab2748f92022437bc23c26c371c
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977208"
---
# <a name="automate-os-and-framework-patching-with-acr-tasks"></a>Automate OS and framework patching with ACR Tasks (Automatizzare l'applicazione di patch al sistema operativo e al framework con ACR Tasks)

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. È comunque necessario definire, tuttavia, come applicare patch alla virtualizzazione delle applicazioni.

## <a name="what-is-acr-tasks"></a>Informazioni su ACR Tasks

**ACR Tasks** è una suite di funzionalità in Registro Azure Container. Fornisce funzionalità per la creazione di immagini di contenitore basate su cloud per Linux, Windows e ARM e ed è in grado di automatizzare l'[applicazione di patch a sistemi operativi e framework](#automate-os-and-framework-patching) per i contenitori Docker. ACR Tasks non solo estende il ciclo di sviluppo "a ciclo interno" al cloud con la creazione di immagini contenitore on demand, ma permette anche la compilazione automatica su commit del codice sorgente o quando viene aggiornata l'immagine base del contenitore. Con i trigger correlati all'aggiornamento dell'immagine di base, è possibile automatizzare il flusso di lavoro dell'applicazione di patch al sistema operativo e al framework applicazioni mantenendo gli ambienti sicuri e rispettando le entità di sicurezza di contenitori non modificabili.

Compilare e testare le immagini del contenitore con ACR Tasks in quattro modi:

* [Attività rapida](#quick-task): compilare ed eseguire il push di immagini del contenitore on demand, in Azure, senza necessitare di un'installazione locale di un motore Docker. Pensare `docker build`, `docker push` nel cloud. Compilazione dal codice sorgente locale o un repository GIT.
* [Compilazione automatica in caso di commit del codice sorgente](#automatic-build-on-source-code-commit): attivare automaticamente una compilazione dell'immagine del contenitore quando viene eseguito il commit di codice in un repository Git.
* [Compilazione in caso di aggiornamento dell'immagine di base](#automate-os-and-framework-patching): attivare una compilazione di immagini dei contenitori quando l'immagine di base dell'immagine è stata aggiornata.
* [Attività multipassaggio](#multi-step-tasks): definire le attività in più passaggi che compilano immagini, eseguono i contenitori come comandi ed eseguono il push delle immagini in un registro. Questa funzionalità di attività di registro contenitori di AZURE supporta l'esecuzione dell'attività su richiesta e compilazione dell'immagine parallela, testare e operazioni di push.

## <a name="quick-task"></a>Attività rapida

Il ciclo di sviluppo interno, processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente, è in realtà l'inizio della gestione del ciclo di vita del contenitore.

Prima di eseguire il commit della prima riga di codice, la funzione [attività rapida](container-registry-tutorial-quick-task.md) di ACR Tasks può offrire un'esperienza di sviluppo integrato eseguendo l'offload delle build di un'immagine del contenitore in Azure. Con questa funzionalità è possibile verificare le definizioni delle compilazioni automatiche e rilevare problemi potenziali prima di eseguire il commit di codice.

Usando il noto formato `docker build`, il comando [az acr build][az-acr-build] nell'interfaccia della riga di comando di Azure accetta un *contesto* (l'insieme di file da compilare), lo invia al servizio ACR Tasks e al termine, per impostazione predefinita, esegue il push dell'immagine compilata nel registro.

Per un'introduzione, vedere la Guida introduttiva a [compilare ed eseguire un'immagine del contenitore](container-registry-quickstart-task-cli.md) in Registro contenitori di Azure.  

La tabella seguente mostra alcuni esempi di percorsi di contesto supportati per ACR Tasks:

| Posizione contesto | Descrizione | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo master GitHub | File nel master (o altra impostazione predefinita) di un repository GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Ramo specifico di un repository GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Richiesta pull GitHub | Richiesta pull in un repository GitHub. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub. L'esempio mostra una combinazione di richieste pull e specifiche della sottocartella. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |

ACR Tasks è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Tasks in una soluzione CI/CD. Eseguendo [az login][az-login] con un' [entità servizio][az-login-service-principal], la soluzione di integrazione continua/distribuzione continua potrà quindi eseguire comandi [az acr build][az-acr-build] per avviare compilazioni delle immagini.

Per informazioni su come usare le attività rapide, vedere la prima esercitazione su ACR Tasks, [Compilare immagini dei contenitori nel cloud con ACR Tasks](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Compilazione automatica in caso di commit del codice sorgente

Usare ACR Tasks per attivare automaticamente una compilazione dell'immagine del contenitore quando viene eseguito il commit di codice in un repository GIT. Le attività di compilazione, configurabili con il comando [az acr task][az-acr-task] dell'interfaccia della riga di comando di Azure, consentono di specificare un repository GIT e, facoltativamente, un ramo e un Dockerfile. Quando il team esegue il commit di codice nel repository, un webhook creato da ACR Tasks attiva una compilazione dell'immagine del contenitore definita nel repository.

> [!IMPORTANT]
> Se in precedenza sono state create attività durante l'anteprima con il comando `az acr build-task`, tali attività devono essere ricreate con il comando [az acr task][az-acr-task].

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su ACR Tasks, [Automatizzare la compilazione di immagini dei contenitori con ACR Tasks](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

ACR Tasks può realmente migliorare il flusso di lavoro di compilazione dei contenitori grazie alla capacità di rilevare un aggiornamento di un'immagine di base. Quando viene eseguito il push dell'immagine di base aggiornata nel registro, ACR Tasks può compilare automaticamente tutte le immagini di applicazioni basate su di essa.

Le immagini dei contenitori possono essere suddivise in linea di massima in immagini di *base* e immagini di *applicazioni*. Le immagini di base includono in genere il sistema operativo e i framework applicazioni su cui è basata l'applicazione, insieme ad altre personalizzazioni. Tali immagini di base sono in genere basate a propria volta su immagini upstream pubbliche, ad esempio: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet] o [Node.js ][base-node]. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Quando un'immagine del sistema operativo o del framework app viene aggiornata dal gestore upstream, ad esempio con una patch di sicurezza critica del sistema operativo, è necessario aggiornare anche le immagini di base in modo da includere l'aggiornamento critico. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

Individuando dinamicamente le dipendenze dell'immagine di base durante la compilazione di un'immagine del contenitore, ACR Tasks può rilevare l'aggiornamento dell'immagine di base di un'immagine di applicazione. Con un'[attività di compilazione](container-registry-tutorial-base-image-update.md#create-a-task) preconfigurata, ACR Tasks **ricompila quindi automaticamente ogni immagine di applicazione**. Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

Per informazioni sull'applicazione di patch al sistema operativo e al framework, vedere la terza esercitazione su ACR Tasks, [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Tasks](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Gli aggiornamenti delle immagini di base attivano compilazioni solo quando sia l'immagine di base che quella dell'applicazione risiedono nello stesso Registro Azure Container, o la base risiede in un repository dell'hub Docker accessibile pubblicamente.

## <a name="multi-step-tasks"></a>Attività in più passaggi

Attività multipassaggio forniscono una definizione di attività basata sul passaggio e l'esecuzione per la creazione, test e dell'applicazione di patch immagini del contenitore nel cloud. I passaggi dell'attività definiscono singole operazioni di compilazione e push dell'immagine contenitore. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

Ad esempio, è possibile creare un'attività in più passaggi che consenta di automatizzare le operazioni seguenti:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Eseguire il contenitore di test dell'applicazione Web che esegue test sul contenitore dell'applicazione in esecuzione
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Le attività in più passaggi consentono di dividere la creazione, l'esecuzione e il test di un'immagine in più passaggi componibili, con il supporto della dipendenza tra i passaggi. Con le attività in più passaggi in ACR Tasks si dispone di un controllo più granulare sulla compilazione delle immagini, sui test e sui flussi di lavoro di applicazione di patch al framework e al sistema operativo.

Altre informazioni sulle attività in più passaggi in [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi con ACR Tasks).

## <a name="view-task-logs"></a>Visualizzare i log attività

Ogni esecuzione di attività genera output del log che è possibile esaminare per determinare se i passaggi di attività è stata eseguita correttamente. Se si usa la [compilazione di az acr](/cli/azure/acr#az-acr-build), [az acr eseguire](/cli/azure/acr#az-acr-run), o [dell'esecuzione dell'attività az acr](/cli/azure/acr/task#az-acr-task-run) comando per attivare l'attività, output del log per l'esecuzione dell'attività è trasmesse alla console e anche archiviati per un uso successivo recupero. Visualizza i log per un'attività Esegui nel portale di Azure oppure usano il [log attività di az acr](/cli/azure/acr/task#az-acr-task-logs) comando.

A partire da luglio 2019, dati e i log per le esecuzioni di attività in un registro verranno conservati per impostazione predefinita per 30 giorni e quindi eliminati automaticamente. Se si desidera archiviare i dati per eseguire un'operazione, abilitare l'archiviazione tramite il [update-esecuzione di az acr attività](/cli/azure/acr/task#az-acr-task-update-run) comando. L'esempio seguente Abilita l'archiviazione per l'esecuzione dell'attività *cf11* nel Registro di sistema *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Passaggi successivi

Quando si è pronti per automatizzare l'applicazione di patch framework tramite la compilazione delle immagini del contenitore nel cloud e del sistema operativo, consultare le tre parti [serie di esercitazioni in Registro contenitori di AZURE attività](container-registry-tutorial-quick-task.md).

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
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
