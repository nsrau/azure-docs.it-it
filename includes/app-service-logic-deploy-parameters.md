---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167424"
---
Con Azure Resource Manager è possibile definire parametri per i valori da usare durante la distribuzione del modello. Il modello include una sezione `parameters` che contiene tutti i valori dei parametri. Ogni valore di parametro viene usato dal modello per definire le risorse da distribuire.

> [!NOTE]
> Non definire i parametri per i valori che rimangono invariati. Definire parametri solo per i valori che variano in base al progetto distribuito o all'ambiente in cui viene eseguita la distribuzione.

Durante la definizione dei parametri:

* Per specificare i valori consentiti che possono essere immessi dall'utente durante la distribuzione, usare il campo **allowedValues**.

* Per assegnare valori predefiniti al parametro se durante la distribuzione non vengono specificati valori, usare il campo **defaultValue**. 
