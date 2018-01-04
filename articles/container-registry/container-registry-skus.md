---
title: SKU del Registro contenitori di Azure
description: Confrontare i diversi livelli di servizio disponibili nel Registro di sistema contenitore di Azure.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 15179fa3e3567f92a5eae69ba9a684addc3138dd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="azure-container-registry-skus"></a>SKU del Registro contenitori di Azure

Il Registro contenitori di Azure è disponibile su più livelli di servizio, noti come SKU. Queste SKU forniscono prezzi stimabile e diverse opzioni per l'allineamento per i modelli di utilizzo e capacità privato Docker del Registro di sistema in Azure.

| SKU | Gestito | DESCRIZIONE |
| --- | :-------: | ----------- |
| **Basic** | Sì | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro contenitori di Azure. I registri Basic dispongono delle stesse funzionalità programmatiche del livello Standard e Premium, ovvero l'integrazione dell'autenticazione, l'eliminazione di immagini e gli hook Web di Azure Active Directory, tuttavia ci sono limiti di dimensioni e uso. |
| **Standard** | Sì | I registri standard offrono le stesse funzionalità del livello base, con limiti di archiviazione e velocità effettiva di immagine aumentati. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | Sì | I registri premium offrono limiti più elevati sui vincoli quali l'archiviazione e le operazioni simultanee, l'abilitazione di scenari con volumi elevati. Oltre alle capacità di velocità effettiva superiore immagine, Premium aggiunge funzionalità come [-replica geografica] [ container-registry-geo-replication] per la gestione di un singolo registro di sistema in più aree, mantenere un registro di sistema di rete e di chiusura a ogni distribuzione. |
| Classico | No  | Lo SKU del registro classico ha abilitato la versione iniziale del servizio Registro contenitori di Azure in Azure. I registri classici sono supportati da un account di archiviazione creato da Azure nella sottoscrizione, che limita la capacità di Registro contenitori di Azure di offrire funzionalità di livello superiore, ad esempio velocità effettiva e replica geografica aumentate. A causa delle capacità limitate, si pensa in futuro di rimuovere lo SKU classico. |

Scelta di che uno SKU di livello superiore fornisce ulteriori scalabilità e prestazioni, tuttavia, tutti gli SKU gestiti forniscono le stesse funzionalità a livello di codice. Con più livelli di servizio, è possibile iniziare con Basic, quindi convertire man mano che aumenta l'utilizzo del Registro di sistema Standard e Premium.

> [!NOTE]
> Poiché lo SKU del registro classico verrà eliminato, è consigliabile usare i livelli di base, standard o premium per tutti i nuovi registri. Per informazioni sulla conversione del Registro di sistema classico esistente, vedere [l'aggiornamento di un registro di sistema classico][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Gestite e non gestito

Basic, Standard e Premium SKU sono noti come *gestiti* i registri e i registri classica come *non gestito*. La differenza principale tra i due è una modalità di archiviazione delle immagini contenitore.

### <a name="managed-basic-standard-premium"></a>Gestito (Basic, Standard e Premium)

Registri di sistema gestiti viene eseguiti da un account di archiviazione di Azure gestito da Azure. Vale a dire l'account di archiviazione che archivia le immagini non vengono visualizzati nella sottoscrizione di Azure. Esistono diversi vantaggi derivanti dall'uso di uno del Registro di sistema gestito SKU, illustrate informazioni dettagliate sulla [l'aggiornamento di un registro di sistema classico][container-registry-upgrade]. Questo articolo illustra gli SKU gestito del Registro di sistema e le relative funzionalità.

### <a name="unmanaged-classic"></a>Non gestito (versione classica)

I registri classici sono "non gestiti" nel senso che l'account di archiviazione che esegue il backup del Registro di sistema una classica si trova all'interno di *il* sottoscrizione di Azure. Di conseguenza, si è responsabili della gestione dell'account di archiviazione in cui sono archiviate le immagini contenitore. Con i registri non gestiti, è possibile passare da SKU in base alle esigenze (diverso da [aggiornamento] [ container-registry-upgrade] un registro di sistema gestito), e diverse funzionalità di registri gestiti non sono disponibili (ad esempio, l'eliminazione di immagini contenitore, [-replica geografica] [ container-registry-geo-replication] e [webhook][container-registry-webhook]).

Per ulteriori informazioni sull'aggiornamento di un registro di sistema classica a uno dei SKU gestiti, vedere [l'aggiornamento di un registro di sistema classico][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Matrice di funzionalità SKU

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Base, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Cambiare SKU

È possibile modificare lo SKU del Registro di sistema con l'interfaccia CLI di Azure o nel portale di Azure. È possibile spostare liberamente tra SKU gestito, purché lo SKU si passa a con la capacità di archiviazione massimo richiesto. Se si passa a uno dei SKU gestito dalla versione classica, è possibile riportare classica, è una conversione unidirezionale.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per spostarsi tra gli SKU di CLI di Azure, utilizzare il [aggiornamento acr az] [ az-acr-update] comando. Ad esempio, per passare alla versione Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portale di Azure

Nel Registro di sistema contenitore **Panoramica** nel portale di Azure, selezionare **aggiornamento**, quindi selezionare un nuovo **SKU** dall'elenco a discesa SKU.

![Aggiornare lo SKU del registro contenitori nel portale di Azure][update-registry-sku]

Se si dispone di un registro di sistema classica, è possibile selezionare uno SKU gestito all'interno del portale di Azure. In alternativa, è innanzitutto necessario [aggiornamento] [ container-registry-upgrade] un registro di sistema gestito (vedere [modifica dalla versione classica](#changing-from-classic)).

## <a name="changing-from-classic"></a>Cambiare il registro classico

Ci sono altre considerazioni per prendere in considerazione durante la migrazione di un registro di sistema classico non gestito a uno dei gestito Basic, Standard o Premium SKU. Se il Registro di sistema classica contiene un numero elevato di immagini e molti gigabyte di spazio, il processo di migrazione può richiedere alcuni minuti. Inoltre, `docker push` operazioni sono disabilitate fino al completamento della migrazione.

Per informazioni dettagliate sull'aggiornamento del Registro di sistema classica per uno dei SKU gestiti, vedere [l'aggiornamento di un registro di sistema contenitore classica][container-registry-upgrade].

## <a name="pricing"></a>Prezzi

Per informazioni su ciascuno dei SKU Azure contenitore del Registro di sistema sui prezzi, vedere [prezzi contenitore del Registro di sistema][container-registry-pricing].

## <a name="next-steps"></a>Passaggi successivi

**Guida del Registro contenitori di Azure**

Visitare il [ACR Roadmap] [ acr-roadmap] su GitHub per trovare informazioni sulle funzionalità future del servizio.

**UserVoice del Registro contenitori di Azure**

Inviare e votare nuovi suggerimenti sulle funzionalità in [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-webhook]: container-registry-webhook.md
