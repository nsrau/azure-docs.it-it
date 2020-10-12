---
title: Quote di servizio e disponibilità di aree
description: Quote, limiti e aree disponibili per il servizio Istanze di Azure Container.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384830"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Quote e limiti per istanze di Azure Container

Tutti i servizi di Azure prevedono determinati limiti predefiniti e quote per le risorse e le funzionalità. Questo articolo descrive le quote e i limiti predefiniti per le istanze di Azure Container.

La disponibilità delle risorse di calcolo, memoria e archiviazione per istanze di contenitore di Azure varia in base all'area e al sistema operativo. Per informazioni dettagliate, vedere [disponibilità delle risorse per istanze di contenitore di Azure](container-instances-region-availability.md).

Usare l'API [List Usage](/rest/api/container-instances/location/listusage) per esaminare l'utilizzo della quota corrente in un'area per una sottoscrizione.

## <a name="service-quotas-and-limits"></a>Quote e limiti del servizio

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Per richiedere un aumento di una o più risorse che lo consentono, inviare una [richiesta di supporto tecnico di Azure][azure-support] (selezionare "Quota" per **Tipo di problema**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
