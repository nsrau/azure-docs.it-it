---
title: Automatizzare la creazione e l'applicazione di patch alle immagini del contenitore con le attività di Container Registry di Azure (attività ACR)
description: Introduzione alle attività di ACR, una suite di funzionalità in Azure Container Registry che fornisce la compilazione, la gestione e l'applicazione di patch di immagini del contenitore sicure e automatizzate nel cloud.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: bc32ce59a7ec99278fb193f375d4ca945c227d2f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172182"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizzare le compilazioni e la manutenzione delle immagini del contenitore con le attività ACR

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. Ciò che rimane, tuttavia, è la necessità di risolvere il modo in cui la virtualizzazione dell'applicazione viene gestita e applicata al ciclo di vita del contenitore.

## <a name="what-is-acr-tasks"></a>Informazioni su ACR Tasks

**ACR Tasks** è una suite di funzionalità in Registro Azure Container. Fornisce funzionalità per la creazione di immagini di contenitore basate su cloud per Linux, Windows e ARM e ed è in grado di automatizzare l'[applicazione di patch a sistemi operativi e framework](#automate-os-and-framework-patching) per i contenitori Docker. Attività del Registro Azure Container non solo estende il ciclo di sviluppo "a ciclo interno" al cloud con la creazione di immagini contenitore on demand, ma permette anche la compilazione automatica su commit del codice sorgente o quando viene aggiornata l'immagine base del contenitore. Con i trigger correlati all'aggiornamento dell'immagine di base, è possibile automatizzare il flusso di lavoro dell'applicazione di patch al sistema operativo e al framework applicazioni mantenendo gli ambienti sicuri e rispettando le entità di sicurezza di contenitori non modificabili.

Compilare e testare le immagini del contenitore con Attività del Registro Azure Container in quattro modi:

