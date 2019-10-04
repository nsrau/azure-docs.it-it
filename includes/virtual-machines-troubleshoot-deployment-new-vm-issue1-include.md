---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180145"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problema: Immagine personalizzata; errori di provisioning
Gli errori di provisioning si verificano se si carica o acquisisce un'immagine di VM generalizzata come un'immagine di VM specializzata o viceversa. Il primo causerà un errore di timeout del provisioning e il secondo un errore di provisioning. Per distribuire l'immagine personalizzata senza errori, è necessario assicurarsi che il tipo dell'immagine non venga modificato durante il processo di acquisizione.

La tabella seguente include l'elenco delle possibili combinazioni di immagini generalizzate e specializzate, il tipo di errore visualizzato e cosa è necessario fare per correggere gli errori.

