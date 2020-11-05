---
title: Esportare le metriche del connettore Azure IoT per FHIR (anteprima) tramite impostazioni di diagnostica
description: Questo articolo illustra come esportare il connettore Azure per la metrica FHIR (anteprima) tramite le impostazioni di diagnostica
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/30/2020
ms.author: jasteppe
ms.openlocfilehash: 5a57b13cdb8c7c1144815543e03b970de32b4369
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394255"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Esportare le metriche del connettore Azure IoT per FHIR (anteprima) tramite impostazioni di diagnostica

In questo articolo si apprenderà come esportare il connettore Azure per i log delle metriche FHIR *. La funzionalità che Abilita la registrazione delle metriche è rappresentata dalle [**impostazioni di diagnostica**](../azure-monitor/platform/diagnostic-settings.md) nella portale di Azure. 

> [!TIP]
> Per configurare la registrazione di controllo, seguire le istruzioni riportate nella pagina relativa all' [Abilitazione della registrazione diagnostica nell'API di Azure per FHIR e nel connettore Azure per FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) .

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Abilitare la registrazione delle metriche per il connettore Azure per FHIR (anteprima)
1. Per abilitare la registrazione delle metriche per il connettore Azure per FHIR, selezionare l'API di Azure per il servizio FHIR nel portale di Azure 

2. Passa a **impostazioni di diagnostica** 

3. Selezionare **+ Aggiungi impostazione di diagnostica**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Immettere un nome nella finestra di dialogo **Nome impostazione di diagnostica** .

5. Selezionare il metodo che si vuole usare per accedere ai log di diagnostica:

    1. **Archiviare in un account di archiviazione per il** controllo o l'ispezione manuale. È necessario che l'account di archiviazione che si vuole usare sia già stato creato.
    2. **Trasmettere a hub eventi** per l'inserimento da parte di un servizio di terze parti o di una soluzione analitica personalizzata. Prima di poter configurare questo passaggio, è necessario creare uno spazio dei nomi dell'hub eventi e un criterio dell'hub eventi.
    3. **Trasmettere all'area di lavoro log Analytics** in monitoraggio di Azure. Per poter selezionare questa opzione, è necessario creare l'area di lavoro log Analytics.

6. Selezionare **errori, traffico e latenza** per il connettore Azure per FHIR.  Selezionare le altre categorie di metriche che si vuole acquisire per l'API di Azure per FHIR.

7. Selezionare **Salva**

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Potrebbero essere necessari fino a 15 minuti per la visualizzazione dei primi log di metrica nel repository di propria scelta.  
 
Per altre informazioni su come usare i log di diagnostica, vedere la [documentazione relativa al log delle risorse di Azure](../azure-monitor/platform/platform-logs-overview.md)

## <a name="conclusion"></a>Conclusione 
L'accesso ai log delle metriche è essenziale per il monitoraggio e la risoluzione dei problemi.  Il connettore Azure per FHIR consente di eseguire queste azioni tramite i log di metrica. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Azure per FHIR.

>[!div class="nextstepaction"]
>[Connettore Azure per le domande frequenti su FHIR](fhir-faq.md)

*Nel portale di Azure si fa riferimento al Connettore Azure IoT per FHIR come Connettore IoT (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.