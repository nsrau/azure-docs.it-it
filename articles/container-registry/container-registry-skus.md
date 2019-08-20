---
title: SKU di Registro Azure Container
description: Confrontare i diversi livelli di servizio disponibili in Registro Azure Container.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68311898"
---
# <a name="azure-container-registry-skus"></a>SKU di Registro Azure Container

Registro Azure Container è disponibile su più livelli di servizio, noti come SKU. Gli SKU offrono prezzi prevedibili e diverse opzioni per allinearsi ai modelli di capacità e utilizzo del registro docker privato in Azure.

| SKU | Gestita | Descrizione |
| --- | :-------: | ----------- |
| **Basic** | Yes | Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro Azure Container. I registri di base hanno le stesse funzionalità programmatiche di standard e Premium (ad esempio Azure Active Directory [l'integrazione dell'autenticazione](container-registry-authentication.md#individual-login-with-azure-ad)[eliminazione di immagini][container-registry-delete]e i [webhook][container-registry-webhook]). Tuttavia, le risorse di archiviazione incluse e la velocità effettiva di immagine sono più appropriate per scenari di utilizzo inferiore. |
| **Standard** | Sì | I registri standard offrono le stesse funzionalità del livello base, con risorse di archiviazione incluse e velocità effettiva di immagine maggiori. I registri Standard devono soddisfare le esigenze della maggior parte degli scenari di produzione. |
| **Premium** | Sì | I registri Premium offrono il massimo livello di risorse di archiviazione incluse e operazioni simultanee, consentendo scenari con volumi elevati. Oltre a una velocità effettiva di immagine superiore, il livello Premium aggiunge funzionalità, tra cui la [replica geografica][container-registry-geo-replication] per la gestione di un singolo registro in più aree, l'attendibilità del [contenuto](container-registry-content-trust.md) per la firma di tag immagine e i [firewall e le reti virtuali (anteprima)](container-registry-vnet.md) a limitare l'accesso al registro di sistema. |
|  Classica (*non disponibile dopo il 2019 aprile*) | No | Lo SKU ha abilitato la versione iniziale del servizio Registro Azure Container in Azure. I registri classici sono supportati da un account di archiviazione creato da Azure nella sottoscrizione, che limita la capacità di Registro Azure Container di offrire funzionalità di livello superiore, ad esempio velocità effettiva e replica geografica aumentate. |

> [!IMPORTANT]
> Lo SKU del registro di sistemaclassico verrà deprecato e non sarà disponibile dopo il **2019 aprile**. È consigliabile usare Basic, standard o Premium per tutti i nuovi registri. Tutti i registri classici esistenti devono essere aggiornati prima del 2019 aprile. Per informazioni sull'aggiornamento, vedere [aggiornamento di un registro classico][container-registry-upgrade].

Gli SKU Basic, standard e Premium, chiamati collettivamente *registri gestiti*, forniscono le stesse funzionalità a livello di codice. Anche tutti traggono vantaggio dall' [archiviazione di immagini][container-registry-storage] gestita interamente da Azure. La scelta di uno SKU di livello superiore offre maggiori prestazioni e una migliore scalabilità. Con più livelli di servizio, è possibile iniziare con il livello Basic, quindi passare a Standard e Premium con l'aumento dell'utilizzo del registro.

## <a name="sku-feature-matrix"></a>Matrice di funzionalità SKU

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Cambiare SKU

È possibile cambiare lo SKU del registro con l'interfaccia della riga di comando di Azure o nel portale di Azure. È possibile alternare liberamente gli SKU gestiti, purché lo SKU a cui si passa abbia la capacità di archiviazione massima richiesta. Quando si passa a uno degli SKU gestiti dal modello classico, non è possibile tornare alla versione classica, perché si tratta di una conversione unidirezionale.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per spostarsi tra gli SKU nell'interfaccia della riga di comando di Azure, usare il comando [AZ ACR Update][az-acr-update] . Ad esempio, per passare al registro Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portale di Azure

Nella **Panoramica** del registro del contenitore nel portale di Azure selezionare **Aggiorna**, quindi scegliere un nuovo **SKU** dall'elenco SKU a discesa.

![Aggiornare lo SKU del registro contenitori nel portale di Azure][update-registry-sku]

Se si utilizza un registro classico, non è possibile selezionare uno SKU gestito all'interno del portale di Azure. Al contrario, è necessario [eseguire][container-registry-upgrade] prima l'aggiornamento a un registro gestito.

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
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
