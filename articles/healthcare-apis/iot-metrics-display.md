---
title: Visualizzare e configurare le metriche del connettore Azure IoT per FHIR (anteprima)
description: Questo articolo illustra come visualizzare e configurare il connettore Azure per la metrica FHIR (anteprima).
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 79a7b18986f4bed503cd5493a7d74325a13fe535
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630516"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Visualizzare e configurare le metriche del connettore Azure IoT per FHIR (anteprima) 

Questo articolo illustra come visualizzare e configurare il connettore Azure per le risorse di interoperabilità sanitaria veloce (FHIR&#174;) *.

> [!TIP]
> Per informazioni su come configurare l'esportazione dei dati di metrica, seguire le istruzioni [riportate in esportare il connettore Azure per le metriche di FHIR (anteprima) tramite le impostazioni di diagnostica](./iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Visualizzare le metriche per il connettore Azure per FHIR (anteprima)

1. Accedere al portale di Azure, quindi selezionare l'API di Azure per il servizio FHIR. 

2. Nel riquadro sinistro selezionare **metriche**. 

3. Selezionare la scheda **connettore** Internet.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Screenshot del riquadro ' Internet del connettore ', con grafici a linee che visualizzano il numero di messaggi in ingresso e normalizzati." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selezionare un connettore Internet per visualizzare le relative metriche. Ad esempio, ci sono quattro connettori Internet ( *connettore 1* , *connettore 2* e così via) associati a questa API di Azure per il servizio FHIR.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Screenshot del riquadro ' il connettore ', che visualizza le schede del connettore Internet 1, 2, 3 e 4." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Selezionare il periodo di tempo (ad esempio, **1 ora** , **24 ore** , **7 giorni** o **personalizzato** ) delle metriche del connettore per le cose che si desidera visualizzare. Selezionando la scheda **personalizzata** , è possibile creare combinazioni di data/ora specifiche per la visualizzazione delle metriche del connettore Internet.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Screenshot del riquadro ' sacco connettore ', che visualizza un grafico a linee del periodo di tempo di 1 ora per ' Connector 1'." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Tipi di metrica per il connettore Azure Internet per FHIR (anteprima) 

> [!TIP]
> Per informazioni sul flusso di dati nel connettore Azure per FHIR, vedere la guida alla risoluzione dei problemi del connettore Azure per [FHIR (anteprima)](./iot-data-flow.md) e il [connettore Azure Internet per FHIR (anteprima)](./iot-troubleshoot-guide.md) per ulteriori informazioni su messaggi di errore e correzioni.

Le metriche del connettore Internet delle cose che è possibile visualizzare sono elencate nella tabella seguente:

|Tipo di metrica|Scopo della metrica| 
|-----------|--------------|
|Numero di messaggi in ingresso|Visualizza il numero di messaggi in ingresso non elaborati ricevuti, ad esempio gli eventi del dispositivo.|
|Numero di messaggi normalizzati|Visualizza il numero di messaggi normalizzati.|
|Numero di gruppi di messaggi|Consente di visualizzare il numero di gruppi con messaggi aggregati nell'intervallo di tempo designato.|
|Latenza media della fase normalizzata|Visualizza la latenza media della fase normalizzata. La fase normalizzata esegue la normalizzazione su messaggi in ingresso non elaborati.|
|Latenza media delle fasi del gruppo|Visualizza la latenza media della fase del gruppo. La fase di raggruppamento esegue il buffering, l'aggregazione e il raggruppamento su messaggi normalizzati.| 
|Numero totale errori|Visualizza il numero totale di errori.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Concentrarsi e configurare il connettore Azure per la metrica FHIR (anteprima)

In questo esempio, concentriamoci sul **numero di** metriche dei messaggi in ingresso.

1. Selezionare un punto nel tempo in cui si desidera concentrarsi.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Screenshot del riquadro della metrica &quot;numero di messaggi in ingresso&quot;, evidenziando un singolo punto nel tempo sul grafico a linee." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Nel riquadro **numero di messaggi in ingresso** è possibile personalizzare ulteriormente la metrica selezionando **Aggiungi metrica** , **Aggiungi filtro** o **applica suddivisione**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Screenshot del riquadro metrica ' numero di messaggi in ingresso ', che evidenzia i pulsanti ' Aggiungi metrica,' Aggiungi filtro ' è applica suddivisione '." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusione 
L'accesso alle metriche del piano dati è essenziale per il monitoraggio e la risoluzione dei problemi. Il connettore Azure per FHIR ti aiuta a eseguire queste operazioni attraverso le metriche. 

## <a name="next-steps"></a>Passaggi successivi

Risposte alle domande frequenti sul connettore Azure per FHIR.

>[!div class="nextstepaction"]
>[Domande frequenti sul connettore Azure Internet per FHIR](fhir-faq.md)

* Nel portale di Azure, il connettore Azure Internet per FHIR viene indicato come connettore Internet (anteprima). FHIR è un marchio registrato di HL7 e viene usato con l'autorizzazione di HL7. 
