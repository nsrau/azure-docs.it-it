---
title: Livelli di servizio registro e funzionalità
description: Informazioni sulle funzionalità e sui limiti dei livelli di servizio Basic, Standard e Premium (SKU) di Registro Azure Container.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: e2a5ad52775e9000aa0beb0a926d809da1c5a0e0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048475"
---
# <a name="azure-container-registry-service-tiers"></a>Livelli di servizio di Registro Azure Container

Registro Azure Container è disponibile in più livelli di servizio, noti come SKU. Questi livelli offrono prezzi prevedibili e diverse opzioni per l'allineamento ai modelli di capacità e utilizzo del registro Docker privato in Azure.

| Livello | Descrizione |
| --- | ----------- |
| **Base** | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro Azure Container. I registri Basic offrono le stesse capacità di programmazione del livello Standard e Premium, ad esempio l'[integrazione dell'autenticazione](container-registry-authentication.md#individual-login-with-azure-ad), l'[eliminazione delle immagini][container-registry-delete] e i [webhook][container-registry-webhook] di Azure Active Directory. Tuttavia, le risorse di archiviazione incluse e la velocità effettiva di immagine sono più appropriate per scenari di utilizzo inferiore. |
| **Standard** | I registri standard offrono le stesse funzionalità del livello base, con risorse di archiviazione incluse e velocità effettiva di immagine maggiori. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | I registri Premium offrono il massimo livello di risorse di archiviazione incluse e operazioni simultanee, consentendo scenari con volumi elevati. Oltre alla maggiore velocità effettiva per le immagini, il livello Premium aggiunge funzionalità quali la [replica geografica][container-registry-geo-replication] per la gestione di un solo registro in più aree, l'[attendibilità dei contenuti](container-registry-content-trust.md) per la firma dei tag di immagine, [collegamenti privati con endpoint privati](container-registry-private-link.md) per limitare l'accesso al registro. |

I livelli Basic, Standard e Premium offrono tutti le stesse capacità di programmazione. Traggono inoltre vantaggio dall'[archiviazione immagini][container-registry-storage] gestita interamente da Azure. La scelta di un livello di servizio superiore offre maggiori prestazioni e una migliore scalabilità. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

## <a name="service-tier-features-and-limits"></a>Funzionalità e limiti del livello di servizio

La tabella seguente illustra in dettaglio le funzionalità e i limiti del registro di sistema dei livelli di servizio Basic, standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Modifica dei livelli

È possibile cambiare il livello di servizio del registro tramite l'interfaccia della riga di comando di Azure o nel portale di Azure. È possibile passare liberamente da un livello all'altro, purché il livello a cui si passa abbia la capacità di archiviazione massima richiesta. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per passare da un livello di servizio all'altro nell'interfaccia della riga di comando di Azure, usare il comando [az acr update][az-acr-update]. Ad esempio, per passare al registro Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portale di Azure

Nella **Panoramica** del registro del contenitore nel portale di Azure selezionare **Aggiorna**, quindi scegliere un nuovo **SKU** dall'elenco SKU a discesa.

![Aggiornare lo SKU del registro contenitori nel portale di Azure][update-registry-sku]

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi di ogni SKU di Registro Azure Container, vedere [Prezzi del Registro Container][container-registry-pricing].

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