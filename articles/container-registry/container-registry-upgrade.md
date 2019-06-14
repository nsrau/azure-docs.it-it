---
title: Aggiornamento di Registro Azure Container classico
description: Sfruttare il set di funzionalità ampliato dei registri di contenitori gestiti di base, standard e premium tramite l'aggiornamento del registro di contenitori classico non gestito.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480339"
---
# <a name="upgrade-a-classic-container-registry"></a>Aggiornamento del registro contenitori classico

Il Registro Azure Container (ACR) è disponibile su più livelli di servizio, noti come [SKU](container-registry-skus.md). La versione iniziale di ACR offriva un singolo SKU, classico, che non disponeva di numerose funzionalità incluse negli SKU di base, standard e premium (noti collettivamente come registri*gestiti*).

Lo SKU classico verrà deprecato e non sarà disponibile dopo il mese di aprile 2019. Questo articolo descrive in dettaglio come eseguire la migrazione del registro classico non gestito a uno degli SKU gestiti in modo da poterne sfruttare il set di funzionalità avanzate.

## <a name="why-upgrade"></a>Ragioni dell'aggiornamento

Il registro classico SKU è in corso **deprecate**e non sarà disponibile dopo **aprile 2019**. Tutti i registri classici esistenti devono essere aggiornati prima di aprile 2019. Verranno gradualmente le funzionalità del portale di gestione dei registri classici. La creazione di nuovi registri classici verrà disabilitata dopo aprile 2019.

A causa della dichiarazione obsolescenza pianificata e funzionalità limitate dei registri non gestiti classici, devono essere aggiornati tutti i registri classici ai registri gestiti (Basic, Standard o Premium). Questi SKU di livello superiore integrano in modo più specifico il registro nelle funzionalità di Azure. Per altre informazioni sui prezzi e funzionalità di diversi livelli di servizio, vedere [SKU del registro contenitori](container-registry-skus.md).

Il registro classico dipende dall'account di archiviazione per cui Azure esegue il provisioning automatico nella sottoscrizione di Azure al momento della creazione del registro. Al contrario, gli SKU Basic, Standard e Premium sfruttano i vantaggi delle [funzionalità di archiviazione avanzate](container-registry-storage.md) di Azure gestendo in modo automatico e trasparente l'archiviazione delle immagini. Non viene creato un account di archiviazione separato nella propria sottoscrizione.

Un archivio di registro gestito è caratterizzato dai vantaggi seguenti:

