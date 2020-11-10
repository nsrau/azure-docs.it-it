---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425082"
---
Una delle funzionalità principali del servizio di riconoscimento vocale è la possibilità di riconoscere la voce umana e tradurla in altre lingue. Questa Guida introduttiva illustra come usare l'SDK di riconoscimento vocale nelle app e nei prodotti per eseguire la traduzione vocale di alta qualità. Questa Guida introduttiva converte il riconoscimento vocale dal microfono in testo in un'altra lingua.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Imposta lingua di origine e di destinazione

Questo comando chiama l'interfaccia della riga di comando vocale per tradurre la voce dal microfono dall'italiano al francese.

```shell
 spx translate --microphone --source it-IT --target fr
```