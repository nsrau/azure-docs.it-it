---
title: Aggiornamento del gruppo di contenitori
description: Informazioni su come aggiornare i contenitori in esecuzione nei gruppi di contenitori in Istanze di Azure Container.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cfc27de8caae98dd1c3065b5ed06433c4baaa5d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82928721"
---
# <a name="update-containers-in-azure-container-instances"></a>Aggiornare i contenitori in Istanze di Azure Container

Durante il normale funzionamento delle istanze di contenitore, può risultare necessario aggiornare i contenitori in esecuzione in un [gruppo di contenitori](./container-instances-container-groups.md). Ad esempio, potrebbe essere necessario aggiornare una proprietà, ad esempio una versione dell'immagine, un nome DNS o una variabile di ambiente, oppure aggiornare una proprietà in un contenitore con l'arresto anomalo dell'applicazione.

Aggiornare i contenitori in un gruppo di contenitori in esecuzione ridistribuendo un gruppo esistente con almeno una proprietà modificata. Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esecuzione nel gruppo vengono riavviati sul posto, in genere nello stesso host contenitore sottostante.

> [!NOTE]
> Non è possibile aggiornare i gruppi di contenitori terminati o eliminati. Dopo che un gruppo di contenitori è stato terminato (si trova nello stato SUCCEEDED o Failed) o è stato eliminato, il gruppo deve essere distribuito come nuovo. Vedere altre [limitazioni](#limitations).

## <a name="update-a-container-group"></a>Aggiornare un gruppo di contenitori

Per aggiornare un gruppo di contenitori esistente:

* Eseguire il comando Create (o usare il portale di Azure) e specificare il nome di un gruppo esistente 
* Modificare o aggiungere almeno una proprietà del gruppo che supporta l'aggiornamento quando si esegue la ridistribuzione. Alcune proprietà [non supportano gli aggiornamenti](#properties-that-require-container-delete).
* Impostare altre proprietà con i valori specificati in precedenza. Se non si imposta un valore per una proprietà, viene ripristinato il valore predefinito.

> [!TIP]
> Un [file YAML](./container-instances-container-groups.md#deployment) consente di mantenere la configurazione di distribuzione di un gruppo di contenitori e fornisce un punto di partenza per la distribuzione di un gruppo aggiornato. Se è stato usato un metodo diverso per creare il gruppo, è possibile esportare la configurazione in YAML usando [AZ container Export][az-container-export], 

### <a name="example"></a>Esempio

L'esempio di interfaccia della riga di comando di Azure seguente aggiorna un gruppo di contenitori con una nuova etichetta del nome DNS. Poiché la proprietà dell'etichetta del nome DNS del gruppo è una che può essere aggiornata, il gruppo di contenitori viene ridistribuito e i contenitori vengono riavviati.

Distribuzione iniziale con l'etichetta del nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aggiornare il gruppo di contenitori con una nuova etichetta del nome DNS, *MyApplication*e impostare le proprietà rimanenti con i valori usati in precedenza:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Vantaggi dell'aggiornamento

Il vantaggio principale dell'aggiornamento di un gruppo di contenitori esistente è una distribuzione più veloce. Quando si ridistribuisce un gruppo di contenitori esistente, viene eseguito il pull dei relativi livelli dell'immagine del contenitore da quelli memorizzati nella cache per la distribuzione precedente. Invece di eseguire il pull di tutti i livelli dell'immagine aggiornata dal registro come avviene con le nuove distribuzioni, viene eseguito il pull dei soli livelli modificati (se presenti).

Le applicazioni basate su immagini del contenitore di dimensioni maggiori, ad esempio Windows Server Core, possono ottenere un miglioramento significativo della velocità di distribuzione quando si esegue l'aggiornamento anziché eliminarle ed eseguire nuovamente la distribuzione.

## <a name="limitations"></a>Limitazioni

* Non tutte le proprietà di un gruppo di contenitori supportano gli aggiornamenti. Per modificare alcune proprietà di un gruppo di contenitori è necessario eliminare e ridistribuire il gruppo. Vedere [proprietà che richiedono l'eliminazione del contenitore](#properties-that-require-container-delete).
* Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esso presenti vengono riavviati. È possibile eseguire un aggiornamento o un riavvio sul posto di un contenitore specifico in un gruppo multi-contenitore.
* L'indirizzo IP di un gruppo di contenitori viene in genere mantenuto tra gli aggiornamenti, ma non è garantito che rimanga invariato. Se distribuito nello stesso host sottostante, il gruppo di contenitori mantiene il proprio indirizzo IP. Sebbene rare, esistono alcuni eventi interni di Azure che possono causare la ridistribuzione in un host diverso. Per attenuare questo problema, è consigliabile usare un'etichetta del nome DNS per le istanze di contenitore.
* Non è possibile aggiornare i gruppi di contenitori terminati o eliminati. Quando un gruppo di contenitori viene arrestato (si trova nello stato *terminato* ) o eliminato, il gruppo viene distribuito come nuovo.

## <a name="properties-that-require-container-delete"></a>Proprietà che richiedono l'eliminazione del contenitore

Non è possibile aggiornare tutte le proprietà del gruppo di contenitori. Per modificare il criterio di riavvio di un contenitore, ad esempio, è necessario innanzitutto eliminare il gruppo di contenitori, quindi crearlo di nuovo.

Le modifiche apportate a queste proprietà richiedono l'eliminazione del gruppo di contenitori prima della ridistribuzione:

* Tipo di sistema operativo
* Risorse CPU, memoria o GPU
* Criteri di riavvio
* Profilo di rete

Quando si elimina un gruppo di contenitori e lo si ricrea, questo non viene "ridistribuito", ma creato nuovo. Il pull di tutti i livelli dell'immagine viene eseguito dai dati aggiornati del registro, non da quelli memorizzati nella cache da una distribuzione precedente. L'indirizzo IP del contenitore può cambiare anche a causa della distribuzione in un altro host sottostante.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è menzionato più volte il **gruppo di contenitori**. Ogni contenitore di Istanze di Azure Container viene distribuito in un gruppo di contenitori, che può contenere più contenitori.

[Gruppi di contenitori in Istanze di Azure Container](./container-instances-container-groups.md)

[Distribuire un gruppo multi-contenitore](container-instances-multi-container-group.md)

[Arrestare o avviare manualmente i contenitori in Istanze di Azure Container](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
