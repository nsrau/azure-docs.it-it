---
title: Criteri per mantenere manifesti senza tag in Azure Container Registry
description: Informazioni su come abilitare i criteri di conservazione nel registro contenitori di Azure per l'eliminazione automatica dei manifesti senza tag dopo un periodo definito.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306003"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Impostare un criterio di conservazione per i manifesti senza tag

Azure Container Registry offre la possibilità di impostare criteri di *conservazione* per i manifesti di immagini archiviati che non dispongono di tag associati (*manifesti senza tag*). Quando un criterio di conservazione è abilitato, i manifesti senza tag nel registro di sistema vengono eliminati automaticamente dopo un certo numero di giorni impostati. Questa funzionalità impedisce al registro di sistema di riempire gli artefatti che non sono necessari e consente di risparmiare sui costi di archiviazione. Se l' `delete-enabled` attributo di un manifesto senza tag è impostato su `false`, non è possibile eliminare il manifesto e i criteri di conservazione non sono applicabili.

È possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di comando in questo articolo. Se si vuole usarlo localmente, è richiesta la versione 2.0.74 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Impostare un criterio di conservazione con cautela. i dati dell'immagine eliminati sono irreversibili. Se si dispone di sistemi che effettuano il pull di immagini dal digest del manifesto (in contrapposizione al nome dell'immagine), non impostare un criterio di conservazione per i manifesti senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Anziché estrarre il manifesto, è consigliabile adottare uno schema di *tag univoco* , una [procedura consigliata](container-registry-image-tag-version.md).

Se si vuole eliminare singoli tag di immagine o manifesti usando i comandi dell'interfaccia della riga di comando di Azure, vedere [eliminare immagini del contenitore in azure container Registry](container-registry-delete.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Solo un registro contenitori **Premium** può essere configurato con un criterio di conservazione. Per informazioni sui livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md).
* È possibile impostare un criterio di conservazione solo per i manifesti senza tag.

## <a name="set-a-retention-policy---cli"></a>Impostare un criterio di conservazione-CLI

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per impostare un criterio di conservazione per i manifesti senza tag in un registro.

### <a name="enable-a-retention-policy"></a>Abilita criteri di conservazione

Per impostazione predefinita, non viene impostato alcun criterio di conservazione in un registro contenitori. Per impostare o aggiornare i criteri di conservazione, eseguire il comando [AZ ACR config conservazione Update][az-acr-config-retention-update] nell'interfaccia della riga di comando di Azure. È possibile specificare un numero di giorni compreso tra 0 e 365 per mantenere i manifesti senza tag. Se non si specifica un numero di giorni, il comando imposta un valore predefinito di 7 giorni. Al termine del periodo di memorizzazione, tutti i manifesti senza tag nel registro di sistema vengono eliminati automaticamente.

Nell'esempio seguente vengono impostati i criteri di conservazione di 30 giorni per i manifesti senza tag *nel registro di*sistema del registro di sistema:

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

Nell'esempio seguente vengono impostati i criteri per eliminare eventuali manifesti nel registro di sistema non appena vengono contrassegnati. Per creare questo criterio, impostare un periodo di memorizzazione di 0 giorni:

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Disabilitare i criteri di conservazione

Per visualizzare i criteri di conservazione impostati in un registro, eseguire il comando [AZ ACR config retention Show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --name myregistry
```

Per disabilitare i criteri di conservazione in un registro di sistema, eseguire il comando [AZ ACR config conservazione Update][az-acr-config-retention-update] e impostare `--status disabled`:

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Impostare un criterio di conservazione-portale

È anche possibile impostare i criteri di conservazione del registro di sistema nella [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come usare il portale per impostare un criterio di conservazione per i manifesti senza tag in un registro di sistema.

### <a name="enable-a-retention-policy"></a>Abilita criteri di conservazione

1. Passare al registro contenitori di Azure. In **criteri**selezionare **conservazione** (anteprima).
1. In **stato**selezionare **abilitato**.
1. Selezionare un numero di giorni compreso tra 0 e 365 per mantenere i manifesti senza tag. Selezionare **Salva**.

![Abilitare un criterio di conservazione in portale di Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Disabilitare i criteri di conservazione

1. Passare al registro contenitori di Azure. In **criteri**selezionare **conservazione** (anteprima).
1. In **stato**selezionare **disattivato**. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle opzioni per [eliminare immagini e repository](container-registry-delete.md) in Azure container Registry

* Informazioni su come [ripulire automaticamente](container-registry-auto-purge.md) le immagini e i manifesti selezionati da un registro

* Altre informazioni sulle opzioni per [bloccare le immagini e i manifesti](container-registry-image-lock.md) in un registro

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
