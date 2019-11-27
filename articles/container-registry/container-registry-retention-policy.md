---
title: Criteri per mantenere manifesti senza tag
description: Informazioni su come abilitare i criteri di conservazione nel registro contenitori di Azure per l'eliminazione automatica dei manifesti senza tag dopo un periodo definito.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454824"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Impostare un criterio di conservazione per i manifesti senza tag

Azure Container Registry offre la possibilità di impostare criteri di *conservazione* per i manifesti di immagini archiviati che non dispongono di tag associati (*manifesti senza tag*). Quando un criterio di conservazione è abilitato, i manifesti senza tag nel registro di sistema vengono eliminati automaticamente dopo un certo numero di giorni impostati. Questa funzionalità impedisce al registro di sistema di riempire gli artefatti che non sono necessari e consente di risparmiare sui costi di archiviazione. Se l'attributo `delete-enabled` di un manifesto senza tag è impostato su `false`, il manifesto non può essere eliminato e i criteri di conservazione non sono applicabili.

È possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di comando in questo articolo. Se si vuole usarlo localmente, è richiesta la versione 2.0.74 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Impostare un criterio di conservazione con cautela. i dati dell'immagine eliminati sono irreversibili. Se si dispone di sistemi che effettuano il pull di immagini dal digest del manifesto (in contrapposizione al nome dell'immagine), non impostare un criterio di conservazione per i manifesti senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Anziché estrarre il manifesto, è consigliabile adottare uno schema di *tag univoco* , una [procedura consigliata](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Solo un registro contenitori **Premium** può essere configurato con un criterio di conservazione. Per informazioni sui livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md).
* È possibile impostare un criterio di conservazione solo per i manifesti senza tag.
* I criteri di conservazione sono attualmente applicabili solo ai manifesti senza tag *dopo* l'abilitazione dei criteri. I manifesti senza tag esistenti nel registro di sistema non sono soggetti ai criteri. Per eliminare manifesti senza tag esistenti, vedere esempi in [eliminare immagini del contenitore in Azure container Registry](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Informazioni sui criteri di conservazione

Azure Container Registry fa riferimento al conteggio dei manifesti nel registro di sistema. Quando un manifesto è senza tag, verifica i criteri di conservazione. Se un criterio di conservazione è abilitato, un'operazione di eliminazione del manifesto viene accodata, con una data specifica, in base al numero di giorni impostati nei criteri.

Un processo di gestione delle code separato elabora costantemente i messaggi, ridimensionando in base alle esigenze. Si supponga, ad esempio, di non contrassegnare due manifesti a distanza di 1 ora in un registro con criteri di conservazione di 30 giorni. Verranno accodati due messaggi. Quindi, 30 giorni dopo, a distanza di un'ora circa, i messaggi vengono recuperati dalla coda ed elaborati, supponendo che i criteri siano ancora attivi.

## <a name="set-a-retention-policy---cli"></a>Impostare un criterio di conservazione-CLI

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per impostare un criterio di conservazione per i manifesti senza tag in un registro.

### <a name="enable-a-retention-policy"></a>Abilita criteri di conservazione

Per impostazione predefinita, non viene impostato alcun criterio di conservazione in un registro contenitori. Per impostare o aggiornare i criteri di conservazione, eseguire il comando [AZ ACR config conservazione Update][az-acr-config-retention-update] nell'interfaccia della riga di comando di Azure. È possibile specificare un numero di giorni compreso tra 0 e 365 per mantenere i manifesti senza tag. Se non si specifica un numero di giorni, il comando imposta un valore predefinito di 7 giorni. Al termine del periodo di memorizzazione, tutti i manifesti senza tag nel registro di sistema vengono eliminati automaticamente.

Nell'esempio seguente vengono impostati i criteri di conservazione di 30 giorni per i manifesti senza tag *nel registro di*sistema del registro di sistema:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Nell'esempio seguente vengono impostati i criteri per eliminare eventuali manifesti nel registro di sistema non appena vengono contrassegnati. Per creare questo criterio, impostare un periodo di memorizzazione di 0 giorni. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Convalidare un criterio di conservazione

Se si Abilita il criterio precedente con un periodo di memorizzazione di 0 giorni, è possibile verificare rapidamente che i manifesti senza tag vengano eliminati:

1. Eseguire il push di un'immagine di test `hello-world:latest` immagine nel registro o sostituirla con un'altra immagine di test scelta.
1. Contrassegno l'immagine `hello-world:latest`, ad esempio, usando il comando [AZ ACR repository contrassegno][az-acr-repository-untag] . Il manifesto senza tag rimane nel registro di sistema.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Entro pochi secondi viene eliminato il manifesto senza tag. È possibile verificare l'eliminazione elencando i manifesti nel repository, ad esempio usando il comando [AZ ACR repository Show-manifests][az-acr-repository-show-manifests] . Se l'immagine di test è l'unica nel repository, il repository stesso viene eliminato.

### <a name="disable-a-retention-policy"></a>Disabilitare i criteri di conservazione

Per visualizzare i criteri di conservazione impostati in un registro, eseguire il comando [AZ ACR config retention Show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --registry myregistry
```

Per disabilitare i criteri di conservazione in un registro di sistema, eseguire il comando [AZ ACR config conservazione Update][az-acr-config-retention-update] e impostare `--status disabled`:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
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
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
