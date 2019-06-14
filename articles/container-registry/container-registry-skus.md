---
title: SKU di Registro Azure Container
description: Confrontare i diversi livelli di servizio disponibili in Registro Azure Container.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: f36b206ff015511dea7369617febe9220282bbe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069049"
---
# <a name="azure-container-registry-skus"></a>SKU di Registro Azure Container

Registro Azure Container è disponibile su più livelli di servizio, noti come SKU. Gli SKU offrono prezzi prevedibili e diverse opzioni per allinearsi ai modelli di capacità e utilizzo del registro docker privato in Azure.

| SKU | Gestito | Descrizione |
| --- | :-------: | ----------- |
| **Basic** | Yes | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro Azure Container. I registri Basic dispongono delle stesse funzionalità a livello di codice Standard e Premium (ad esempio Azure Active Directory [l'integrazione dell'autenticazione](container-registry-authentication.md#individual-login-with-azure-ad), [l'eliminazione di immagine][container-registry-delete], e [webhooks][container-registry-webhook]). Tuttavia, le risorse di archiviazione incluse e la velocità effettiva di immagine sono più appropriate per scenari di utilizzo inferiore. |
| **Standard** | Yes | I registri standard offrono le stesse funzionalità del livello base, con risorse di archiviazione incluse e velocità effettiva di immagine maggiori. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | Yes | I registri Premium offrono il massimo livello di risorse di archiviazione incluse e operazioni simultanee, consentendo scenari con volumi elevati. Oltre a una velocità effettiva di immagine, il livello Premium aggiunge funzionalità, tra cui [replica geografica] [ container-registry-geo-replication] per la gestione di un singolo registro in più aree, [attendibilità del contenuto](container-registry-content-trust.md) per la firma di tag di immagine, e [firewall e reti virtuali (anteprima)](container-registry-vnet.md) per limitare l'accesso al Registro di sistema. |
|  Classico (*non è disponibile dopo aprile 2019*) | No | Lo SKU ha abilitato la versione iniziale del servizio Registro Azure Container in Azure. I registri classici sono supportati da un account di archiviazione creato da Azure nella sottoscrizione, che limita la capacità di Registro Azure Container di offrire funzionalità di livello superiore, ad esempio velocità effettiva e replica geografica aumentate. |

> [!IMPORTANT]
> Il registro classico SKU è in corso **deprecate**e non sarà disponibile dopo **aprile 2019**. È consigliabile usare Basic, Standard o Premium per tutti i nuovi registri. Tutti i registri classici esistenti devono essere aggiornati prima di aprile 2019. Per informazioni sull'aggiornamento, vedere [aggiornare un registro classico][container-registry-upgrade].

Il Basic, Standard e Premium SKU (collettivamente chiamati *registri gestiti*) offrono tutti le stesse funzionalità a livello di codice. Sono anche vantaggi offerti dalle [archiviazione di immagini] [ container-registry-storage] gestite completamente da Azure. La scelta di uno SKU di livello superiore offre maggiori prestazioni e una migliore scalabilità. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

## <a name="sku-feature-matrix"></a>Matrice di funzionalità SKU

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Cambiare SKU

È possibile cambiare lo SKU del registro con l'interfaccia della riga di comando di Azure o nel portale di Azure. È possibile alternare liberamente gli SKU gestiti, purché lo SKU a cui si passa abbia la capacità di archiviazione massima richiesta. Quando si passa a uno degli SKU gestiti dalla distribuzione classica, è possibile tornare al registro classico: si tratta di una conversione unidirezionale.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per alternare gli SKU nell'interfaccia della riga di comando di Azure, utilizzare il comando [az acr update][az-acr-update]. Ad esempio, per passare al registro Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portale di Azure

Nella **Panoramica** del registro del contenitore nel portale di Azure selezionare **Aggiorna**, quindi scegliere un nuovo **SKU** dall'elenco SKU a discesa.

![Aggiornare lo SKU del registro contenitori nel portale di Azure][update-registry-sku]

Se si utilizza un registro classico, non è possibile selezionare uno SKU gestito all'interno del portale di Azure. In alternativa, è necessario innanzitutto [aggiornare] [ container-registry-upgrade] a un registro gestito.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi di ogni SKU di Registro Azure Container, vedere [Prezzi del Registro contenitori][container-registry-pricing].

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
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