* Le immagini di contenitori sono [crittografati a riposo](container-registry-storage.md#encryption-at-rest).
* Le immagini vengono archiviate usando [archiviazione con ridondanza geografica](container-registry-storage.md#geo-redundant-storage), garantendo backup delle immagini con replica in più aree (solo SKU Premium).
* Possibilità di [cambiare SKU](container-registry-skus.md#changing-skus) liberamente, consentendo una maggiore velocità effettiva, quando si sceglie uno SKU di livello superiore. Con ogni SKU, il Registro Azure Container può soddisfare i requisiti di velocità effettiva man mano che aumentano le esigenze.
* Il modello di sicurezza unificata per il registro e il relativo archivio consente la gestione semplificata dei diritti. È necessario gestire solo le autorizzazioni per il registro di contenitori, evitando la necessità di gestire anche le autorizzazioni per un account di archiviazione separato.

Per altri dettagli sull'archiviazione di immagini in Registro Azure Container, vedere [Archiviazione di immagini del contenitore in Registro Azure Container](container-registry-storage.md).

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Quando si aggiorna un registro classico a un registro gestito, Azure deve copiare tutte le immagini del contenitore esistente dall'account di archiviazione creato al registro contenitori di AZURE nella sottoscrizione in un account di archiviazione gestito da Azure. A seconda delle dimensioni del registro, questo processo può richiedere da alcuni minuti a diverse ore. A scopo di valutazione, prevedere un tempo di migrazione di circa 0,5 GiB al minuto.

Durante il processo di conversione, `docker push` operazioni sono disabilitate durante le ultime 10% della migrazione. `docker pull` continua a funzionare normalmente.

Non eliminare o modificare i contenuti dell'account di archiviazione di backup del registro classico durante il processo di conversione. Questa operazione può causare il danneggiamento delle immagini di contenitori.

Al termine della migrazione, l'account di archiviazione nella sottoscrizione inizialmente usato come backup del registro classico non è non è più usato dal registro contenitori di Azure. Dopo aver verificato che la migrazione ha avuto esito positivo, prendere in considerazione l'eliminazione dell'account di archiviazione per ridurre i costi.

>[!IMPORTANT]
> L'aggiornamento dalla versione classica a uno degli SKU gestiti è un **processo unidirezionale**. Dopo aver convertito un registro classico in uno di base, standard o premium, non è possibile ripristinare la versione classica. È tuttavia possibile, passare liberamente a uno degli SKU gestiti con una capacità sufficiente per il registro.

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

È possibile aggiornare un registro classico non gestito a uno degli SKU gestiti in diversi modi. Nelle sezioni seguenti viene descritto il processo per l'uso dell'[interfaccia della riga di comando di Azure][azure-cli] e del [portale di Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Aggiornamento tramite l'interfaccia della riga di comando di Azure

Per aggiornare un registro classico nell'interfaccia della riga di comando di Azure, eseguire il comando [az acr update][az-acr-update] e specificare il nuovo SKU per il registro. Nell'esempio seguente, un registro classico denominato *myclassicregistry* viene aggiornato a uno SKU premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Al termine della migrazione, l'output dovrebbe essere simile al seguente. Si noti che il `sku` è "Premium" e il `storageAccount` è `null`, che indica che Azure ora gestisce l'archiviazione di immagini per questo registro.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Se per il registro gestito si specifica uno SKU la cui capacità massima è inferiore alle dimensioni del registro classico, si riceverà un messaggio di errore simile al seguente.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Se si riceve un errore simile, eseguire nuovamente il comando [az acr update][az-acr-update] e specificare lo SKU suggerito, ossia lo SKU di livello più alto successivo in grado di gestire le immagini.

## <a name="upgrade-in-azure-portal"></a>Aggiornamento tramite il portale di Azure

Quando si aggiorna un registro classico tramite il portale di Azure, Azure seleziona automaticamente: Standard o Premium SKU, a seconda di quale SKU può gestire le immagini. Ad esempio, se il Registro di sistema contiene meno di 100 GiB di immagini, Azure automaticamente Seleziona e converte il registro classico in Standard (100 GiB massimo).

Per aggiornare il registro classico tramite il portale di Azure, passare alla **Panoramica** del registro contenitori e selezionare **Aggiorna a registro gestito**.

![Pulsante di aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-01-upgrade]

Selezionare **OK** per confermare che si vuole eseguire l'aggiornamento a un registro gestito.

Durante la migrazione, il portale indica che lo **Stato provisioning** del registro è *Aggiornamento*. Come accennato in precedenza, `docker push` operazioni sono disabilitate durante le ultime 10% della migrazione. Eseguire operazioni non delete o update l'account di archiviazione usato dal registro classico mentre è in corso, in questo modo la migrazione può causare un danneggiamento delle immagini.

![Avanzamento dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-03-updating]

Durante la migrazione è completata, il **lo stato di Provisioning** indica *Succeeded*, sarà possibile riprendere le normali operazioni con il Registro di sistema.

![Stato di completamento dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-04-updated]

## <a name="next-steps"></a>Passaggi successivi

Dopo che è stato eseguito l'aggiornamento di un registro classico a un registro gestito, Azure non usa più account di archiviazione inizialmente usato come backup del Registro di sistema di distribuzione classica. Per ridurre i costi, prendere in considerazione l'eliminazione dell'account di archiviazione o del contenitore BLOB all'interno dell'account che contiene le immagini di contenitori precedenti.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com