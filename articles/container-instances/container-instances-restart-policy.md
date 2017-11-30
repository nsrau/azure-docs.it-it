---
title: "Eseguire attività in contenitori in Istanze di contenitore di Azure"
description: "Informazioni su come usare Istanze di contenitore di Azure per eseguire attività eseguite fino al completamento, ad esempio nella compilazione, nei test o nei processi per il rendering di immagini."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: ae2466bdd3b21984b141c9178ea46bd25bb6357b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Eseguire un'attività in contenitori in Istanze di contenitore di Azure

La semplicità e la velocità della distribuzione di contenitori in Istanze di contenitore di Azure offre una piattaforma interessante per l'esecuzione di attività eseguite una sola volta come la compilazione, il test e il rendering di immagini in un'istanza del contenitore.

Con un criterio di riavvio configurabile, è possibile specificare l'arresto dei contenitori al completamento dei processi. Poiché le istanze del contenitore vengono fatturate al secondo, vengono addebitate solo le risorse di calcolo usate mentre il contenitore che esegue l'attività è in esecuzione.

Gli esempi presentati in questo articolo usano l'interfaccia della riga di comando di Azure. È necessario aver [installato in locale](/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure versione 2.0.21 o versione superiore, oppure usare l'interfaccia della riga di comando di Azure in [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Criteri di riavvio del contenitore

Quando si crea un contenitore in Istanze di contenitore di Azure, è possibile specificare una delle tre impostazioni dei criteri di riavvio.

| Criterio di riavvio   | Descrizione |
| ---------------- | :---------- |
| `Always` | I contenitori nel gruppo contenitore vengono sempre riavviati. Questa è l'impostazione **predefinita** applicata quando non si specifica alcun criterio di riavvio al momento della creazione del contenitore. |
| `Never` | I contenitori nel gruppo contenitore non vengono riavviati mai. I contenitori vengono eseguiti al massimo una volta. |
| `OnFailure` | I contenitori nel gruppo contenitore vengono riavviati solo quando il processo eseguito nel contenitore ha esito negativo, quando termina con un codice di uscita diverso da zero. I contenitori vengono eseguiti almeno una volta. |

## <a name="specify-a-restart-policy"></a>Specificare un criterio di riavvio

Le modalità con cui si specificano i criteri di riavvio dipendano da come si creano le istanze del contenitore, ad esempio con l'interfaccia della riga di comando di Azure, i cmdlet di Azure PowerShell o nel portale di Azure. Nell'interfaccia della riga di comando di Azure specificare il parametro `--restart-policy` quando si chiama [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Eseguire l'esempio di completamento

Per visualizzare i criteri di riavvio in azione, creare un'istanza del contenitore dall'immagine [microsoft/aci-wordcount](https://hub.docker.com/r/microsoft/aci-wordcount/) e specificare il criterio di riavvio `OnFailure`. Questo contenitore di esempio esegue uno script di Python che, per impostazione predefinita, analizza il testo [Amleto](http://shakespeare.mit.edu/hamlet/full.html) di Shakespeare, scrive le 10 parole più comuni in STDOUT ed esce.

Eseguire il contenitore di esempio con il comando seguente [az container create][az-container-create]:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Istanze di contenitore di Azure avvia il contenitore e lo interrompe quindi quando la sua applicazione, o lo script in questo caso, esce. Quando Istanze di contenitore di Azure arresta un contenitore i cui criteri di riavvio sono `Never` o `OnFailure`, lo stato del contenitore viene impostato su **Terminato**. È possibile controllare lo stato del contenitore usando il comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Output di esempio:

```bash
"Terminated"
```

Quando lo stato del contenitore di esempio mostra *Terminato*, è possibile visualizzare l'output dell'attività visualizzando i log dei contenitori. Eseguire il comando [az container logs][az-container-logs] per visualizzare l'output dello script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Output:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Questo esempio mostra l'output che lo script ha inviato a STDOUT. Le attività nei contenitori, tuttavia, potrebbero invece scrivere l'output in un archivio permanente per il recupero successivo. Ad esempio in una [condivisione file di Azure](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Configurare i contenitori durante il runtime

Quando si crea un'istanza del contenitore, è possibile impostarne le **variabili di ambiente**, nonché specificare una **riga di comando** personalizzata da eseguire quando il contenitore viene avviato. È possibile usare queste impostazioni nei processi batch per preparare ogni contenitore con la configurazione specifica dell'attività.

## <a name="environment-variables"></a>Variabili di ambiente

Impostare le variabili di ambiente nel contenitore per offrire la configurazione dinamica dell'applicazione o lo script eseguiti dal contenitore. È simile all'argomento della riga di comando `--env` per `docker run`.

Ad esempio, è possibile modificare il comportamento dello script nel contenitore di esempio, specificando le variabili di ambiente seguenti quando si crea l'istanza del contenitore:

*NumWords*: il numero di parole inviate a STDOUT.

*MinLength*: il numero minimo di caratteri in una parola perché venga contata. Un numero più alto ignora le parole comuni, ad esempio "di" e "il".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Specificando `NumWords=5` e `MinLength=8` per le variabili di ambiente del contenitore, i log contenitori devono mostrare un output diverso. Quando lo stato del contenitore viene mostrato come *Terminato*, usare `az container show` per verificarne lo stato, visualizzare i log per visualizzare il nuovo output:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Output:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Eseguire l'override della riga di comando

Specificare una riga di comando quando si crea un'istanza del contenitore per eseguire l'override della riga di comando compresa nell'immagine del contenitore. È simile all'argomento della riga di comando `--entrypoint` per `docker run`.

Ad esempio, è possibile fare in modo che il contenitore di esempio analizzi un testo diverso da *Amleto* specificando una riga di comando diversa. Lo script Python eseguito dal contenitore, *wordcount.py*, accetta un URL come argomento ed elaborerà il contenuto di questa pagina anziché il valore predefinito.

Ad esempio, per determinare le prime 3 parole di cinque lettere in *Romeo e Giulietta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Di nuovo, quando il contenitore mostra lo stato *Terminato*, visualizzare l'output tramite la visualizzazione dei log del contenitore:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Output:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="persist-task-output"></a>Mantenere l'output dell'attività

Per informazioni dettagliate su come mantenere l'output dei contenitori che vengono eseguiti fino al completamento, vedere [Montaggio di una condivisione file di Azure con Istanze di contenitore di Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show