---
title: Eliminare tag e manifesti
description: Usare un comando purge per eliminare più tag e manifesti da un Registro Azure Container in base all'età e a un filtro tag e, facoltativamente, pianificare le operazioni di rimozione.
ms.topic: article
ms.date: 11/10/2020
ms.openlocfilehash: 406a1f231af57407e9475a8888b68aad9d88dcb3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445116"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Eliminare automaticamente le immagini da un Registro Azure Container

Quando si usa un Registro Azure Container come parte di un flusso di lavoro di sviluppo, il registro può riempirsi rapidamente di immagini o altri artefatti non necessari dopo un breve periodo di tempo. Potrebbe essere necessario eliminare tutti i tag creati oltre un determinato periodo di tempo o corrispondenti a un determinato filtro dei nomi. Questo articolo descrive il comando `acr purge` per eliminare rapidamente più artefatti, eseguibile come un'attività su richiesta o [pianificata](container-registry-tasks-scheduled.md) del Registro Azure Container. 

Il comando `acr purge` è attualmente distribuito in un'immagine del contenitore pubblica (`mcr.microsoft.com/acr/acr-cli:0.3`), compilata dal codice sorgente nel repository [acr-cli](https://github.com/Azure/acr-cli) di GitHub.

Per eseguire gli esempi di attività di Registro Azure Container di questo articolo è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si preferisce l'interfaccia locale, è necessario usare la versione 2.0.76 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install]. 

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Usare il comando `acr purge` con cautela: i dati di un'immagine eliminata NON POSSONO ESSERE RIPRISTINATI. Se si dispone di sistemi che eseguono il pull delle immagini tramite hash di manifesto e non tramite i nomi, non eliminare immagini senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci* , una [procedura consigliata](container-registry-image-tag-version.md).

Se si vuole eliminare singoli tag di immagine o manifesti usando i comandi dell'interfaccia della riga di comando di Azure, vedere [Eliminare immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Usare il comando purge

Il comando `acr purge` del contenitore elimina le immagini in base ai tag in un repository che corrispondono a un filtro dei nomi e che sono stati creati oltre un determinato periodo di tempo. Per impostazione predefinita, vengono eliminati solo i riferimenti ai tag, non i dati sottostanti di [manifesti](container-registry-concepts.md#manifest) e livello. Il comando ha un'opzione per eliminare anche i manifesti. 

> [!NOTE]
> `acr purge` non elimina un tag o repository di immagini in cui l'attributo `write-enabled` è impostato su `false`. Per informazioni, vedere [Bloccare un'immagine del contenitore in un Registro Azure Container](container-registry-image-lock.md).

`acr purge` è progettato per essere eseguito come comando del contenitore in un'[Attività del Registro Azure Container](container-registry-tasks-overview.md), in modo che venga autenticata in automatico ed esegua le azioni nel registro in cui viene eseguita l'attività stessa. Gli esempi di attività in questo articolo usano l'[alias](container-registry-tasks-reference-yaml.md#aliases) del comando `acr purge` invece di un comando completo di immagine del contenitore.

Specificare come minimo quanto segue quando si esegue `acr purge`:

