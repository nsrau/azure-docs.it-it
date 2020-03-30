---
title: Criteri per mantenere i manifesti senza tag
description: Informazioni su come abilitare criteri di conservazione nel Registro di sistema del contenitore di Azure per l'eliminazione automatica dei manifesti senza tag dopo un periodo definito.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454824"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Impostare un criterio di conservazione per i manifesti senza tag

Registro di sistema del contenitore di Azure offre la possibilità di impostare criteri di *conservazione* per i manifesti di immagini archiviati a cui non sono associati tag *(manifesti senza tag*). Quando è abilitato un criterio di conservazione, i manifesti senza tag nel Registro di sistema vengono eliminati automaticamente dopo un numero di giorni impostati. Questa funzionalità impedisce al Registro di sistema di riempire con gli elementi che non sono necessari e consente di risparmiare sui costi di archiviazione. Se `delete-enabled` l'attributo di un manifesto `false`senza tag è impostato su , il manifesto non può essere eliminato e i criteri di conservazione non vengono applicati.

È possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di comando in questo articolo. Se si desidera utilizzarlo in locale, è necessaria la versione 2.0.74 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Impostare un criterio di conservazione con attenzione: i dati delle immagini eliminati sono UNRECOVERABLE. Se si dispone di sistemi che estraggono le immagini tramite il digest del manifesto (anziché il nome dell'immagine), non è consigliabile impostare un criterio di conservazione per i manifesti senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Solo un registro contenitori **Premium** può essere configurato con un criterio di conservazione. Per informazioni sui livelli del servizio Registro di sistema, vedere [SKU del Registro di sistema del contenitore](container-registry-skus.md)di Azure.For information about registry service tiers, see Azure Container Registry SKUs .
* È possibile impostare un criterio di conservazione solo per i manifesti senza tag.
* I criteri di conservazione attualmente si applicano solo ai manifesti senza tag *dopo* l'abilitazione del criterio. I manifesti senza tag esistenti nel Registro di sistema non sono soggetti ai criteri. Per eliminare i manifesti senza tag esistenti, vedere esempi in [Eliminare immagini contenitore nel Registro di](container-registry-delete.md)sistema contenitori di Azure.To delete existing untagged manifests, see examples in Delete container images in Azure Container Registry .

## <a name="about-the-retention-policy"></a>Informazioni sui criteri di conservazione

Il Registro di sistema del contenitore di Azure fa riferimento al conteggio dei manifesti nel Registro di sistema. Quando un manifesto viene senza tag, controlla i criteri di conservazione. Se un criterio di conservazione è abilitato, un'operazione di eliminazione del manifesto viene accodata, con una data specifica, in base al numero di giorni impostato nel criterio.

Un processo di gestione delle code separato elabora costantemente i messaggi, ridimensionandoli in base alle esigenze. Si supponga, ad esempio, di aver detaggato due manifesti, a distanza di 1 ora, in un Registro di sistema con un criterio di conservazione di 30 giorni. Due messaggi verrebbero accodati. Quindi, 30 giorni dopo, a circa 1 ora di distanza, i messaggi verrebbero recuperati dalla coda ed elaborati, presupponendo che il criterio fosse ancora attivo.

## <a name="set-a-retention-policy---cli"></a>Impostare un criterio di conservazione - CLI

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per impostare criteri di conservazione per i manifesti senza tag in un Registro di sistema.

### <a name="enable-a-retention-policy"></a>Abilitare un criterio di conservazioneEnable a retention policy

Per impostazione predefinita, nessun criterio di conservazione è impostato in un registro contenitori. Per impostare o aggiornare i criteri di conservazione, eseguire il comando [az acr config retention update][az-acr-config-retention-update] nell'interfaccia della riga di comando di Azure.To set or update a retention policy, run the az acr config retention update command in the Azure CLI. È possibile specificare un numero di giorni compreso tra 0 e 365 per mantenere i manifesti senza tag. Se non si specifica un numero di giorni, il comando imposta un valore predefinito di 7 giorni. Dopo il periodo di conservazione, tutti i manifesti senza tag nel Registro di sistema vengono eliminati automaticamente.

Nell'esempio seguente viene impostato un criterio di conservazione di 30 giorni per i manifesti senza tag nel Registro di sistema *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Nell'esempio seguente viene impostato un criterio per eliminare qualsiasi manifesto nel Registro di sistema non appena viene senza tag. Creare questo criterio impostando un periodo di conservazione di 0 giorni. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Convalidare un criterio di conservazione

Se si abilita il criterio precedente con un periodo di conservazione di 0 giorni, è possibile verificare rapidamente che i manifesti senza tag vengano eliminati:

1. Eseguire il `hello-world:latest` push di un'immagine di prova nel Registro di sistema o sostituire un'altra immagine di prova di propria scelta.
1. Annullare `hello-world:latest` il tag dell'immagine, ad esempio utilizzando il comando untag del [repository az acr.][az-acr-repository-untag] Il manifesto senza tag rimane nel Registro di sistema.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Entro pochi secondi, il manifesto senza tag viene eliminato. È possibile verificare l'eliminazione elencando i manifesti nel repository, ad esempio usando il comando [az acr repository show-manifests.][az-acr-repository-show-manifests] Se l'immagine di prova è l'unica nel repository, il repository stesso viene eliminato.

### <a name="disable-a-retention-policy"></a>Disabilitare un criterio di conservazioneDisable a retention policy

Per visualizzare i criteri di conservazione impostati in un Registro di sistema, eseguire il comando [az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Per disabilitare un criterio di conservazione in un Registro di sistema, eseguire il comando [az acr config retention update][az-acr-config-retention-update] e impostare: `--status disabled`

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Impostare un criterio di conservazione - portaleSet a retention policy - portal

È anche possibile impostare i criteri di conservazione di un Registro di sistema nel portale di Azure.You can also set a registry's retention policy in the [Azure portal](https://portal.azure.com). Nell'esempio seguente viene illustrato come utilizzare il portale per impostare un criterio di conservazione per i manifesti senza tag in un Registro di sistema.

### <a name="enable-a-retention-policy"></a>Abilitare un criterio di conservazioneEnable a retention policy

1. Passare al Registro di sistema del contenitore di Azure.Navigate to your Azure container registry. In **Criteri**selezionare **Conservazione** (anteprima).
1. In **Stato**selezionare **Abilitato**.
1. Selezionare un numero di giorni compreso tra 0 e 365 per mantenere i manifesti senza tag. Selezionare **Salva**.

![Abilitare criteri di conservazione nel portale di AzureEnable a retention policy in Azure portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Disabilitare un criterio di conservazioneDisable a retention policy

1. Passare al Registro di sistema del contenitore di Azure.Navigate to your Azure container registry. In **Criteri**selezionare **Conservazione** (anteprima).
1. In **Stato**selezionare **Disabilitato**. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle opzioni per eliminare immagini e archivi nel Registro di sistema dei contenitori di AzureLearn more about options to delete images and [repositories](container-registry-delete.md) in Azure Container Registry

* Informazioni su come [eliminare automaticamente](container-registry-auto-purge.md) le immagini e i manifesti selezionati da un Registro di sistema

* Ulteriori informazioni sulle opzioni per [bloccare immagini e manifesti](container-registry-image-lock.md) in un Registro di sistema

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
