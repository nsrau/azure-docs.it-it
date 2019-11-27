---
title: Livelli di servizio e SKU
description: Informazioni sulle funzionalità e i limiti dei livelli di servizio Basic, standard e Premium (SKU) di Azure Container Registry.
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

| Sku | DESCRIZIONE |
| --- | ----------- |
| **Basic** | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro Azure Container. I registri di base hanno le stesse funzionalità programmatiche di standard e Premium (ad esempio Azure Active Directory [l'integrazione dell'autenticazione, l'](container-registry-authentication.md#individual-login-with-azure-ad) [eliminazione di immagini][container-registry-delete]e i [webhook][container-registry-webhook]). Tuttavia, le risorse di archiviazione incluse e la velocità effettiva di immagine sono più appropriate per scenari di utilizzo inferiore. |
| **Standard** | I registri standard offrono le stesse funzionalità del livello base, con risorse di archiviazione incluse e velocità effettiva di immagine maggiori. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | I registri Premium offrono il massimo livello di risorse di archiviazione incluse e operazioni simultanee, consentendo scenari con volumi elevati. Oltre a una velocità effettiva di immagine superiore, il livello Premium aggiunge funzionalità come la [replica geografica][container-registry-geo-replication] per la gestione di un singolo registro in più aree, l' [attendibilità del contenuto](container-registry-content-trust.md) per la firma di tag di immagine, i [firewall e le reti virtuali (anteprima)](container-registry-vnet.md) a limitare l'accesso al registro di sistema. |

Gli SKU Basic, standard e Premium forniscono tutte le stesse funzionalità a livello di codice. Anche tutti traggono vantaggio dall' [archiviazione di immagini][container-registry-storage] gestita interamente da Azure. La scelta di uno SKU di livello superiore offre maggiori prestazioni e una migliore scalabilità. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

## <a name="sku-features-and-limits"></a>Funzionalità e limiti dello SKU

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Cambiare SKU

È possibile cambiare lo SKU del registro con l'interfaccia della riga di comando di Azure o nel portale di Azure. È possibile spostarsi liberamente tra gli SKU purché lo SKU a cui si sta passando ha la capacità di archiviazione massima richiesta. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per spostarsi tra gli SKU nell'interfaccia della riga di comando di Azure, usare il comando [AZ ACR Update][az-acr-update] . Ad esempio, per passare al registro Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portale di Azure

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
