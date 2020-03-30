---
title: Eliminare tag e manifesti
description: Usare un comando di eliminazione per eliminare più tag e manifesti da un registro contenitore di Azure in base alla validità e a un filtro di tag e facoltativamente pianificare le operazioni di eliminazione.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087324"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Eliminazione automatica delle immagini da un registro contenitori di AzureAutomatically purge images from an Azure container registry

Quando si usa un registro contenitori di Azure come parte di un flusso di lavoro di sviluppo, il Registro di sistema può riempirsi rapidamente di immagini o altri elementi che non sono necessari dopo un breve periodo. È possibile eliminare tutti i tag che sono più vecchi di una determinata durata o che corrispondono a un filtro dei nomi specificato. Per eliminare rapidamente più elementi, `acr purge` in questo articolo viene presentato il comando che è possibile eseguire come attività ACR su richiesta o [pianificata.](container-registry-tasks-scheduled.md) 

Il `acr purge` comando è attualmente distribuito`mcr.microsoft.com/acr/acr-cli:0.1`in un'immagine del contenitore pubblico ( ), compilata dal codice sorgente nell'archivio [acr-cli](https://github.com/Azure/acr-cli) in GitHub.

È possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di attività ACR in questo articolo. Se si desidera utilizzarlo in locale, è necessaria la versione 2.0.69 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI. 

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Utilizzare `acr purge` il comando con cautela: i dati dell'immagine eliminati sono UNRECOVERABLE. Se si dispone di sistemi che estraggono le immagini tramite il digest del manifesto (anziché il nome dell'immagine), è consigliabile non eliminare le immagini senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata](container-registry-image-tag-version.md).

Se si desidera eliminare singoli tag di immagine o manifesti usando i comandi dell'interfaccia della riga di comando di Azure, vedere [Eliminare le immagini del contenitore nel Registro](container-registry-delete.md)di sistema contenitori di Azure.If you want to delete single image tags or manifests using Azure CLI commands, see Delete container images in Azure Container Registry .

## <a name="use-the-purge-command"></a>Utilizzare il comando purge

Il `acr purge` comando container elimina le immagini in base al tag in un repository che corrispondono a un filtro nomi e che sono meno recenti di una durata specificata. Per impostazione predefinita, vengono eliminati solo i riferimenti ai tag, non i manifesti e i dati del livello [sottostanti.](container-registry-concepts.md#manifest) Il comando ha un'opzione per eliminare anche i manifesti. 

> [!NOTE]
> `acr purge`non elimina un tag immagine `write-enabled` o un `false`repository in cui l'attributo è impostato su . Per informazioni, vedere [Bloccare un'immagine del contenitore in un registro contenitore](container-registry-image-lock.md)di Azure.For information, see Lock a container image in an Azure container registry.

