---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123832"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problema: Immagine personalizzata; errori di provisioning
Gli errori di provisioning si verificano se si carica o acquisisce un'immagine di VM generalizzata come un'immagine di VM specializzata o viceversa. Il primo causerà un errore di timeout del provisioning e il secondo un errore di provisioning. Per distribuire l'immagine personalizzata senza errori, è necessario assicurarsi che il tipo dell'immagine non venga modificato durante il processo di acquisizione.

La tabella seguente include l'elenco delle possibili combinazioni di immagini generalizzate e specializzate, il tipo di errore visualizzato e cosa è necessario fare per correggere gli errori.

<!--Update_Description: wording update, update link-->