* [Attività rapida](#quick-task): compilare ed eseguire il push di immagini del contenitore on demand, in Azure, senza necessitare di un'installazione locale di un motore Docker. Pensare `docker build`, `docker push` nel cloud. Compilazione dal codice sorgente locale o un repository GIT.
* [Compilazione automatica in caso di commit del codice sorgente](#automatic-build-on-source-code-commit): attivare automaticamente una compilazione dell'immagine del contenitore quando viene eseguito il commit di codice in un repository Git.
* [Compilazione in caso di aggiornamento dell'immagine di base](#automate-os-and-framework-patching): attivare una compilazione di immagini dei contenitori quando l'immagine di base dell'immagine è stata aggiornata.
* [Attività](#multi-step-tasks)in più passaggi: definire le attività in più passaggi che compilano immagini, eseguono i contenitori come comandi ed eseguono il push delle immagini in un registro. Questa funzionalità delle attività ACR supporta l'esecuzione di attività su richiesta e le operazioni di compilazione, test e push di immagini parallele.

## <a name="quick-task"></a>Attività rapida

Il ciclo di sviluppo interno, processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente, è in realtà l'inizio della gestione del ciclo di vita del contenitore.

Prima di eseguire il commit della prima riga di codice, la funzione [attività rapida](container-registry-tutorial-quick-task.md) di Attività del Registro Azure Container può offrire un'esperienza di sviluppo integrato eseguendo l'offload delle build di un'immagine del contenitore in Azure. Con questa funzionalità è possibile verificare le definizioni delle compilazioni automatiche e rilevare problemi potenziali prima di eseguire il commit di codice.

Usando il formato `docker build` familiare, il comando [AZ ACR Build][az-acr-build] nell'interfaccia della riga di comando di Azure accetta un *contesto* (il set di file da compilare), invia le attività ACR e, per impostazione predefinita, effettua il push dell'immagine compilata al relativo registro al termine del completamento.

Per un'introduzione, vedere la Guida introduttiva per [compilare ed eseguire un'immagine del contenitore](container-registry-quickstart-task-cli.md) in Azure container Registry.  

La tabella seguente mostra alcuni esempi di percorsi di contesto supportati per ACR Tasks:

| Posizione contesto | Descrizione | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo master GitHub | File nel master (o altra impostazione predefinita) di un repository GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Ramo specifico di un repository GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub. Esempio mostra la combinazione di una specifica di Branch e sottocartella. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |

Per impostazione predefinita, le attività ACR compilano immagini per il sistema operativo Linux e l'architettura amd64. Specificare il `--platform` tag per compilare immagini Windows o immagini Linux per altre architetture. Specificare il sistema operativo e, facoltativamente, un'architettura supportata nel formato del sistema operativo/ `--platform Linux/arm`architettura (ad esempio,). Per le architetture ARM, è possibile specificare facoltativamente una variante nel formato sistema operativo/architettura/variante ( `--platform Linux/arm64/v8`ad esempio,):

| OS | Architettura|
| --- | ------- | 
| Linux | amd64<br/>ARM<br/>arm64<br/>386 |
| Windows | amd64 |

ACR Tasks è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Tasks in una soluzione CI/CD. Eseguendo [AZ login][az-login] con un' [entità servizio][az-login-service-principal], la soluzione ci/CD potrebbe quindi rilasciare i comandi [AZ ACR Build][az-acr-build] per avviare le compilazioni di immagini.

Per informazioni su come usare le attività rapide, vedere la prima esercitazione su Attività del Registro Azure Container, [Compilare immagini dei contenitori nel cloud con Attività del Registro Azure Container](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Compilazione automatica in caso di commit del codice sorgente

Usare le attività ACR per attivare automaticamente una compilazione dell'immagine del contenitore quando viene eseguito il commit del codice in un repository git in GitHub o Azure DevOps. Le attività di compilazione, configurabili con il comando dell'interfaccia della riga di comando di Azure [AZ ACR Task][az-acr-task], consentono di specificare un repository git e, facoltativamente, un Branch e un Dockerfile. Quando il team esegue il commit di codice nel repository, un webhook creato da Attività del Registro Azure Container attiva una compilazione dell'immagine del contenitore definita nel repository.

> [!IMPORTANT]
> Se in precedenza sono state create attività durante l'anteprima con il comando `az acr build-task`, tali attività devono essere ricreate con il comando [az acr task][az-acr-task].

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su Attività del Registro Azure Container, [Automatizzare la compilazione di immagini dei contenitori con Attività del Registro Azure Container](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

ACR Tasks può realmente migliorare il flusso di lavoro di compilazione dei contenitori grazie alla capacità di rilevare un aggiornamento di un'immagine di base. Quando viene effettuato il push dell'immagine di base aggiornata nel registro di sistema o un'immagine di base viene aggiornata in un repository pubblico, ad esempio in Docker Hub, le attività di ACR possono compilare automaticamente eventuali immagini dell'applicazione basate su di esso.

Le immagini dei contenitori possono essere suddivise in linea di massima in immagini di *base* e immagini di *applicazioni*. Le immagini di base includono in genere il sistema operativo e i framework applicazioni su cui è basata l'applicazione, insieme ad altre personalizzazioni. Tali immagini di base sono in genere basate a propria volta su immagini upstream pubbliche, ad esempio: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet]o [node. js][base-node]. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Quando un'immagine del sistema operativo o del framework app viene aggiornata dal gestore upstream, ad esempio con una patch di sicurezza critica del sistema operativo, è necessario aggiornare anche le immagini di base in modo da includere l'aggiornamento critico. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

Individuando dinamicamente le dipendenze dell'immagine di base durante la compilazione di un'immagine del contenitore, Attività del Registro Azure Container può rilevare l'aggiornamento dell'immagine di base di un'immagine di applicazione. Con un'[attività di compilazione](container-registry-tutorial-base-image-update.md#create-a-task) preconfigurata, ACR Tasks **ricompila quindi automaticamente ogni immagine di applicazione**. Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

Un'attività ACR tiene traccia dell'aggiornamento di un'immagine di base quando l'immagine di base si trova in una delle posizioni seguenti:

* Lo stesso registro contenitori di Azure in cui viene eseguita l'attività
* Un altro registro contenitori di Azure nella stessa area 
* Un repository pubblico nell'hub Docker
* Un repository pubblico in Microsoft Container Registry

Per altre informazioni sull'applicazione di patch per il sistema operativo e il Framework, vedere la terza esercitazione sulle attività di ACR, automatizzare le compilazioni di [Immagini nell'aggiornamento dell'immagine di base con container Registry Azure](container-registry-tutorial-base-image-update.md)

## <a name="multi-step-tasks"></a>Attività in più passaggi

Le attività in più passaggi forniscono la definizione e l'esecuzione di attività basate sui passaggi per la compilazione, il test e l'applicazione di patch alle immagini del contenitore nel cloud. I passaggi dell'attività definiscono singole operazioni di compilazione e push dell'immagine contenitore. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

Ad esempio, è possibile creare un'attività in più passaggi che consenta di automatizzare le operazioni seguenti:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Eseguire il contenitore di test dell'applicazione Web che esegue test sul contenitore dell'applicazione in esecuzione
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Le attività in più passaggi consentono di dividere la creazione, l'esecuzione e il test di un'immagine in più passaggi componibili, con il supporto della dipendenza tra i passaggi. Con le attività in più passaggi in ACR Tasks si dispone di un controllo più granulare sulla compilazione delle immagini, sui test e sui flussi di lavoro di applicazione di patch al framework e al sistema operativo.

Altre informazioni sulle attività in più passaggi in [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi con ACR Tasks).

## <a name="view-task-logs"></a>Visualizzare i log delle attività

Ogni esecuzione di attività genera l'output del log che è possibile esaminare per determinare se i passaggi dell'attività sono stati eseguiti correttamente. Se si usa il comando [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)o [AZ ACR task run](/cli/azure/acr/task#az-acr-task-run) per attivare l'attività, l'output del log per l'esecuzione dell'attività viene trasmesso alla console e archiviato anche per un successivo recupero. Visualizzare i log per un'attività eseguita nel portale di Azure o usare il comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

A partire da luglio 2019, i dati e i log per le esecuzioni delle attività in un registro verranno conservati per impostazione predefinita per 30 giorni e quindi eliminati automaticamente. Se si vogliono archiviare i dati per un'esecuzione di attività, abilitare l'archiviazione usando il comando [AZ ACR task update-Run](/cli/azure/acr/task#az-acr-task-update-run) . Nell'esempio seguente viene abilitata l'archiviazione per l'esecuzione dell'attività *CF11* nel registro di sistema del registro di sistema.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Passaggi successivi

Quando si è pronti per automatizzare l'applicazione di patch al sistema operativo e al Framework compilando le immagini del contenitore nel cloud, vedere la serie di esercitazioni di [attività ACR](container-registry-tutorial-quick-task.md)in tre parti.

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
