---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776656"
---
Una delle funzionalità principali del servizio di riconoscimento vocale è la possibilità di riconoscere la voce umana e tradurla in altre lingue. Questa Guida introduttiva illustra come usare l'SDK di riconoscimento vocale nelle app e nei prodotti per eseguire la traduzione vocale di alta qualità. Questa Guida introduttiva converte il riconoscimento vocale dal microfono in testo in un'altra lingua.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../get-started.md).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Imposta lingua di origine e di destinazione

Questo comando chiama l'interfaccia della riga di comando vocale per tradurre la voce dal microfono dall'italiano al francese.

```shell
 spx translate --microphone --source it-IT --target fr
```