`acr purge`è progettato per essere eseguito come comando contenitore in [un'attività ACR](container-registry-tasks-overview.md), in modo che venga autenticata automaticamente con il Registro di sistema in cui l'attività viene eseguita ed eseguita azioni in tale posizione. Gli esempi di attività `acr purge` in questo articolo usano [l'alias](container-registry-tasks-reference-yaml.md#aliases) del comando al posto di un comando immagine contenitore completo.

Quando si esegue: `acr purge`

* `--filter`- Un repository e *un'espressione regolare* per filtrare i tag nel repository. Esempi: `--filter "hello-world:.*"` corrisponde a `hello-world` tutti i `--filter "hello-world:^1.*"` tag nel `1`repository e ai tag che iniziano con . Passare `--filter` più parametri per eliminare più repository.
* `--ago`- Una stringa di [durata](https://golang.org/pkg/time/) Go-style per indicare una durata oltre la quale le immagini vengono eliminate. La durata è costituita da una sequenza di uno o più numeri decimali, ognuno con un suffisso di unità. Le unità di tempo valide includono "d" per i giorni, "h" per le ore e "m" per i minuti. Ad esempio, `--ago 2d3h6m` seleziona tutte le immagini filtrate per l'ultima volta `--ago 1.5h` più di 2 giorni, 3 ore e 6 minuti fa e seleziona le immagini per l'ultima modifica più di 1,5 ore fa.

`acr purge`supporta diversi parametri opzionali. Negli esempi di questo articolo vengono utilizzati i due seguenti:The following two are used in examples in this article:

* `--untagged`- Specifica che i manifesti a cui non sono associati tag *(manifesti senza tag*) vengono eliminati.
* `--dry-run`- Specifica che nessun dato viene eliminato, ma l'output è lo stesso come se il comando viene eseguito senza questo flag. Questo parametro è utile per testare un comando di eliminazione per assicurarsi che non elimini inavvertitamente i dati che si intende conservare.

Per ulteriori parametri, eseguire `acr purge --help`. 

`acr purge`supporta altre funzionalità dei comandi Attività ACR, tra cui [le variabili](container-registry-tasks-reference-yaml.md#run-variables) di esecuzione e i registri di esecuzione [delle attività](container-registry-tasks-logs.md) che vengono trasmessi in streaming e salvati anche per un successivo recupero.

### <a name="run-in-an-on-demand-task"></a>Eseguire in un'attività su richiesta

Nell'esempio seguente viene utilizzato il comando `acr purge` [az acr run][az-acr-run] per eseguire il comando su richiesta. In questo esempio vengono eliminati tutti `hello-world` i tag e i manifesti di immagine nel repository in *myregistry* che sono stati modificati più di 1 giorno fa. Il comando container viene passato utilizzando una variabile di ambiente. L'attività viene eseguita senza un contesto di origine.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Esegui in un'operazione pianificata

Nell'esempio seguente viene utilizzato il comando [az acr task create][az-acr-task-create] per creare un'attività [ACR pianificata](container-registry-tasks-scheduled.md)quotidianamente. L'attività elimina i tag modificati più `hello-world` di 7 giorni fa nel repository. Il comando container viene passato utilizzando una variabile di ambiente. L'attività viene eseguita senza un contesto di origine.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Eseguire il comando [az acr task show][az-acr-task-show] per verificare che il trigger timer sia configurato.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Eliminare un numero elevato di tag e manifesti

L'eliminazione di un numero elevato di tag e manifesti potrebbe richiedere alcuni minuti o più. Per eliminare migliaia di tag e manifesti, potrebbe essere necessario eseguire il comando più a lungo del tempo di timeout predefinito di 600 secondi per un'attività su richiesta o di 3600 secondi per un'operazione pianificata. Se il tempo di timeout viene superato, vengono eliminati solo un sottoinsieme di tag e manifesti. Per assicurarsi che un'eliminazione su `--timeout` larga scala sia stata completata, passare il parametro per aumentare il valore. 

Ad esempio, la seguente attività su richiesta imposta un tempo di timeout di 3600 secondi (1 ora):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Esempio: eliminazione pianificata di più repository in un Registro di sistemaExample: Scheduled purge of multiple repositories in a registry

In questo esempio `acr purge` viene illustrato l'utilizzo per pulire periodicamente più repository in un Registro di sistema. Ad esempio, si potrebbe avere una pipeline di `samples/devimage1` `samples/devimage2` sviluppo che esegue il push delle immagini nei repository e . Le immagini di sviluppo vengono importate periodicamente in un repository di produzione per le distribuzioni, in modo che le immagini di sviluppo non siano più necessarie. Su base settimanale, `samples/devimage1` si `samples/devimage2` eliminano i repository e, in preparazione per il lavoro della prossima settimana.

### <a name="preview-the-purge"></a>Visualizzare in anteprima l'eliminazione

Prima di eliminare i dati, è consigliabile `--dry-run` eseguire un'attività di eliminazione su richiesta utilizzando il parametro . Questa opzione consente di visualizzare i tag e i manifesti che il comando eliminerà, senza rimuovere alcun dato. 

Nell'esempio seguente, il filtro in ogni repository seleziona tutti i tag. Il `--ago 0d` parametro corrisponde a immagini di tutte le età nei repository che corrispondono ai filtri. Modificare i criteri di selezione in base alle esigenze dello scenario. Il `--untagged` parametro indica di eliminare i manifesti oltre ai tag. Il comando container viene passato al comando [az acr run][az-acr-run] utilizzando una variabile di ambiente.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Esaminare l'output del comando per visualizzare i tag e i manifesti che corrispondono ai parametri di selezione. Poiché il comando `--dry-run`viene eseguito con , non viene eliminato alcun dato.

Output di esempio:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Pianificare l'eliminazione

Dopo aver verificato l'esecuzione a secco, creare un'attività pianificata per automatizzare l'eliminazione. Nell'esempio seguente viene pianificata un'attività settimanale la domenica alle 1:00 UTC per eseguire il comando di eliminazione precedente:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Eseguire il comando [az acr task show][az-acr-task-show] per verificare che il trigger timer sia configurato.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre opzioni per [eliminare i dati immagine](container-registry-delete.md) nel Registro di sistema del contenitore di Azure.Learn about other options to delete image data in Azure Container Registry.

Per altre informazioni sull'archiviazione delle immagini, vedere [Archiviazione di immagini contenitore nel Registro](container-registry-storage.md)di sistema del contenitore di Azure.For more information about image storage, see Container image storage in Azure Container Registry .

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

