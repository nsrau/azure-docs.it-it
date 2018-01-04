---
title: Aggiornamento del Registro di sistema un contenitore di Azure classico
description: "Sfruttare espanso set di funzionalità di base, Standard e Premium gestiti registri contenitore aggiornando il Registro di sistema di contenitore classica non gestito."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 19090bb69d7165c1e904450dc93b925e23e44782
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-a-classic-container-registry"></a>L'aggiornamento di un registro di sistema contenitore classica

Azure contenitore del Registro di sistema (ACR) è disponibile in diversi livelli di servizio [noto come SKU](container-registry-skus.md). La versione iniziale di ACR offerto un singolo SKU, classica, che non dispone di numerose funzionalità inerente a Basic, Standard e SKU Premium (noti collettivamente come *gestiti* registri). In questo articolo viene illustrato come eseguire la migrazione del Registro di sistema classico non gestito a uno dei SKU gestito in modo che è possibile sfruttare i set di funzionalità avanzate.

## <a name="why-upgrade"></a>Vantaggi dell'aggiornamento

A causa di funzionalità limitate di registri classica non gestita, è consigliabile utilizzare tutti i registri classica registri gestiti aggiornati al livello Basic, Standard o Premium. Questi SKU di livello superiore integrano in modo più specifico il registro nelle funzionalità di Azure.

Forniscono i registri gestiti:

* Integrazione di Azure Active Directory per [singoli account di accesso](container-registry-authentication.md#individual-login-with-azure-ad)
* il supporto per l'eliminazione di immagini e tag
* [Replica geografica](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

Ma soprattutto, un registro classico dipende dall'account di archiviazione per cui Azure esegue il provisioning automatico nella sottoscrizione di Azure al momento della creazione del registro. Gli SKU di base, standard e premium invece sfruttano l'*archivio gestito*. Questo vuol dire che Azure gestisce in modo trasparente l'archiviazione delle immagini per l'utente: nella propria sottoscrizione non viene creato un account di archiviazione diverso.

Archiviazione del Registro di sistema gestito offre i vantaggi seguenti:

* Le immagini di contenitori sono [crittografati a riposo](../storage/common/storage-service-encryption.md).
* Le immagini vengono archiviate usando l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage), garantendo un backup delle immagini con la replica su più aree.
* Possibilità di liberamente [spostare tra SKU](container-registry-skus.md#changing-skus), consentendo una velocità effettiva, quando si sceglie uno SKU di livello superiore. Con ogni SKU, il Registro contenitori di Azure può soddisfare i requisiti di velocità effettiva man mano che aumentano le esigenze.
* Modello di sicurezza unificata per il Registro di sistema e lo spazio di archiviazione fornisce la gestione semplificata dei diritti. Gestire le autorizzazioni solo per il Registro di sistema di contenitore, senza dover gestire anche le autorizzazioni per un account di archiviazione separata.

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Quando si modifica un registro di sistema classico in un registro di sistema gestito, Azure necessario copiare tutte le immagini contenitore esistente dall'account di archiviazione creato ACR nella sottoscrizione di un account di archiviazione gestito da Azure. A seconda delle dimensioni del Registro di sistema, questo processo può richiedere alcuni minuti a diverse ore.

Durante il processo di conversione, tutti `docker push` operazioni vengono bloccate, mentre `docker pull` continua a funzionare.

Non eliminare o modificare il contenuto dell'account di archiviazione di backup del Registro di sistema classico durante il processo di conversione. Questo può causare il danneggiamento delle immagini contenitore.

Una volta completata la migrazione, l'account di archiviazione nella sottoscrizione di cui è eseguito originariamente il Registro di sistema classico è più utilizzata da record. Dopo aver verificato che la migrazione ha avuto esito positivo, prendere in considerazione l'eliminazione dell'account di archiviazione per ridurre i costi.

>[!IMPORTANT]
> L'aggiornamento dalla versione classica a uno dei SKU gestito è un **processo unidirezionale**. Dopo aver convertito un registro di sistema di base, Standard o Premium classica, è possibile ripristinare classica. È tuttavia possibile, spostare liberamente tra SKU gestito con una capacità sufficiente per il Registro di sistema.

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

È possibile aggiornare un registro di sistema classico non gestito a uno dei SKU gestiti in diversi modi. Nelle sezioni seguenti viene descritto il processo per l'utilizzo di [CLI di Azure] [ azure-cli] e [portale di Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Aggiornamento in CLI di Azure

Per aggiornare un registro di sistema classica l'interfaccia CLI di Azure, eseguire il [aggiornamento acr az] [ az-acr-update] comando e specificare l'unità SKU di nuovo per il Registro di sistema. Nell'esempio seguente, un registro di sistema classico denominato *myclassicregistry* viene aggiornato per lo SKU Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Una volta completata la migrazione, si dovrebbe vedere output simile al seguente. Si noti che il `sku` "Premium" e `storageAccount` è "null", che indica che Azure ora gestisce l'archiviazione di immagini per questo registro di sistema.

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

Se si specifica un registro di sistema gestito SKU la cui capacità massima è inferiore alle dimensioni del Registro di sistema classico, si riceverà un messaggio di errore simile al seguente.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Se si riceve un errore simile, eseguire il [aggiornamento acr az] [ az-acr-update] comando nuovo e specificare la SKU suggerita, che è la SKU di livello più alto successivo che è in grado di gestire le immagini.

## <a name="upgrade-in-azure-portal"></a>Eseguire l'aggiornamento nel portale di Azure

Quando si aggiorna un registro di sistema classico tramite il portale di Azure, Azure viene selezionata automaticamente la SKU di livello più basso che è in grado di gestire le immagini. Ad esempio, se il Registro di sistema contiene 12 GiB nelle immagini, Azure automaticamente consente di selezionare e converte il Registro di sistema classico Standard (massimo di 100 GiB).

Per aggiornare il Registro di sistema classico tramite il portale di Azure, passare al Registro di sistema contenitore **Panoramica** e selezionare **l'aggiornamento al Registro di sistema gestito**.

![Pulsante nell'interfaccia utente del portale Azure l'aggiornamento del Registro di sistema classico][update-classic-01-upgrade]

Selezionare **OK** per confermare che si desidera eseguire l'aggiornamento a un registro di sistema gestito.

![Conferma nel portale di Azure dell'interfaccia utente di aggiornamento del Registro di sistema classico][update-classic-02-confirm]

Durante la migrazione, il portale indica che il Registro di sistema **lo stato di Provisioning** è *aggiornamento*. Come accennato in precedenza, `docker push` operazioni sono disabilitate durante la migrazione e non è necessario eliminare o aggiornamento dell'account di archiviazione utilizzato dal Registro di sistema classico durante la migrazione in corso--questa operazione può provocare il danneggiamento dei immagine.

![Stato nel portale di Azure dell'interfaccia utente di aggiornamento del Registro di sistema classico][update-classic-03-updating]

Al termine, la migrazione di **lo stato di Provisioning** indica *Succeeded*, ed è nuovamente possibile `docker push` nel Registro di sistema.

![Lo stato di completamento nel portale di Azure dell'interfaccia utente di aggiornamento del Registro di sistema classico][update-classic-04-updated]

## <a name="next-steps"></a>Passaggi successivi

Dopo che è stato eseguito l'aggiornamento di un registro di sistema di base classica, Standard o Premium, Azure non utilizzerà più account di archiviazione eseguito originariamente il Registro di sistema classica. Per ridurre il costo, provare a eliminare l'account di archiviazione o il contenitore di Blob all'interno dell'account che contiene le immagini contenitore precedente.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com