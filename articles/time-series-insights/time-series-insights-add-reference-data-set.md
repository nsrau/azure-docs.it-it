---
title: Aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights | Microsoft Docs
description: In questa esercitazione si aggiunge un set di dati di riferimento all'ambiente Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 23444297b5231e6a026bcd9ce3ee9f943bf05867
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---

# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Creare un set di dati di riferimento per l'ambiente Time Series Insights usando il portale di Ibiza

Un set di dati di riferimento è una raccolta di elementi che vengono integrati con gli eventi provenienti dall'origine evento. Il motore in ingresso di Time Series Insights esegue il join di un evento dell'origine evento con un elemento del set di dati di riferimento. Questo evento aumentato sarà quindi disponibile per le query. Questo join è basato sulle chiavi definite nel set di dati di riferimento.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>Procedura per aggiungere un set di dati di riferimento all'ambiente

1. Accedere al [portale di Ibiza](https://portal.azure.com).
2. Fare clic su "Tutte le risorse" nel menu sul lato sinistro del portale di Ibiza.
3. Selezionare l'ambiente Time Series Insights.

    ![Creare il set di dati di riferimento di Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Selezionare "Set di dati di riferimento" e fare clic su "+ Aggiungi".

    ![Creare il set di dati di riferimento di Time Series Insights - Dettagli](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Specificare il nome del set di dati di riferimento.
6. Specificare il nome della chiave e il relativo tipo. Il nome e il tipo vengono usati per selezionare la proprietà corretta dall'evento nell'origine evento. Se ad esempio si specifica "DeviceId" come nome della chiave e "String" come tipo, il motore in ingresso di Time Series Insights cerca una proprietà denominata "DeviceId" di tipo "String" nell'evento in ingresso. È possibile specificare più di una chiave per creare il join con l'evento. La corrispondenza del nome della proprietà distingue tra maiuscole e minuscole.

     ![Creare il set di dati di riferimento di Time Series Insights - Dettagli](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Fare clic su "Crea".

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i dati di riferimento](time-series-insights-manage-reference-data-csharp.md) a livello di codice.
* Per le informazioni di riferimento complete per l'API, vedere il documento relativo all'[API dei dati di riferimento](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
