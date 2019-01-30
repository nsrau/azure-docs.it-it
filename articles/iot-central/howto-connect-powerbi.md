---
title: Visualizzare i dati di Azure IoT Central in un dashboard di Power BI | Microsoft Docs
description: Usare il modello di soluzione Azure IoT Central Analytics Power BI per visualizzare e analizzare i dati di IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: a37eb68d135d156c25c58bf91489d5b50ac2c170
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423121"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualizzare e analizzare i dati di Azure IoT Central in un dashboard di Power BI

*Questo argomento riguarda gli amministratori.*

![Pipeline del modello di soluzione Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Usare il modello di soluzione Azure IoT Central Analytics Power BI per creare un avanzato dashboard di Power BI che consenta di monitorare le prestazioni dei dispositivi IoT. Nel dashboard di Power BI è possibile:
- Monitorare la quantità di dati che i dispositivi inviano nel corso del tempo
- Confrontare il volume di dati tra telemetria, stati ed eventi
- Identificare i dispositivi che inviano un numero elevato di misurazioni
- Osservare le tendenze cronologiche delle misurazioni dei dispositivi
- Identificare i dispositivi problematici che segnalano un numero elevato di eventi critici

Questo modello di soluzione consente di impostare la pipeline che riceve i dati nell'account di archiviazione Blob di Azure dall'[esportazione continua dei dati](howto-export-data.md). I dati passano attraverso Funzioni di Azure, Azure Data Factory e Database SQL di Azure per elaborare e trasformare i dati. L'output può essere visualizzato e analizzato in un report di Power BI che si può scaricare come file PBIX. Tutte queste risorse vengono create nella sottoscrizione di Azure, in modo da poter personalizzare ogni componente in base alle proprie esigenze. Questo modello di soluzione è completamente open source; pertanto, per altre informazioni sull'architettura e sull'estensione della soluzione, è possibile visitare il [repository GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsbpsolutiontemplatescomnamemicrosoft-iotcontinuousdataexporttemplate-from-microsoft-appsource"></a>Ottenere il [modello di soluzione Azure IoT Central Analytics](https://bpsolutiontemplates.com/?name=Microsoft-IoTContinuousDataExportTemplate) da Microsoft AppSource.

## <a name="prerequisites"></a>Prerequisiti
Per configurare il modello è necessario disporre di:
- Accesso a una sottoscrizione di Azure
- Dati esportati usando l'[esportazione continua dei dati](howto-export-data.md) dall'app IoT Central. Si consiglia di attivare le misurazioni, i dispositivi e i flussi dei modelli di dispositivo per sfruttare al massimo il dashboard di Power BI.
- Power BI Desktop (versione più recente)
- Power BI Pro (se si vuole condividere il dashboard con altri utenti)

## <a name="reports"></a>Report

Vengono generati automaticamente due report. 

Il primo report mostra una visualizzazione cronologica delle misurazioni inviate dai dispositivi e suddivide i diversi tipi di misure e di dispositivi che hanno inviato il maggior numero di misurazioni.

![Pagina 1 del report di Power BI](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Il secondo report approfondisce gli eventi e mostra una visualizzazione cronologica degli errori e degli avvisi segnalati. Mostra anche quali dispositivi segnalano il maggior numero di eventi nel complesso e, in particolare, di eventi di errore e di avviso.

![Pagina 2 del report di Power BI](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Risorse

Visitare AppSource per ottenere il [modello di soluzione Azure IoT Central Analytics](https://bpsolutiontemplates.com/?name=Microsoft-IoTContinuousDataExportTemplate).

Visitare il [repository GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) per altre informazioni sull'architettura e per estendere la soluzione.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come visualizzare i dati in Power BI, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come gestire i dispositivi](howto-manage-devices.md)
