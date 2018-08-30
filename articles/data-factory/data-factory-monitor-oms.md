---
title: Monitorare Azure Data Factory con OMS | Microsoft Docs
description: Informazioni su come monitorare Azure Data Factory indirizzando i dati a Operations Management Suite (OMS) per l'analisi.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "42146891"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Monitorare Azure Data Factory con Operations Management Suite (OMS)

È possibile usare l'integrazione di Azure Data Factory con Monitoraggio di Azure per indirizzare i dati a Operations Management Suite (OMS). Questa integrazione è utile negli scenari seguenti:

1.  Per scrivere query complesse in un set completo di metriche pubblicate da Data Factory in OMS e per creare avvisi personalizzati per queste query tramite OMS.

2.  Per eseguire il monitoraggio nelle data factory e indirizzare i dati da più data factory a una singola area di lavoro OMS.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Attività iniziali

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Configurare le impostazioni di diagnostica e l'area di lavoro OMS

Abilitare le impostazioni di diagnostica per una data factory.

1.  Selezionare **Monitoraggio di Azure** -> **Impostazioni di diagnostica** -> Seleziona data factory -> Abilita diagnostica.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Fornire le impostazioni di diagnostica, inclusa la configurazione dell'area di lavoro OMS.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Installare il pacchetto OMS di Azure Data Factory Analytics da Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Fare clic su **Crea** e selezionare l'area di lavoro OMS e le relative impostazioni.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Monitorare le metriche di Azure Data Factory usando OMS

L'installazione del pacchetto OMS di **Azure Data Factory Analytics** crea un set predefinito di visualizzazioni che abilita le metriche seguenti:

- Esecuzioni di Azure Data Factory - 1) Esecuzioni di pipeline di Data Factory

- Esecuzioni di Azure Data Factory - 2) Esecuzioni di attività di Data Factory

- Esecuzioni di Azure Data Factory - 3) Esecuzioni di trigger di Data Factory

- Errori di Azure Data Factory - 1) Primi 10 errori di pipeline di Data Factory

- Errori di Azure Data Factory - 2) Prime 10 esecuzioni di attività di pipeline di Data Factory

- Errori di Azure Data Factory - 3) Primi 10 errori di trigger di Data Factory

- Statistiche di Azure Data Factory - 1) Esecuzioni di attività per tipo

- Statistiche di Azure Data Factory - 2) Esecuzioni di trigger per tipo

- Statistiche di Azure Data Factory - 3) Durata massima esecuzioni di pipeline

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

È possibile visualizzare tutte le metriche indicate sopra, esaminare le query alla base di queste metriche, modificare le query, creare avvisi e così via.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [Monitorare e gestire le pipeline a livello di codice](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) per informazioni sul monitoraggio e sulla gestione delle pipeline tramite l'esecuzione di script.
