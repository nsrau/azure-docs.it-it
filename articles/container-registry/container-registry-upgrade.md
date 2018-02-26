---
title: Aggiornamento del registro contenitori di Azure classico
description: "Sfruttare il set di funzionalità ampliato dei registri di contenitori gestiti di base, standard e premium tramite l'aggiornamento del registro di contenitori classico non gestito."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 19090bb69d7165c1e904450dc93b925e23e44782
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-a-classic-container-registry"></a>Aggiornamento del registro contenitori classico

Il Registro contenitori di Azure (ACR) è disponibile su più livelli di servizio, noti come [SKU](container-registry-skus.md). La versione iniziale di ACR offriva un singolo SKU, classico, che non disponeva di numerose funzionalità incluse negli SKU di base, standard e premium (noti collettivamente come registri*gestiti*). Questo articolo descrive in dettaglio come eseguire la migrazione del registro classico non gestito a uno degli SKU gestiti in modo da poterne sfruttare il set di funzionalità avanzate.

## <a name="why-upgrade"></a>Ragioni dell'aggiornamento

A causa delle funzionalità limitate dei registri non gestiti classici, è consigliabile eseguire l'aggiornamento di tutti i registri classici ai registri gestiti di base, standard o premium. Questi SKU di livello superiore integrano in modo più specifico il registro nelle funzionalità di Azure.

I registri gestiti consentono:

* l'integrazione di Azure Active Directory per l'[accesso singolo](container-registry-authentication.md#individual-login-with-azure-ad)
* il supporto per l'eliminazione di immagini e tag
* [Replica geografica](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

Ma soprattutto, un registro classico dipende dall'account di archiviazione per cui Azure esegue il provisioning automatico nella sottoscrizione di Azure al momento della creazione del registro. Gli SKU di base, standard e premium invece sfruttano l'*archivio gestito*. Questo vuol dire che Azure gestisce in modo trasparente l'archiviazione delle immagini per l'utente: nella propria sottoscrizione non viene creato un account di archiviazione diverso.

Un archivio di registro gestito è caratterizzato dai vantaggi seguenti:

* Le immagini di contenitori sono [crittografati a riposo](../storage/common/storage-service-encryption.md).
* Le immagini vengono archiviate usando l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage), garantendo un backup delle immagini con la replica su più aree.
* Possibilità di [cambiare SKU](container-registry-skus.md#changing-skus) liberamente, consentendo una maggiore velocità effettiva, quando si sceglie uno SKU di livello superiore. Con ogni SKU, il Registro contenitori di Azure può soddisfare i requisiti di velocità effettiva man mano che aumentano le esigenze.
* Il modello di sicurezza unificata per il registro e il relativo archivio consente la gestione semplificata dei diritti. È necessario gestire solo le autorizzazioni per il registro di contenitori, evitando la necessità di gestire anche le autorizzazioni per un account di archiviazione separato.

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Quando si passa da un registro classico a uno gestito, Azure deve copiare tutte le immagini di contenitori esistenti dall'account di archiviazione creato da ACR nella sottoscrizione in un account di archiviazione gestito da Azure. A seconda delle dimensioni del registro, questo processo può richiedere da alcuni minuti a diverse ore.

Durante il processo di conversione, tutte le operazioni di `docker push` vengono bloccate, mentre `docker pull` continua a funzionare.

Non eliminare o modificare i contenuti dell'account di archiviazione di backup del registro classico durante il processo di conversione. Questa operazione può causare il danneggiamento delle immagini di contenitori.

Al termine della migrazione, l'account di archiviazione nella sottoscrizione inizialmente usato come backup del registro classico non viene più usato da ACR. Dopo aver verificato che la migrazione ha avuto esito positivo, prendere in considerazione l'eliminazione dell'account di archiviazione per ridurre i costi.

>[!IMPORTANT]
> L'aggiornamento dalla versione classica a uno degli SKU gestiti è un **processo unidirezionale**. Dopo aver convertito un registro classico in uno di base, standard o premium, non è possibile ripristinare la versione classica. È tuttavia possibile, passare liberamente a uno degli SKU gestiti con una capacità sufficiente per il registro.

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

È possibile aggiornare un registro classico non gestito a uno degli SKU gestiti in diversi modi. Nelle sezioni seguenti viene descritto il processo per l'uso dell'[interfaccia della riga di comando di Azure][azure-cli] e del [portale di Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Aggiornamento tramite l'interfaccia della riga di comando di Azure

Per aggiornare un registro classico nell'interfaccia della riga di comando di Azure, eseguire il comando [az acr update][az-acr-update] e specificare il nuovo SKU per il registro. Nell'esempio seguente, un registro classico denominato *myclassicregistry* viene aggiornato a uno SKU premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Al termine della migrazione, l'output dovrebbe essere simile al seguente. Si noti che `sku` è "Premium" e `storageAccount` è "null" e indica che Azure ora gestisce l'archiviazione di immagini per questo registro.

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

Quando si aggiorna un registro di sistema classico tramite il portale di Azure, Azure selezionata automaticamente lo SKU di livello più basso in grado di gestire le immagini. Ad esempio, se il registro contiene 12 GiB di immagini, Azure seleziona e converte automaticamente il registro classico in standard (100 GiB massimo).

Per aggiornare il registro classico tramite il portale di Azure, passare alla **Panoramica** del registro contenitori e selezionare **Aggiorna a registro gestito**.

![Pulsante di aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-01-upgrade]

Selezionare **OK** per confermare che si vuole eseguire l'aggiornamento a un registro gestito.

![Conferma dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-02-confirm]

Durante la migrazione, il portale indica che lo **Stato provisioning** del registro è *Aggiornamento*. Come accennato in precedenza, le operazioni di `docker push` sono disabilitate durante la migrazione e non è necessario eliminare o aggiornare l'account di archiviazione usato dal registro classico mentre è in corso la migrazione: questa operazione può provocare il danneggiamento delle immagini.

![Avanzamento dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-03-updating]

Al termine della migrazione in **Stato provisioning** è indicato *Operazione completata* ed è nuovamente possibile `docker push` nel registro.

![Stato di completamento dell'aggiornamento del registro classico nell'interfaccia utente del portale di Azure][update-classic-04-updated]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito l'aggiornamento di un registro classico alla versione di base, standard o premium, Azure non usa più l'account di archiviazione inizialmente usato come backup del registro classico. Per ridurre i costi, prendere in considerazione l'eliminazione dell'account di archiviazione o del contenitore BLOB all'interno dell'account che contiene le immagini di contenitori precedenti.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com