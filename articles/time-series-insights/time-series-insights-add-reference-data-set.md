---
title: Come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights | Microsoft Docs
description: In questo articolo viene descritto come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights. Risulta utile associare i dati di riferimento ai dati di origine per aumentarne il valore.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Creare un set di dati di riferimento per l'ambiente Time Series Insights usando il portale di Azure

In questo articolo viene descritto come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights. Risulta utile associare i dati di riferimento ai dati di origine per aumentarne il valore.

Un set di dati di riferimento è una raccolta di elementi che vengono integrati con gli eventi provenienti dall'origine evento. Il motore in ingresso di Time Series Insights esegue il join di un evento dell'origine evento con un elemento del set di dati di riferimento. Questo evento aumentato sarà quindi disponibile per le query. Questo join è basato sulle chiavi definite nel set di dati di riferimento.

## <a name="add-a-reference-data-set"></a>Aggiungere un set di dati di riferimento

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights esistente. Fare clic su **Tutte le risorse** nel menu sul lato sinistro del portale di Azure. Selezionare l'ambiente Time Series Insights.

3. Sotto all'intestazione **Topologia dell'ambiente** selezionare **Set di dati di riferimento**.

    ![Creare il set di dati di riferimento di Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Selezionare **+ Aggiungi** per aggiungere un nuovo set di dati di riferimento.

5. Specificare un valore univoco nella casella **Nome del set di dati di riferimento**.

    ![Creare il set di dati di riferimento di Time Series Insights - Dettagli](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Specificare il **nome della chiave** nel campo vuoto e selezionare il relativo **tipo**. Il nome e il tipo vengono usati per selezionare la proprietà corretta dall'evento nell'origine evento da associare ai dati di riferimento. 

   Se ad esempio si specifica **DeviceId** come nome della chiave e **String** come tipo, il motore in ingresso di Time Series Insights cerca e associa una proprietà denominata **DeviceId** di tipo **String** nell'evento in ingresso. È possibile specificare più di una chiave per creare il join con l'evento. La corrispondenza del nome della chiave distingue tra maiuscole e minuscole.

7. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire i dati di riferimento](time-series-insights-manage-reference-data-csharp.md) a livello di codice.
* Per le informazioni di riferimento complete per l'API, vedere il documento relativo all'[API dei dati di riferimento](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
