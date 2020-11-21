---
title: Creare un ambiente - Azure Time Series Insights | Microsoft Docs
description: Informazioni su come usare la portale di Azure per creare un nuovo ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023327"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Creare un nuovo ambiente Azure Time Series Insights Gen1 nella portale di Azure

> [!CAUTION]
> È un articolo di Gen1.

Questo articolo descrive come creare un nuovo ambiente di Azure Time Series Insights usando il portale di Azure.

Azure Time Series Insights consente di iniziare a visualizzare ed eseguire query sul flusso di dati in hub eventi e hub eventi di Azure in pochi minuti, consentendo di eseguire query su grandi volumi di dati di serie temporali in pochi secondi.  È stato progettato per ridimensionare l'Internet delle cose (IoT) ed è in grado di gestire terabyte di dati.

## <a name="steps-to-create-the-environment"></a>Procedura per creare l'ambiente

Seguire questa procedura per creare un ambiente:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare il pulsante **+ Crea una risorsa**.

1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   [![Creare l'ambiente Azure Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Nella pagina **Time Series Insights** selezionare **Crea**.

1. Compilare i parametri obbligatori. La tabella seguente descrive ciascun parametro:

   [![Creare il gruppo di risorse Azure Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Impostazione|Valore consigliato|Descrizione
   ---|---|---
   Nome ambiente | Un nome univoco | Questo nome rappresenta l'ambiente in [Esplora serie temporali](https://insights.timeseries.azure.com)
   Subscription | Sottoscrizione in uso | Se si dispone di più sottoscrizioni, scegliere preferibilmente la sottoscrizione che contiene l'origine evento. Azure Time Series Insights possibile rilevare automaticamente le risorse dell'hub eventi e dell'hub eventi di Azure esistenti nella stessa sottoscrizione.
   Resource group | Crearne un nuovo o utilizzarne uno esistente | Un gruppo di risorse è una raccolta di risorse di Azure usate insieme. È possibile scegliere un gruppo di risorse esistente, ad esempio quello che contiene l'hub eventi o l'hub IoT. Oppure, se la risorsa non è correlata alle altre risorse, è possibile crearne una nuova.
   Location | Il più vicino all'origine evento | Scegliere preferibilmente la stessa posizione del data center che contiene i dati dell'origine evento, allo scopo di evitare i costi aggiuntivi per la larghezza di banda tra aree e tra zone e un'ulteriore latenza quando i dati vengono spostati all'esterno dell'area.
   Piano tariffario | S1 | Scegliere la velocità effettiva necessaria. Per la capacità iniziale e i costi minimi, selezionare S1.
   Capacità | 1 | Per Capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.  È possibile modificare la capacità di un ambiente dopo la creazione. Per i costi minimi, selezionare una capacità pari a 1.
  
1. Fare clic su **Crea** per iniziare il processo di provisioning. Questa operazione può richiedere alcuni minuti.

1. Selezionare il simbolo **Notifiche** (icona a forma di campanello) per monitorare il processo di distribuzione.

   [![Controllare le notifiche](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. In **Panoramica** delle risorse verificare le impostazioni di configurazione della distribuzione.

   [![Creare il pin Azure Time Series Insights al dashboard](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Facoltativo)** Selezionare l' **icona Aggiungi** nell'angolo in alto a destra per accedere facilmente all'ambiente Azure Time Series Insights in futuro.

## <a name="next-steps"></a>Passaggi successivi

* [Definire i criteri di accesso ai dati](./concepts-access-policies.md) per proteggere l'ambiente.

* [Aggiungere un'origine evento Hub eventi](./how-to-ingest-data-event-hub.md) a un ambiente Azure Time Series Insights.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Visualizzare l'ambiente in [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).