---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

* Per la disponibilità delle VM serie N, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/en-us/regions/services/).

* Le VM serie N possono essere distribuite solo nel modello di distribuzione Resource Manager.

* Le macchine virtuali serie N differiscono nel tipo di Archiviazione di Azure supportato per i dischi. Le VM serie NC e NV supportano solo dischi della macchina virtuale con archiviazione su disco di Azure di tipo Standard (HDD). Le VM serie NCv2, ND e NCv3 supportano solo dischi di VM con archiviazione su disco di Azure di tipo Premium (SSD).

* Per distribuire numerose VM serie N, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

* Può essere necessario aumentare la quota di core (per area) nella sottoscrizione di Azure e la quota separata per i core NC, NCv2, NCv3, ND o NV. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../articles/azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.




