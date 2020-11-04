---
title: Esportare il connettore Azure per la metrica FHIR (anteprima) tramite le impostazioni di diagnostica
description: Questo articolo illustra come esportare il connettore Azure per la metrica FHIR (anteprima) tramite le impostazioni di diagnostica
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/30/2020
ms.author: jasteppe
ms.openlocfilehash: 2c10dc05775739a55267dd3766e533b73e12b3a1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322354"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Esportare il connettore Azure per la metrica FHIR (anteprima) tramite le impostazioni di diagnostica

In questo articolo si apprenderà come esportare il connettore Azure per i log delle metriche FHIR *. La funzionalità che Abilita la registrazione delle metriche è rappresentata dalle [**impostazioni di diagnostica**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) nella portale di Azure. 

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
 
Per altre informazioni su come usare i log di diagnostica, vedere la [documentazione relativa al log delle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview)

## <a name="conclusion"></a>Conclusione 
L'accesso ai log delle metriche è essenziale per il monitoraggio e la risoluzione dei problemi.  Il connettore Azure per FHIR consente di eseguire queste azioni tramite i log di metrica. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Azure per FHIR.

>[!div class="nextstepaction"]
>[Connettore Azure per le domande frequenti su FHIR](fhir-faq.md)

*Nel portale di Azure si fa riferimento al Connettore Azure IoT per FHIR come Connettore IoT (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.
