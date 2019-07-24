---
title: Aggiornamento di Registro Azure Container classico
description: Sfruttare il set di funzionalità ampliato dei registri di contenitori gestiti di base, standard e premium tramite l'aggiornamento del registro di contenitori classico non gestito.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310385"
---
# <a name="upgrade-a-classic-container-registry"></a>Aggiornamento del registro contenitori classico

Il Registro Azure Container (ACR) è disponibile su più livelli di servizio, noti come [SKU](container-registry-skus.md). La versione iniziale di ACR offriva un singolo SKU, classico, che non disponeva di numerose funzionalità incluse negli SKU di base, standard e premium (noti collettivamente come registri*gestiti*).

Lo SKU classico verrà deprecato e non sarà disponibile dopo il 2019 aprile. Questo articolo descrive in dettaglio come eseguire la migrazione del registro classico non gestito a uno degli SKU gestiti in modo da poterne sfruttare il set di funzionalità avanzate.

## <a name="why-upgrade"></a>Ragioni dell'aggiornamento

Lo SKU del registro di sistema classico verrà deprecato e non sarà disponibile dopo il **2019 aprile**. Tutti i registri classici esistenti devono essere aggiornati prima del 2019 aprile. Le funzionalità di gestione del portale dei registri classici verranno eliminate gradualmente. La creazione di nuovi registri classici verrà disabilitata dopo il 2019 aprile.

A causa della deprecazione pianificata e delle funzionalità limitate dei registri classici non gestiti, tutti i registri classici devono essere aggiornati ai registri gestiti (Basic, standard o Premium). Questi SKU di livello superiore integrano in modo più specifico il registro nelle funzionalità di Azure. Per ulteriori informazioni sui prezzi e sulle funzionalità dei diversi livelli di servizio, vedere [container Registry SKU](container-registry-skus.md).

Il registro classico dipende dall'account di archiviazione per cui Azure esegue il provisioning automatico nella sottoscrizione di Azure al momento della creazione del registro. Al contrario, gli SKU Basic, Standard e Premium sfruttano i vantaggi delle [funzionalità di archiviazione avanzate](container-registry-storage.md) di Azure gestendo in modo automatico e trasparente l'archiviazione delle immagini. Non viene creato un account di archiviazione separato nella propria sottoscrizione.

Un archivio di registro gestito è caratterizzato dai vantaggi seguenti:

* Le immagini di contenitori sono [crittografati a riposo](container-registry-storage.md#encryption-at-rest).
* Le immagini vengono archiviate con l' [archiviazione](container-registry-storage.md#geo-redundant-storage)con ridondanza geografica, garantendo il backup delle immagini con la replica in più aree (solo SKU Premium).
* Possibilità di [cambiare SKU](container-registry-skus.md#changing-skus) liberamente, consentendo una maggiore velocità effettiva, quando si sceglie uno SKU di livello superiore. Con ogni SKU, il Registro Azure Container può soddisfare i requisiti di velocità effettiva man mano che aumentano le esigenze.
* Il modello di sicurezza unificata per il registro e il relativo archivio consente la gestione semplificata dei diritti. È necessario gestire solo le autorizzazioni per il registro di contenitori, evitando la necessità di gestire anche le autorizzazioni per un account di archiviazione separato.

Per altri dettagli sull'archiviazione di immagini in Registro Azure Container, vedere [Archiviazione di immagini del contenitore in Registro Azure Container](container-registry-storage.md).

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Quando si aggiorna un registro classico a un registro gestito, Azure deve copiare tutte le immagini del contenitore esistenti dall'account di archiviazione creato da ACR nella sottoscrizione a un account di archiviazione gestito da Azure. A seconda delle dimensioni del registro, questo processo può richiedere da alcuni minuti a diverse ore. Per finalità di valutazione, prevedere un tempo di migrazione di circa 0,5 GiB al minuto.

Durante il processo di conversione `docker push` , le operazioni vengono disabilitate durante l'ultimo 10% della migrazione. `docker pull`continua a funzionare normalmente.

Non eliminare o modificare i contenuti dell'account di archiviazione di backup del registro classico durante il processo di conversione. Questa operazione può causare il danneggiamento delle immagini di contenitori.

Al termine della migrazione, l'account di archiviazione nella sottoscrizione che originariamente ha eseguito il backup del registro classico non viene più usato da Azure Container Registry. Dopo aver verificato che la migrazione ha avuto esito positivo, prendere in considerazione l'eliminazione dell'account di archiviazione per ridurre i costi.

>[!IMPORTANT]
> L'aggiornamento dalla versione classica a uno degli SKU gestiti è un **processo unidirezionale**. Dopo aver convertito un registro classico in uno di base, standard o premium, non è possibile ripristinare la versione classica. È tuttavia possibile, passare liberamente a uno degli SKU gestiti con una capacità sufficiente per il registro.

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

È possibile aggiornare un registro classico non gestito a uno degli SKU gestiti in diversi modi. Nelle sezioni seguenti viene descritto il processo per l'uso dell' [interfaccia][azure-cli] and the [Azure portal][azure-portal]della riga di comando di Azure.

## <a name="upgrade-in-azure-cli"></a>Aggiornamento tramite l'interfaccia della riga di comando di Azure

Per aggiornare un registro classico nell'interfaccia della riga di comando di Azure, eseguire il comando [AZ ACR Update][az-acr-update] e specificare il nuovo SKU per il registro di sistema. Nell'esempio seguente, un registro classico denominato *myclassicregistry* viene aggiornato a uno SKU premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Al termine della migrazione, l'output dovrebbe essere simile al seguente. `sku` Si noti che è "Premium" `storageAccount` e è `null`, a indicare che Azure ora gestisce lo spazio di archiviazione delle immagini per questo registro.

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

Se viene visualizzato un errore simile, eseguire di nuovo il comando [AZ ACR Update][az-acr-update] e specificare lo SKU suggerito, ovvero lo SKU di livello più alto successivo che può contenere le immagini.

## <a name="upgrade-in-azure-portal"></a>Aggiornamento tramite il portale di Azure

Quando si aggiorna un registro classico usando il portale di Azure, Azure seleziona automaticamente lo SKU standard o Premium, a seconda dello SKU che può contenere le immagini. Ad esempio, se il registro contiene meno di 100 GiB nelle immagini, Azure seleziona e converte automaticamente il registro classico in standard (100 GiB massimo).

Per aggiornare il registro classico tramite il portale di Azure, passare alla **Panoramica** del registro contenitori e selezionare **Aggiorna a registro gestito**.

![Pulsante di aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-01-upgrade]

Selezionare **OK** per confermare che si vuole eseguire l'aggiornamento a un registro gestito.

Durante la migrazione, il portale indica che lo **Stato provisioning** del registro è *Aggiornamento*. Come indicato in precedenza `docker push` , le operazioni vengono disabilitate durante l'ultimo 10% della migrazione. Non eliminare o aggiornare l'account di archiviazione usato dal registro classico mentre è in corso la migrazione. questa operazione può causare un danneggiamento dell'immagine.

![Avanzamento dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-03-updating]

Al termine della migrazione, lo **stato** di provisioning indica *succeeded*ed è possibile riprendere le normali operazioni con il registro.

![Stato di completamento dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-04-updated]

## <a name="next-steps"></a>Passaggi successivi

Una volta eseguito l'aggiornamento di un registro classico a un registro gestito, Azure non usa più l'account di archiviazione che originariamente supportava il registro classico. Per ridurre i costi, prendere in considerazione l'eliminazione dell'account di archiviazione o del contenitore BLOB all'interno dell'account che contiene le immagini di contenitori precedenti.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com