* `--filter`: un repository e un' *espressione regolare* per filtrare i tag nel repository. Esempi: `--filter "hello-world:.*"` corrisponde a tutti i tag nel repository `hello-world` e `--filter "hello-world:^1.*"` corrisponde ai tag che iniziano con `1`. Passare più parametri `--filter` per rimuovere più repository.
* `--ago`: una [stringa di durata](https://golang.org/pkg/time/) in Go per indicare un periodo di tempo oltre il quale le immagini vengono eliminate. La durata è costituita da una sequenza di uno o più numeri decimali, ognuno con un suffisso di unità. Le unità di tempo valide includono "d" per i giorni, "h" per le ore e "m" per i minuti. Ad esempio, `--ago 2d3h6m` seleziona tutte le immagini filtrate modificate l'ultima volta più di 2 giorni, 3 ore e 6 minuti fa, mentre `--ago 1.5h` seleziona le immagini modificate per l'ultima volta più di 1,5 ore fa.

`acr purge` supporta diversi parametri facoltativi. Negli esempi di questo articolo vengono usati i due seguenti:

* `--untagged`: specifica che i manifesti che non dispongono di tag associati ( *manifesti senza tag* ) vengono eliminati.
* `--dry-run`: specifica che non vengono eliminati dati, ma l'output è come sarebbe se il comando fosse eseguito senza questo flag. Questo parametro è utile per il test di un comando purge per assicurarsi di non eliminare per sbaglio i dati che si desidera mantenere.
* `--keep` -Specifica che viene mantenuto il numero x più recente di tag da eliminare.

Eseguire `acr purge --help` per i parametri aggiuntivi. 

`acr purge` supporta altre funzionalità dei comandi delle Attività del Registro Azure Container, tra cui [l'esecuzione di variabili](container-registry-tasks-reference-yaml.md#run-variables) e [i log di esecuzione delle attività](container-registry-tasks-logs.md) trasmessi e salvati per un recupero in un secondo momento.

### <a name="run-in-an-on-demand-task"></a>Eseguire un'attività su richiesta

Nell'esempio seguente viene usato il comando [az acr run][az-acr-run] per eseguire il comando `acr purge` su richiesta. In questo esempio vengono eliminati tutti i tag di immagine e i manifesti nel repository `hello-world` in *myregistry* modificati più di un giorno fa. Il comando del contenitore viene passato usando una variabile di ambiente. L'attività viene eseguita senza un contesto di origine.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Esecuzione in un'attività pianificata

Nell'esempio seguente viene usato il comando [az acr task create][az-acr-task-create] per creare un'[attività del Registro Azure Container pianificata](container-registry-tasks-scheduled.md) giornaliera. L'attività elimina i tag modificati più di 7 giorni fa nel repository `hello-world`. Il comando del contenitore viene passato usando una variabile di ambiente. L'attività viene eseguita senza un contesto di origine.

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

Eseguire il comando [az acr task show][az-acr-task-show] per verificare che il trigger del timer sia configurato.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Eliminare un numero elevato di tag e manifesti

La rimozione di un numero elevato di tag e manifesti potrebbe richiedere più di diversi minuti. Per eliminare migliaia di tag e manifesti, potrebbe essere necessario eseguire il comando più a lungo del tempo di timeout predefinito di 600 secondi per un'attività su richiesta o di 3600 secondi per un'attività pianificata. Se si supera il tempo di timeout, viene eliminato solo un subset di tag e manifesti. Per assicurarsi che sia stata completata un'operazione di rimozione su larga scala, passare il parametro `--timeout` per aumentare il valore. 

Ad esempio, l'attività su richiesta seguente imposta un tempo di timeout di 3600 secondi (1 ora):

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Esempio: Rimozione pianificata di più repository in un registro

Questo esempio illustra l'uso di `acr purge` per pulire periodicamente più repository in un registro. Ad esempio, si può disporre di una pipeline di sviluppo che esegue il push delle immagini nei repository `samples/devimage1` e `samples/devimage2`. Si importano periodicamente immagini di sviluppo in un repository di produzione per le distribuzioni, quindi non sono più necessarie le immagini di sviluppo. Si eliminano i repository `samples/devimage1` e `samples/devimage2` ogni settimana, in preparazione per il lavoro della settimana successiva.

### <a name="preview-the-purge"></a>Anteprima della rimozione

Prima di eliminare i dati, è consigliabile eseguire un'attività di rimozione su richiesta con il parametro `--dry-run`. Questa opzione consente di visualizzare i tag e i manifesti che il comando eliminerà, senza rimuovere i dati. 

Nell'esempio seguente, il filtro in ogni repository seleziona tutti i tag. Il parametro `--ago 0d` corrisponde a immagini create in qualsiasi momento nei repository che corrispondono ai filtri. Modificare i criteri di selezione in base alle esigenze per lo scenario. Il parametro `--untagged` indica di eliminare i manifesti oltre ai tag. Il comando del contenitore viene passato nel comando [az acr run][az-acr-run] usando una variabile di ambiente.

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

Esaminare l'output del comando per visualizzare i tag e i manifesti corrispondenti ai parametri di selezione. Poiché il comando viene eseguito con `--dry-run`, nessun dato viene eliminato.

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

### <a name="schedule-the-purge"></a>Pianificare la rimozione

Dopo aver verificato l'esecuzione di prova, creare un'attività pianificata per automatizzare la rimozione. Nell'esempio seguente viene pianificata un'attività settimanale la domenica alle ore 1:00 UTC per eseguire il comando di rimozione precedente:

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

Eseguire il comando [az acr task show][az-acr-task-show] per verificare che il trigger del timer sia configurato.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre opzioni per [eliminare i dati di immagine](container-registry-delete.md) in Registro Azure Container.

Per altre informazioni sull'archiviazione delle immagini, vedere [Archiviazione di immagini del contenitore in Registro Azure Container](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

