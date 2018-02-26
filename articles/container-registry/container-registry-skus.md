---
title: SKU del Registro contenitori di Azure
description: Confrontare i diversi livelli di servizio disponibili nel Registro contenitori di Azure.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 15179fa3e3567f92a5eae69ba9a684addc3138dd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="azure-container-registry-skus"></a>SKU del Registro contenitori di Azure

Il Registro contenitori di Azure è disponibile su più livelli di servizio, noti come SKU. Gli SKU offrono prezzi prevedibili e diverse opzioni per allinearsi ai modelli di capacità e utilizzo del registro docker privato in Azure.

| SKU | Gestito | DESCRIZIONE |
| --- | :-------: | ----------- |
| **Basic** | Sì | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro contenitori di Azure. I registri Basic dispongono delle stesse funzionalità programmatiche del livello Standard e Premium, ovvero l'integrazione dell'autenticazione, l'eliminazione di immagini e gli hook Web di Azure Active Directory, tuttavia ci sono limiti di dimensioni e uso. |
| **Standard** | Sì | I registri standard offrono le stesse funzionalità del livello base, con limiti di archiviazione e velocità effettiva di immagine aumentati. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | Sì | I registri premium offrono limiti più elevati sui vincoli quali l'archiviazione e le operazioni simultanee, l'abilitazione di scenari con volumi elevati. Oltre alle maggiore velocità effettiva per le immagini, il livello Premium aggiunge funzionalità come la [replica geografica][container-registry-geo-replication] per la gestione di un solo registro in più aree, mantenendo un registro in una posizione di rete vicina a ogni distribuzione. |
| Classico | No  | Lo SKU del registro classico ha abilitato la versione iniziale del servizio Registro contenitori di Azure in Azure. I registri classici sono supportati da un account di archiviazione creato da Azure nella sottoscrizione, che limita la capacità di Registro contenitori di Azure di offrire funzionalità di livello superiore, ad esempio velocità effettiva e replica geografica aumentate. A causa delle capacità limitate, si pensa in futuro di rimuovere lo SKU classico. |

La scelta di un SKU di livello superiore offre scalabilità e prestazioni migliori, tuttavia, tutti gli SKU gestiti forniscono le stesse funzionalità a livello di codice. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

> [!NOTE]
> Poiché lo SKU del registro classico verrà eliminato, è consigliabile usare i livelli di base, standard o premium per tutti i nuovi registri. Per informazioni sulla conversione del registro classico esistente, vedere [Aggiornare un registro classico][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Gestiti e non gestiti

Gli SKU Basic, Standard e Premium sono noti collettivamente come registri *gestiti* mentre i registri classici come *non gestiti*. La differenza principale è la modalità di archiviazione delle immagini del contenitore.

### <a name="managed-basic-standard-premium"></a>Gestiti (Basic, Standard, Premium)

I registri gestiti sono supportati da un account Archiviazione di Azure gestito da Azure. L'account di archiviazione che archivia le immagini non è visualizzato nella sottoscrizione di Azure. L'uso di uno degli SKU di registri gestiti offre vari vantaggi, illustrati in dettaglio in [Aggiornare un registro classico][container-registry-upgrade]. Questo articolo illustra gli SKU di registri gestiti e le relative funzionalità.

### <a name="unmanaged-classic"></a>Non gestiti (classici)

I registri classici sono detti non gestiti in quanto l'account di archiviazione che li supporta si trova all'interno della *sottoscrizione* di Azure. Di conseguenza, l'utente è responsabile della gestione dell'account di archiviazione in cui sono archiviate le immagini del contenitore. I registri non gestiti non consentono di alternare gli SKU in base alle esigenze (a parte eseguire l'[aggiornamento][container-registry-upgrade] a un registro di sistema gestito) e non offrono diverse funzionalità dei registri gestiti (ad esempio l'eliminazione delle immagini del contenitore, la [replica geografica][container-registry-geo-replication] e i [webhook][container-registry-webhook]).

Per ulteriori informazioni sull'aggiornamento di un registro classico a uno SKU gestito, vedere [Aggiornamento di un registro classico][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Matrice di funzionalità SKU

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Base, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Cambiare SKU

È possibile cambiare lo SKU del registro con l'interfaccia della riga di comando di Azure o nel portale di Azure. È possibile alternare liberamente gli SKU gestiti, purché lo SKU a cui si passa abbia la capacità di archiviazione massima richiesta. Se si passa a uno degli SKU gestiti dalla versione classica, non è possibile tornare al registro classico: si tratta di una conversione unidirezionale.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per alternare gli SKU nell'interfaccia della riga di comando di Azure, utilizzare il comando [az acr update][az-acr-update]. Ad esempio, per passare al registro Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portale di Azure

Nella **Panoramica** del registro del contenitore nel portale di Azure selezionare **Aggiorna**, quindi scegliere un nuovo **SKU** dall'elenco SKU a discesa.

![Aggiornare lo SKU del registro contenitori nel portale di Azure][update-registry-sku]

Se si utilizza un registro classico, non è possibile selezionare uno SKU gestito all'interno del portale di Azure. È invece necessario prima eseguire l'[aggiornamento][container-registry-upgrade] a un registro gestito (vedere [Cambiare il registro classico](#changing-from-classic)).

## <a name="changing-from-classic"></a>Cambiare il registro classico

Occorre considerare altri aspetti nella migrazione da un registro classico non gestito a uno degli SKU gestiti Basic, Standard o Premium. Se il registro classico contiene un numero elevato di immagini e occupa molti gigabyte, il processo di migrazione può richiedere alcuni minuti. Inoltre, le operazioni `docker push` sono disabilitate fino al completamento della migrazione.

Per dettagli sull'aggiornamento del registro classico a uno SKU gestito, vedere [Aggiornamento di un registro del contenitore classico][container-registry-upgrade].

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi di ogni SKU del Registro contenitori di Azure, vedere [Prezzi del Registro contenitori][container-registry-pricing].

## <a name="next-steps"></a>Passaggi successivi

**Guida del Registro contenitori di Azure**

Visitare la [Guida del Registro contenitori di Azure][acr-roadmap] su GitHub per maggiori informazioni sulle funzionalità future del servizio.

**UserVoice del Registro contenitori di Azure**

Inviare e votare i nuovi suggerimenti sulle funzionalità in [UserVoice del Registro contenitori di Azure][container-registry-uservoice].

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
