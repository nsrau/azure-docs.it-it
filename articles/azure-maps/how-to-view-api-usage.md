---
title: Visualizzare le metriche di utilizzo dell'API Maps di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come visualizzare le metriche per le chiamate API di Microsoft Azure Maps nell'portale di Azure.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335160"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Visualizzare le metriche di utilizzo dell'API Mappe di Azure

Questo articolo illustra come visualizzare le metriche di utilizzo dell'API, per l'account Azure Maps, nella [portale di Azure](https://portal.azure.com). Le metriche vengono visualizzate in un pratico formato grafico in riferimento a un periodo di tempo personalizzabile.

## <a name="view-metric-snapshot"></a>Visualizzare lo snapshot delle metriche

È possibile visualizzare alcune metriche comuni nella pagina **Panoramica** dell'account di Mappe. Questa pagina attualmente include i valori *Richieste totali*, *Totale errori* e *Disponibilità* riferiti a un periodo di tempo selezionabile.

![Cenni preliminari sulle metriche di utilizzo di Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Passare alla sezione successiva, se è necessario personalizzare questi grafici per un'analisi specifica.

## <a name="view-detailed-metrics"></a>Visualizzare metriche dettagliate

1. Accedere alla sottoscrizione di Azure nel [portale](https://portal.azure.com).

2. Fare clic sulla voce di menu **Tutte le risorse** sul lato sinistro e passare all'*account di Mappe di Azure*.

3. Dopo aver aperto l'account di Mappe fare clic sul menu **Metriche** a sinistra.

4. Nel riquadro **metrica** scegliere una delle opzioni seguenti:

   1. **Disponibilità**, che illustra la disponibilità *Media* dell'API in un periodo di tempo.
   2. **Utilizzo**, che illustra il funzionamento del *Conteggio* per l'account.

      ![Riquadro metriche di utilizzo di mappe di Azure](media/how-to-view-api-usage/portal-metrics.png)

5. È possibile in seguito selezionare l'*Intervallo di tempo* facendo clic su **Last 24 hours (Automatic)** (Ultime 24 ore - Automatico). Per impostazione predefinita, l'intervallo di tempo è impostato su 24 ore. Dopo aver fatto clic, verranno visualizzati tutti gli intervalli di tempo selezionabili. È possibile selezionare *Granularità temporale* e scegliere di visualizzare l'ora come *locale* oppure *GMT* nello stesso elenco a discesa. Fare clic su **Apply**.

    ![Intervallo di tempo delle metriche di Maps di Azure](media/how-to-view-api-usage/time-range.png)

6. Una volta aggiunta la metrica, è possibile **aggiungere un filtro** dalle proprietà rilevanti per tale metrica. Quindi, selezionare il valore della proprietà che si desidera visualizzare nel grafico.

    ![Filtro metriche di utilizzo di mappe di Azure](media/how-to-view-api-usage/filter.png)

7. È anche possibile **applicare la suddivisione** per la metrica in base alla proprietà della metrica selezionata. Consente di suddividere il grafico in più grafici, per ogni valore della proprietà. Nell'immagine seguente il colore di ogni grafico corrisponde al valore della proprietà visualizzato nella parte inferiore del grafico.

    ![Suddivisione delle metriche di utilizzo di Azure Maps](media/how-to-view-api-usage/splitting.png)

8. È anche possibile osservare più metriche sullo stesso grafico, semplicemente facendo clic sul pulsante **Aggiungi metrica** nella parte superiore.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle API di Mappe di Azure per le quali si vuole tenere traccia dell'utilizzo:
> [!div class="nextstepaction"] 
> [Procedure per l'SDK Web di Azure Maps](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Procedure di Azure Maps Android SDK](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentazione dell'API REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps)
