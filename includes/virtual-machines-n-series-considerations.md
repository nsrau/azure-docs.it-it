---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1de7221f100077e07a2211bdb94e0198b35cb77c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170395"
---
## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

* Per la disponibilità delle VM serie N, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* Le VM serie N possono essere distribuite solo nel modello di distribuzione Resource Manager.

* Le macchine virtuali serie N differiscono nel tipo di Archiviazione di Azure supportato per i dischi. Le VM serie NC e NV supportano solo dischi della macchina virtuale con archiviazione su disco di Azure di tipo Standard (HDD). Le macchine virtuali NCv2, NCv3, ND, NDv2 e NVv2 supportano solo dischi di macchine virtuali con archiviazione su disco Premium (unità SSD).

* Per distribuire numerose VM serie N, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

* Può essere necessario aumentare la quota di core (per area) nella sottoscrizione di Azure e la quota separata per i core NC, NCv2, NCv3, ND, NDv2, NV o NVv2. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../articles/azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.




