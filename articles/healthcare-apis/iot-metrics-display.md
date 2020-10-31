---
title: Visualizza e configura il connettore Azure per la metrica FHIR (anteprima)
description: Questo articolo illustra la visualizzazione e la configurazione del connettore Azure per le metriche di FHIR (anteprima)
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133625"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Visualizza e configura il connettore Azure per la metrica FHIR (anteprima) 

Questo articolo illustra come visualizzare e configurare il connettore Azure per la metrica FHIR *. 

> [!TIP]
> Per informazioni su come configurare l'esportazione dei dati di metrica, seguire le istruzioni [riportate nella pagina relativa all'esportazione del connettore Azure per le metriche FHIR (anteprima) tramite le impostazioni di diagnostica](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) .

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Visualizzare le metriche per il connettore Azure per FHIR (anteprima)
1. Per visualizzare le metriche per i connettori Internet, selezionare l'API di Azure per il servizio FHIR nel portale di Azure. 

2. Passa a **metriche** 

3. Selezionare la scheda **connettore** Internet.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selezionare un connettore Internet per visualizzare le relative metriche. ad esempio, sono presenti (4) connettori per l'Internet delle cose associate a questa API di Azure per il servizio FHIR.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> La scheda **personalizzata** consente di creare combinazioni di data/ora specifiche per la visualizzazione delle metriche del connettore Internet.

5. Selezionare il periodo di tempo per la visualizzazione delle metriche del connettore Internet (ad esempio: 1 ora, 24 ore, 7 giorni o personalizzato).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Tipi di metriche per il connettore Azure Internet per FHIR (anteprima) 
Di seguito sono riportate le metriche del connettore Internet.

|Tipo di metrica|Scopo della metrica| 
|-----------|--------------|
|Numero di messaggi in ingresso|Numero di messaggi in ingresso non elaborati ricevuti (ad esempio, gli eventi del dispositivo).|
|Numero di messaggi normalizzati|Numero di messaggi normalizzati.|
|Numero di gruppi di messaggi|Numero di gruppi con messaggi aggregati nell'intervallo di tempo designato.|
|Latenza media della fase normalizzata|Latenza media della fase di normalizzazione. La fase di normalizzazione consiste nell'eseguire la normalizzazione dei messaggi in ingresso non elaborati.|
|Latenza media delle fasi del gruppo|Latenza media della fase del gruppo. La fase di raggruppamento prevede l'esecuzione del buffering, l'aggregazione e il raggruppamento dei messaggi normalizzati.| 
|Numero totale errori|Numero totale di errori.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Messa a fuoco e configurazione del connettore Azure per la metrica FHIR (anteprima)
In questo esempio, si concentrerà sul **numero di** metriche dei messaggi in ingresso.

1. Selezionare un punto nel tempo in cui si desidera concentrarsi.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Connector1" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Da questa schermata è possibile **aggiungere la metrica** , **aggiungere filtro** e **applicare la suddivisione** per ulteriori personalizzazioni. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Connector1" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusione 
L'accesso alle metriche del piano dati è essenziale per il monitoraggio e la risoluzione dei problemi.  Il connettore Azure per FHIR ti aiuta a eseguire queste azioni attraverso le metriche. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Azure per FHIR.

>[!div class="nextstepaction"]
>[Connettore Azure per le domande frequenti su FHIR](fhir-faq.md)

*Nel portale di Azure si fa riferimento al Connettore Azure IoT per FHIR come Connettore IoT (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.