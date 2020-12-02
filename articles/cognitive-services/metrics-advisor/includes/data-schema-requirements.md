---
title: Requisiti dello schema dei dati
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231430"
---
Advisor metriche è un servizio che gestisce il rilevamento, la diagnostica e l'analisi delle anomalie nelle serie temporali. Supportato dall'intelligenza artificiale, usa i dati per eseguire il training del modello usato. Il servizio accetta tabelle di dati aggregati con le colonne seguenti:

* **Misura** (obbligatoria): una o più colonne contenenti valori numerici.
* **Timestamp** (facoltativa): zero o una colonna di tipo `DateTime` o `String`. Quando questa colonna non è impostata, il timestamp viene impostato come ora di inizio di ogni periodo di inserimento. Formattare il timestamp nel modo seguente: `yyyy-MM-ddTHH:mm:ssZ`. 
  * **Il timestamp dovrebbe corrispondere alla granularità della metrica. Ad esempio, una metrica giornaliera dovrebbe garantire l'ora, il minuto e il secondo sul timestamp con etichetta `00:00:00`** .
* **Dimensione** (facoltativa): le colonne possono essere di qualsiasi tipo di dati. Prestare attenzione quando si lavora con grandi volumi di colonne e valori, per evitare l'elaborazione di un numero eccessivo di dimensioni.

> [!Note]
> Per ogni metrica deve essere presente un solo timestamp per misura, corrispondente a una combinazione di dimensioni. Aggregare i dati prima dell'onboarding o usare la query per specificare i dati da inserire.