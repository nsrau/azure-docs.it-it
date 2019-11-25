---
title: Service tiers and SKUs
description: Learn about the features and limits in the Basic, Standard, and Premium service tiers (SKUs) of Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456279"
---
# <a name="azure-container-registry-skus"></a>SKU di Registro Azure Container

Registro Azure Container è disponibile su più livelli di servizio, noti come SKU. Gli SKU offrono prezzi prevedibili e diverse opzioni per allinearsi ai modelli di capacità e utilizzo del registro docker privato in Azure.

| SKU | Description |
| --- | ----------- |
| **Basic** | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro Azure Container. Basic registries have the same programmatic capabilities as Standard and Premium (such as Azure Active Directory [authentication integration](container-registry-authentication.md#individual-login-with-azure-ad), [image deletion][container-registry-delete], and [webhooks][container-registry-webhook]). Tuttavia, le risorse di archiviazione incluse e la velocità effettiva di immagine sono più appropriate per scenari di utilizzo inferiore. |
| **Standard** | I registri standard offrono le stesse funzionalità del livello base, con risorse di archiviazione incluse e velocità effettiva di immagine maggiori. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | I registri Premium offrono il massimo livello di risorse di archiviazione incluse e operazioni simultanee, consentendo scenari con volumi elevati. In addition to higher image throughput, Premium adds features such as [geo-replication][container-registry-geo-replication] for managing a single registry across multiple regions, [content trust](container-registry-content-trust.md) for image tag signing, [firewalls and virtual networks (preview)](container-registry-vnet.md) to restrict access to the registry. |

The Basic, Standard, and Premium SKUs all provide the same programmatic capabilities. They also all benefit from [image storage][container-registry-storage] managed entirely by Azure. La scelta di uno SKU di livello superiore offre maggiori prestazioni e una migliore scalabilità. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

## <a name="sku-features-and-limits"></a>SKU features and limits

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Cambiare SKU

È possibile cambiare lo SKU del registro con l'interfaccia della riga di comando di Azure o nel portale di Azure. You can move freely between SKUs as long as the SKU you're switching to has the required maximum storage capacity. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

To move between SKUs in the Azure CLI, use the [az acr update][az-acr-update] command. Ad esempio, per passare al registro Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portale di Azure

Nella **Panoramica** del registro del contenitore nel portale di Azure selezionare **Aggiorna**, quindi scegliere un nuovo **SKU** dall'elenco SKU a discesa.

![Aggiornare lo SKU del registro contenitori nel portale di Azure][update-registry-sku]

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi di ogni SKU di Registro Azure Container, vedere [Prezzi di Registro Container][container-registry-pricing].

Per i dettagli sui prezzi per i trasferimenti dei dati, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passaggi successivi

**Guida di Registro Azure Container**

Visitare la [Guida di Registro Azure Container][acr-roadmap] su GitHub per maggiori informazioni sulle funzionalità future del servizio.

**UserVoice di Registro Azure Container**

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
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
