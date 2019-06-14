---
title: Aggiornare i contenitori in Istanze di Azure Container
description: Informazioni su come aggiornare i contenitori in esecuzione nei gruppi di contenitori in Istanze di Azure Container.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 2df6a2724cbdcd6bbb6c6ca6636256b7e399da8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686892"
---
# <a name="update-containers-in-azure-container-instances"></a>Aggiornare i contenitori in Istanze di Azure Container

Durante il normale funzionamento delle istanze di contenitore, potrebbe essere necessario aggiornare i contenitori in un gruppo di contenitori. Ad esempio, è possibile aggiornare la versione dell'immagine, modificare un nome DNS, aggiornare le variabili di ambiente o aggiornare lo stato di un contenitore la cui applicazione si è arrestata in modo anomalo.

## <a name="update-a-container-group"></a>Aggiornare un gruppo di contenitori

Aggiornare i contenitori in un gruppo di contenitori tramite la ridistribuzione di un gruppo esistente con almeno una proprietà modificata. Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esecuzione nel gruppo vengono riavviati sul posto.

Ridistribuire un gruppo di contenitori esistente eseguendo il comando Crea (o tramite il portale di Azure) e specificare il nome di un gruppo esistente. Quando si esegue il comando Crea per attivare la ridistribuzione, modificare almeno una proprietà del gruppo valida. Non tutte le proprietà del gruppo contenitore sono valide per la ridistribuzione. Per un elenco delle proprietà non supportate, vedere [Properties that require delete](#properties-that-require-container-delete) (Proprietà che richiedono l'eliminazione).

L'esempio di interfaccia della riga di comando di Azure seguente aggiorna un gruppo di contenitori con una nuova etichetta del nome DNS. Poiché la proprietà di etichetta del nome DNS del gruppo viene modificata, il gruppo di contenitori viene ridistribuito e i relativi contenitori vengono riavviati.

Distribuzione iniziale con l'etichetta del nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aggiornare il gruppo di contenitori con una nuova etichetta del nome DNS, *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Vantaggi dell'aggiornamento

Il vantaggio principale dell'aggiornamento di un gruppo di contenitori esistente è una distribuzione più veloce. Quando si ridistribuisce un gruppo di contenitori esistente, viene eseguito il pull dei relativi livelli dell'immagine del contenitore da quelli memorizzati nella cache per la distribuzione precedente. Invece di eseguire il pull di tutti i livelli dell'immagine aggiornata dal registro come avviene con le nuove distribuzioni, viene eseguito il pull dei soli livelli modificati (se presenti).

Le applicazioni basate su immagini del contenitore di dimensioni maggiori, ad esempio Windows Server Core, possono ottenere un miglioramento significativo della velocità di distribuzione quando si esegue l'aggiornamento anziché eliminarle ed eseguire nuovamente la distribuzione.

## <a name="limitations"></a>Limitazioni

Non tutte le proprietà di un gruppo di contenitori supportano gli aggiornamenti. Per modificare alcune proprietà di un gruppo di contenitori è necessario eliminare e ridistribuire il gruppo. Per informazioni dettagliate, vedere [Proprietà che richiedono l'eliminazione del contenitore](#properties-that-require-container-delete).

Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esso presenti vengono riavviati. È possibile eseguire un aggiornamento o un riavvio sul posto di un contenitore specifico in un gruppo multi-contenitore.

L'indirizzo IP di un contenitore in genere non cambia tra gli aggiornamenti, ma non è comunque detto che rimanga invariato. Se distribuito nello stesso host sottostante, il gruppo di contenitori mantiene il proprio indirizzo IP. Anche se raramente e anche se Istanze di Azure Container cerca sempre di eseguire la ridistribuzione nello stesso host, esistono alcuni eventi interni di Azure che possono causare la ridistribuzione in un host diverso. Per attenuare questo problema, usare sempre un'etichetta del nome DNS per le istanze di contenitore.

Non è possibile aggiornare i gruppi di contenitori terminati o eliminati. Se arrestato (ovvero in stato *Terminated*) o eliminato, il gruppo di contenitori viene distribuito come nuovo.

## <a name="properties-that-require-container-delete"></a>Proprietà che richiedono l'eliminazione del contenitore

Come accennato in precedenza, non tutte le proprietà del gruppo di contenitori possono essere aggiornate. Ad esempio, per modificare le porte o riavviare i criteri di un contenitore, è necessario innanzitutto eliminare il gruppo di contenitori, per poi crearlo di nuovo.

Queste proprietà richiedono l'eliminazione del gruppo di contenitori prima della ridistribuzione:

* Tipo di sistema operativo
* CPU
* Memoria
* Criterio di riavvio
* Porte

Quando si elimina un gruppo di contenitori e lo si ricrea, questo non viene "ridistribuito", ma creato nuovo. Il pull di tutti i livelli dell'immagine viene eseguito dai dati aggiornati del registro, non da quelli memorizzati nella cache da una distribuzione precedente. L'indirizzo IP del contenitore può cambiare anche a causa della distribuzione in un altro host sottostante.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è menzionato più volte il **gruppo di contenitori**. Ogni contenitore di Istanze di Azure Container viene distribuito in un gruppo di contenitori, che può contenere più contenitori.

[Gruppi di contenitori in Istanze di Azure Container](container-instances-container-groups.md)

[Distribuire gruppi multi-contenitore](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
