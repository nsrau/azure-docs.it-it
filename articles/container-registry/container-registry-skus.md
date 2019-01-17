---
title: SKU del Registro contenitori di Azure
description: Confrontare i diversi livelli di servizio disponibili nel Registro contenitori di Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: danlep
ms.openlocfilehash: 3f058a68057d6b84cbbb2dfdb08ea8c2cb12b0b9
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322094"
---
# <a name="azure-container-registry-skus"></a>SKU del Registro contenitori di Azure

Registro Azure Container è disponibile su più livelli di servizio, noti come SKU. Gli SKU offrono prezzi prevedibili e diverse opzioni per allinearsi ai modelli di capacità e utilizzo del registro docker privato in Azure.

| SKU | Gestito | DESCRIZIONE |
| --- | :-------: | ----------- |
| **Basic** | Yes | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro contenitori di Azure. I registri Basic dispongono delle stesse funzionalità programmatiche del livello Standard e Premium, ovvero l'integrazione dell'autenticazione, l'eliminazione delle immagini e gli hook Web di Azure Active Directory. Tuttavia, le risorse di archiviazione incluse e la velocità effettiva di immagine sono più appropriate per scenari di utilizzo inferiore. |
| **Standard** | Yes | I registri standard offrono le stesse funzionalità del livello base, con risorse di archiviazione incluse e velocità effettiva di immagine maggiori. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | Yes | I registri Premium offrono il massimo livello di risorse di archiviazione incluse e operazioni simultanee, consentendo scenari con volumi elevati. Oltre alla maggiore velocità effettiva per le immagini, il livello Premium aggiunge funzionalità come la [replica geografica][container-registry-geo-replication] per la gestione di un solo registro in più aree, e l'[attendibilità dei contenuti (anteprima)](container-registry-content-trust.md) per la firma dei tag di immagine. |
| Classic<sup>1</sup> | No  | Lo SKU ha abilitato la versione iniziale del servizio Registro contenitori di Azure in Azure. I registri classici sono supportati da un account di archiviazione creato da Azure nella sottoscrizione, che limita la capacità di Registro Azure Container di offrire funzionalità di livello superiore, ad esempio velocità effettiva e replica geografica aumentate. |

<sup>1</sup> Il codice SKU classico sarà **deprecato** a **marzo 2019**. Usare Basic, Standard o Premium per tutti i nuovi registri contenitori.

La scelta di un SKU di livello superiore offre scalabilità e prestazioni migliori, tuttavia, tutti gli SKU gestiti forniscono le stesse funzionalità a livello di codice. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

## <a name="managed-vs-unmanaged"></a>Gestiti e non gestiti

Gli SKU Basic, Standard e Premium sono noti collettivamente come registri *gestiti* mentre i registri classici come *non gestiti*. La differenza principale è la modalità di archiviazione delle immagini del contenitore.

### <a name="managed-basic-standard-premium"></a>Gestiti (Basic, Standard, Premium)

I registri gestiti traggono vantaggio dall'archiviazione immagini gestita interamente da Azure. L'account di archiviazione che archivia le immagini non è visualizzato nella sottoscrizione di Azure. L'uso di uno degli SKU di registri gestiti offre vari vantaggi, illustrati in dettaglio in [Archiviazione immagini del contenitore nel Registro contenitori di Azure][container-registry-storage]. Questo articolo illustra gli SKU di registri gestiti e le relative funzionalità.

### <a name="unmanaged-classic"></a>Non gestiti (classici)

> [!IMPORTANT]
> Il valore SKU classico è deprecato e non sarà disponibile dopo il mese di marzo 2019. Usare Basic, Standard o Premium per tutti i nuovi registri.

I registri classici sono detti non gestiti in quanto l'account di archiviazione che li supporta si trova all'interno della *sottoscrizione* di Azure. Di conseguenza, l'utente è responsabile della gestione dell'account di archiviazione in cui sono archiviate le immagini del contenitore. I registri non gestiti non consentono di alternare gli SKU in base alle esigenze (a parte eseguire l'[aggiornamento][container-registry-upgrade] a un registro gestito) e non offrono diverse funzionalità dei registri gestiti, ad esempio l'eliminazione delle immagini del contenitore, la [replica geografica][container-registry-geo-replication] e i [webhook][container-registry-webhook].

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

Per i dettagli sui prezzi per i trasferimenti dei dati, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passaggi successivi

**Guida del Registro contenitori di Azure**

Visitare la [Guida di Registro Azure Container][acr-roadmap] su GitHub per maggiori informazioni sulle funzionalità future del servizio.

**UserVoice del Registro contenitori di Azure**

Inviare e votare i nuovi suggerimenti sulle funzionalità in [UserVoice di Registro Azure Container][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
