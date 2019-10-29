---
title: Creare un ambiente Azure Time Series Insights | Microsoft Docs
description: Questo articolo spiega come usare il portale di Azure per creare un nuovo ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 1b51931385eb7e2e0bf2a1baf5cc4c3379eab2df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991240"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Creare un nuovo ambiente Time Series Insights nel portale di Azure

Questo articolo spiega come creare un nuovo ambiente Time Series Insights con il portale di Azure.

Time Series Insights consente di iniziare visualizzando ed eseguendo in pochi minuti le query sui dati trasmessi negli hub IoT di Azure e negli hub eventi, consentendo di eseguire in pochi secondi le query su grandi quantitativi di dati delle serie temporali.  È stato progettato per ridimensionare l'Internet delle cose (IoT) ed è in grado di gestire terabyte di dati.

## <a name="steps-to-create-the-environment"></a>Procedura per creare l'ambiente

Seguire questa procedura per creare un ambiente:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare il pulsante **+ Crea una risorsa** .

1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   [![creare l'ambiente Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox))

1. Nella pagina **Time Series Insights** selezionare **Crea**.

1. Compilare i parametri obbligatori. La tabella seguente descrive ciascun parametro:
   
   [![creare il gruppo di risorse Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Impostazione|Valore consigliato|Descrizione
   ---|---|---
   Nome ambiente | Un nome univoco | Questo nome rappresenta l'ambiente nell'[utilità di esplorazione delle serie temporali](https://insights.timeseries.azure.com)
   Sottoscrizione | Sottoscrizione in uso | Se si dispone di più sottoscrizioni, scegliere preferibilmente la sottoscrizione che contiene l'origine evento. Time Series Insights può rilevare automaticamente le risorse di Hub IoT di Azure e di Hub eventi esistenti nella stessa sottoscrizione.
   Resource group | Crearne un nuovo o utilizzarne uno esistente | Un gruppo di risorse è una raccolta di risorse di Azure usate insieme. È possibile scegliere un gruppo di risorse esistente, ad esempio quello che contiene l'hub eventi o l'hub IoT. Oppure, se la risorsa non è correlata alle altre risorse, è possibile crearne una nuova.
   Percorso | Il più vicino all'origine evento | Scegliere preferibilmente la stessa posizione del data center che contiene i dati dell'origine evento, allo scopo di evitare i costi aggiuntivi per la larghezza di banda tra aree e tra zone e un'ulteriore latenza quando i dati vengono spostati all'esterno dell'area.
   Piano tariffario | S1 | Scegliere la velocità effettiva necessaria. Per la capacità iniziale e i costi minimi, selezionare S1.
   Capacità | 1 | Per Capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.  È possibile modificare la capacità di un ambiente dopo la creazione. Per i costi minimi, selezionare una capacità pari a 1. 
  
1. Fare clic su **Crea** per iniziare il processo di provisioning. L'operazione potrebbe richiedere alcuni minuti.

1. Selezionare il simbolo **Notifiche** (icona a forma di campanello) per monitorare il processo di distribuzione.

   [![guardare le notifiche](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Quando la distribuzione ha esito positivo, è possibile selezionare **Vai alla risorsa** per configurare altre proprietà, impostare la sicurezza con criteri di accesso ai dati, aggiungere le origini eventi e altre azioni.

1. In **Panoramica**delle risorse selezionare l' **icona Aggiungi** nell'angolo in alto a destra per accedere facilmente all'ambiente Time Series Insights in futuro.

   [![creare il pin Time Series Insights nel dashboard](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* [Definire i criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere l'ambiente.

* [Aggiungere un'origine evento Hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) a un ambiente Azure Time Series Insights.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Visualizzare l'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
