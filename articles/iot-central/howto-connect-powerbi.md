---
title: Visualizzare i dati di Azure IoT Central in un dashboard di Power BI | Microsoft Docs
description: Usare il modello di soluzione Azure IoT Central Analytics Power BI per visualizzare e analizzare i dati di IoT Central.
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: 85c0432bceef3e94b32fa9b4a2803276b3efee17
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324324"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualizzare e analizzare i dati di Azure IoT Central in un dashboard di Power BI

![Pipeline del modello di soluzione Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Usare il modello di soluzione Azure IoT Central Analytics Power BI per creare un avanzato dashboard di Power BI che consenta di monitorare le prestazioni dei dispositivi IoT. Nel dashboard di Power BI è possibile:
- Monitorare la quantità di dati che i dispositivi inviano nel corso del tempo
- Confrontare il volume di dati tra telemetria, stati ed eventi
- Identificare i dispositivi che inviano un numero elevato di misurazioni
- Osservare le tendenze cronologiche delle misurazioni dei dispositivi
- Identificare i dispositivi problematici che segnalano un numero elevato di eventi critici

Questo modello di soluzione consente di impostare la pipeline che riceve i dati nell'account di archiviazione Blob di Azure dall'[esportazione continua dei dati](howto-export-data.md). Questi dati passano attraverso Funzioni di Azure, Azure Data Factory e il database SQL di Azure, che li elaborano e li trasformano in modo da poter essere visualizzati e analizzati in un report di Power BI che è possibile scaricare come file con estensione PBIX. Tutte queste risorse vengono create nella sottoscrizione di Azure, in modo da poter personalizzare ogni componente in base alle proprie esigenze. Questo modello di soluzione è completamente open source; pertanto, per altre informazioni sull'architettura e sull'estensione della soluzione, è possibile visitare il [repository GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

**[Ottenere il modello di soluzione Azure IoT Central Analytics da Microsoft AppSource.](https://aka.ms/iotcentralpowerbisolutiontemplate)**

## <a name="prerequisites"></a>Prerequisiti
Per configurare il modello è necessario disporre di:
- Accesso a una sottoscrizione di Azure
- Dati esportati tramite l'[esportazione continua dei dati](howto-export-data.md) dall'app IoT Central. Si consiglia di attivare le misurazioni, i dispositivi e i flussi dei modelli di dispositivo per sfruttare al massimo il dashboard di Power BI.
- Power BI Desktop (versione più recente)
- Power BI Pro (se si vuole condividere il dashboard con altri utenti)

## <a name="reports"></a>Report

Vengono generati automaticamente due report. 

Il primo report mostra una visualizzazione cronologica delle misurazioni inviate dai dispositivi e suddivide i diversi tipi di misure e di dispositivi che hanno inviato il maggior numero di misurazioni.

![Pagina 1 del report di Power BI](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Il secondo report approfondisce gli eventi e mostra una visualizzazione cronologica degli errori e degli avvisi segnalati. Mostra anche quali dispositivi segnalano il maggior numero di eventi nel complesso e, in particolare, di eventi di errore e di avviso.

![Pagina 2 del report di Power BI](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Risorse

Visitare AppSource per ottenere il [modello di soluzione Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Visitare il [repository GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) per altre informazioni sull'architettura e per estendere la soluzione.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come visualizzare i dati in Power BI, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come gestire i dispositivi](howto-manage-devices.md)
