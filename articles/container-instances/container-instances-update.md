---
title: Aggiornamento del gruppo di contenitori
description: Informazioni su come aggiornare i contenitori in esecuzione nei gruppi di contenitori in Istanze di Azure Container.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533310"
---
# <a name="update-containers-in-azure-container-instances"></a>Aggiornare i contenitori in Istanze di Azure Container

Durante il normale funzionamento delle istanze di contenitore, può risultare necessario aggiornare i contenitori in esecuzione in un [gruppo di contenitori](container-instances-container-groups.md). Ad esempio, è possibile aggiornare la versione dell'immagine, modificare un nome DNS, aggiornare le variabili di ambiente o aggiornare lo stato di un contenitore la cui applicazione si è arrestata in modo anomalo.

> [!NOTE]
> Non è possibile aggiornare i gruppi di contenitori terminati o eliminati. Dopo che un gruppo di contenitori è stato terminato (si trova nello stato SUCCEEDED o Failed) o è stato eliminato, il gruppo deve essere distribuito come nuovo.

## <a name="update-a-container-group"></a>Aggiornare un gruppo di contenitori

Aggiornare i contenitori in un gruppo di contenitori in esecuzione ridistribuendo un gruppo esistente con almeno una proprietà modificata. Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esecuzione nel gruppo vengono riavviati sul posto, in genere nello stesso host contenitore sottostante.

Ridistribuire un gruppo di contenitori esistente eseguendo il comando Crea (o tramite il portale di Azure) e specificare il nome di un gruppo esistente. Modificare almeno una proprietà valida del gruppo quando si esegue il comando create per attivare la ridistribuzione e lasciare invariate le proprietà rimanenti oppure continuare a usare i valori predefiniti. Non tutte le proprietà del gruppo contenitore sono valide per la ridistribuzione. Per un elenco delle proprietà non supportate, vedere [Properties that require delete](#properties-that-require-container-delete) (Proprietà che richiedono l'eliminazione).

L'esempio di interfaccia della riga di comando di Azure seguente aggiorna un gruppo di contenitori con una nuova etichetta del nome DNS. Poiché la proprietà dell'etichetta del nome DNS del gruppo è una che può essere aggiornata, il gruppo di contenitori viene ridistribuito e i contenitori vengono riavviati.

Distribuzione iniziale con l'etichetta del nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aggiornare il gruppo di contenitori con una nuova etichetta del nome DNS, *MyApplication*e lasciare invariate le proprietà rimanenti:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
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

[Arrestare o avviare manualmente i contenitori nelle istanze di contenitore di Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
