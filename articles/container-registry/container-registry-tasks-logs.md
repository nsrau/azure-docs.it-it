---
title: Visualizzare i log di esecuzione dell'attività-attività
description: Come visualizzare e gestire i log di esecuzione generati dalle attività ACR.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79246968"
---
# <a name="view-and-manage-task-run-logs"></a>Visualizzare e gestire i log di esecuzione delle attività

Ogni attività eseguita in [Azure container Registry attività](container-registry-tasks-overview.md) genera l'output del log che è possibile controllare per determinare se i passaggi dell'attività sono stati eseguiti correttamente. 

Questo articolo illustra come visualizzare e gestire i log di esecuzione delle attività.

## <a name="view-streamed-logs"></a>Visualizzare i log in streaming

Quando si attiva un'attività manualmente, l'output del log viene trasmesso direttamente alla console. Ad esempio, quando si attiva manualmente un'attività usando il comando [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)o [AZ ACR task run](/cli/azure/acr/task#az-acr-task-run) , viene visualizzato il flusso dell'output del log nella console. 

Il seguente esempio [AZ ACR Run](/cli/azure/acr#az-acr-run) Command attiva manualmente un'attività che esegue un contenitore estratto dallo stesso registro di sistema:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Log con flusso:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Visualizzare i log archiviati 

Azure Container Registry archivia i log di esecuzione per tutte le attività. È possibile visualizzare i log di esecuzione archiviati nel portale di Azure. In alternativa, usare il comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) per visualizzare un log selezionato. Per impostazione predefinita, i log vengono conservati per 30 giorni.

Se un'attività viene attivata automaticamente, ad esempio tramite un aggiornamento del codice sorgente, l'accesso ai log archiviati è l' *unico* modo per visualizzare i log di esecuzione. I trigger di attività automatici includono i commit del codice sorgente o le richieste pull, gli aggiornamenti delle immagini di base e i trigger timer.

Per visualizzare i log di esecuzione nel portale:

1. Passare al registro contenitori.
1. In **Servizi**selezionare **Tasks**  >  **esecuzioni**attività.
1. Selezionare un **ID di esecuzione** per visualizzare lo stato di esecuzione ed eseguire i log. Il log contiene le stesse informazioni di un log trasmesso, se ne viene generato uno.

![Visualizzare il portale di accesso per l'esecuzione di attività](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Per visualizzare un log usando l'interfaccia della riga di comando di Azure, eseguire il comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) e specificare un ID esecuzione, un nome di attività o un'immagine specifica creata da un'attività di compilazione. Se viene specificato un nome di attività, il comando Mostra il log per l'ultima esecuzione creata.

Nell'esempio seguente viene restituito il log per l'esecuzione con ID *CF4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Archiviazione di log alternativa

Potrebbe essere necessario archiviare i log di esecuzione delle attività in un file system locale o usare una soluzione di archiviazione alternativa, ad esempio archiviazione di Azure.

Ad esempio, creare una directory *tasklogs* locale e reindirizzare l'output dei [log delle attività AZ ACR](/cli/azure/acr/task#az-acr-task-logs) in un file locale:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

È anche possibile salvare i file di log locali in archiviazione di Azure. Ad esempio, usare l' [interfaccia](../storage/blobs/storage-quickstart-blobs-cli.md)della riga di comando di Azure, il [portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md)o altri metodi per caricare file in un account di archiviazione.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [attività di Azure container Registry](container-registry-tasks-overview.md)

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
