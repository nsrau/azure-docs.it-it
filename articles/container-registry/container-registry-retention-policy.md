---
title: Criteri di conservazione dei manifesti senza tag
description: Informazioni su come abilitare un criterio di conservazione in Registro Azure Container per l'eliminazione automatica dei manifesti senza tag dopo un periodo definito.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683451"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Impostare un criterio di conservazione per i manifesti senza tag

Registro Azure Container offre la possibilità di impostare un *criterio di conservazione* per i manifesti di immagini archiviati a cui non sono associati tag (*manifesti senza tag*). Quando si abilita un criterio di conservazione, i manifesti senza tag nel registro vengono eliminati automaticamente dopo un numero di giorni prestabilito. Questa funzionalità evita che il registro si riempia di artefatti non necessari e consente di risparmiare sui costi di archiviazione. Se l'attributo `delete-enabled` di un manifesto senza tag è impostato su `false`, il manifesto non può essere eliminato e i criteri di conservazione non sono applicabili.

Per eseguire gli esempi di comandi di questo articolo è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si preferisce l'interfaccia locale, è necessario usare la versione 2.0.74 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

Un criterio di conservazione è una funzionalità dei registri contenitori **Premium**. Per informazioni sui livelli di servizio del registro contenitori, vedere [Livelli di servizio di Registro Azure Container](container-registry-skus.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Impostare un criterio di conservazione con cautela: l'eliminazione dei dati dell'immagine è IRREVERSIBILE. Se si hanno sistemi che eseguono il pull delle immagini usando l'hash del manifesto e non i nomi delle immagini, non impostare un criterio di conservazione per le immagini senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull con il manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Per i manifesti senza tag si può solo impostare un criterio di conservazione.
* I criteri di conservazione attualmente si applicano solo ai manifesti i cui tag vengono rimossi *dopo* l'abilitazione dei criteri. I manifesti senza tag esistenti nel registro non sono soggetti ai criteri. Per eliminare i manifesti senza tag esistenti, vedere gli esempi in [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Informazioni sui criteri di conservazione

Registro Azure Container fa riferimento al conteggio dei manifesti nel registro. Quando un manifesto è senza tag, verifica i criteri di conservazione. Se un criterio di conservazione è abilitato, un'operazione di eliminazione del manifesto viene accodata, con una data specifica, in base al numero di giorni impostati nel criterio.

Un processo di gestione delle code separato elabora costantemente i messaggi, ridimensionando in base alle esigenze. Si supponga, ad esempio, di rimuovere i tag da due manifesti, a un'ora di distanza, in un registro con un criterio di conservazione di 30 giorni. Vengono accodati due messaggi. Quindi, 30 giorni dopo, a circa un'ora di distanza, i messaggi vengono recuperati dalla coda ed elaborati, posto che i criteri siano ancora attivi.

## <a name="set-a-retention-policy---cli"></a>Impostare un criterio di conservazione - interfaccia della riga di comando

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per impostare un criterio di conservazione per i manifesti senza tag in un registro.

### <a name="enable-a-retention-policy"></a>Abilitare un criterio di conservazione

Per impostazione predefinita, non viene impostato alcun criterio di conservazione in un registro contenitori. Per impostare o aggiornare i criteri di conservazione, eseguire il comando [az acr config retention update][az-acr-config-retention-update] nell'interfaccia della riga di comando di Azure. È possibile specificare un numero di giorni tra 0 e 365 per conservare i manifesti senza tag. Se non si specifica un numero di giorni, il comando imposta un valore predefinito di 7 giorni. Al termine del periodo di conservazione, tutti i manifesti senza tag nel registro vengono automaticamente eliminati.

Nell'esempio seguente viene impostato un criterio di conservazione di 30 giorni per i manifesti senza tag nel registro *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Nell'esempio seguente viene impostato un criterio per eliminare i manifesti nel registro non appena vengono rimossi i tag. Per creare questo criterio, impostare un periodo di conservazione di 0 giorni. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Convalidare un criterio di conservazione

Se si abilita il criterio precedente con un periodo di conservazione di 0 giorni, è possibile verificare rapidamente se i manifesti senza tag vengono eliminati:

1. Eseguire il push di un'immagine di test `hello-world:latest` nel registro o sostituirla con un'altra immagine di test di propria scelta.
1. Rimuovere il tag dall'immagine `hello-world:latest`, ad esempio, usando il comando [az acr repository untag][az-acr-repository-untag]. Il manifesto senza tag rimane nel registro.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Entro pochi secondi il manifesto senza tag viene eliminato. È possibile verificare l'eliminazione riportando i manifesti nel repository, ad esempio usando il comando [az acr repository show-manifests][az-acr-repository-show-manifests]. Se l'immagine di test è l'unica nel repository, il repository stesso viene eliminato.

### <a name="disable-a-retention-policy"></a>Disabilitare un criterio di conservazione

Per visualizzare i criteri di conservazione impostati in un registro, eseguire il comando [az acr config retention show][az-acr-config-retention-show]:

```azurecli
az acr config retention show --registry myregistry
```

Per disabilitare i criteri di conservazione in un registro, eseguire il comando [az acr config retention update][az-acr-config-retention-update] e impostare `--status disabled`:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Impostare un criterio di conservazione - portale

È possibile impostare i criteri di conservazione del registro anche nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come usare il portale per impostare un criterio di conservazione per i manifesti senza tag in un registro.

### <a name="enable-a-retention-policy"></a>Abilitare un criterio di conservazione

1. Passare al registro contenitori di Azure. In **Criteri** selezionare **Conservazione** (anteprima).
1. In **Stato** selezionare **Abilitato**.
1. Selezionare un numero di giorni tra 0 e 365 per conservare i manifesti senza tag. Selezionare **Salva**.

![Abilitare un criterio di conservazione nel portale di Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Disabilitare un criterio di conservazione

1. Passare al Registro Azure Container. In **Criteri** selezionare **Conservazione** (anteprima).
1. In **Stato** selezionare **Disabilitato**. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle opzioni che consentono di [eliminare immagini e repository](container-registry-delete.md) in Registro Azure Container

* Informazioni su come [rimuovere definitivamente in modo automatico](container-registry-auto-purge.md) le immagini e i manifesti selezionati da un registro

* Altre informazioni sulle opzioni per [bloccare le immagini e i manifesti](container-registry-image-lock.md) in un registro

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
