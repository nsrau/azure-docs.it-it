---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901817"
---
## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

* Per la disponibilità delle VM serie N, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* Le VM serie N possono essere distribuite solo nel modello di distribuzione Resource Manager.

* Le macchine virtuali serie N differiscono nel tipo di Archiviazione di Azure supportato per i dischi. Le VM serie NC e NV supportano solo dischi della macchina virtuale con archiviazione su disco di Azure di tipo Standard (HDD). Le macchine virtuali NCv2, NCv3, ND, NDv2 e NVv2 supportano solo dischi di macchine virtuali con archiviazione su disco Premium (unità SSD).

* Per distribuire numerose VM serie N, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

* Può essere necessario aumentare la quota di core (per area) nella sottoscrizione di Azure e la quota separata per i core NC, NCv2, NCv3, ND, NDv2, NV o NVv2. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.




