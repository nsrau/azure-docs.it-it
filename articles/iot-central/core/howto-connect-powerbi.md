---
title: Visualizzare i dati di Azure IoT Central in un dashboard di Power BI | Microsoft Docs
description: Usare Power BI Solution for Azure IoT Central per visualizzare e analizzare i dati di IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: f996bb2d5126ef038ca872aee1f1893979a5229b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "87080999"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualizzare e analizzare i dati di Azure IoT Central in un dashboard di Power BI

*Questo argomento si applica agli amministratori e agli sviluppatori di soluzioni.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Pipeline della soluzione Power BI":::

Usa la soluzione Power BI per Azure IoT Central V3 per creare un dashboard di Power BI potente per monitorare le prestazioni dei tuoi dispositivi. Nel dashboard di Power BI è possibile:

- Monitorare la quantità di dati che i dispositivi inviano nel corso del tempo
- Confrontare i volumi di dati tra flussi di telemetria diversi
- Filtrare i dati inviati da dispositivi specifici
- Visualizzare i dati di telemetria più recenti in una tabella

Questa soluzione configura una pipeline che legge i dati dall'account di archiviazione BLOB di Azure per l' [esportazione dei dati continua](howto-export-data-blob-storage.md) . La pipeline usa funzioni di Azure, Azure Data Factory e il database SQL di Azure per elaborare e trasformare i dati. è possibile visualizzare e analizzare i dati in un report di Power BI scaricato come file PBIX. Tutte le risorse vengono create nella sottoscrizione di Azure, in modo che sia possibile personalizzare ogni componente in base alle esigenze.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per configurare la soluzione sono necessarie le risorse seguenti:

- Applicazione IoT Central versione 3. Per informazioni su come controllare la versione dell'applicazione, vedere [informazioni sull'applicazione](./howto-get-app-info.md). Per informazioni su come creare un'applicazione IoT Central, vedere [creare un'applicazione Azure IOT Central](./quick-deploy-iot-central.md).
- Esportazione continua dei dati configurata per esportare dati di telemetria, dispositivi e modelli di dispositivo nell'archivio BLOB di Azure. Per altre informazioni, vedere [come esportare i dati dell'Internet delle cose nelle destinazioni in Azure](howto-export-data.md).
  - Assicurarsi che solo l'applicazione IoT Central stia esportando i dati nel contenitore BLOB.
  - I [dispositivi devono inviare messaggi con codifica JSON](../../iot-hub/iot-hub-devguide-messages-d2c.md). I dispositivi devono specificare `contentType:application/JSON` e `contentEncoding:utf-8` o o `contentEncoding:utf-16` `contentEncoding:utf-32` nelle proprietà del sistema del messaggio.
- Power BI Desktop (versione più recente). Vedere [Power bi downloads](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (se si desidera condividere il dashboard con altri utenti).

> [!NOTE]
> Se si usa un'applicazione IoT Central versione 2, vedere [visualizzare e analizzare i dati di IOT Central di Azure in un dashboard di Power bi](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-connect-powerbi) nel sito della documentazione sulle versioni precedenti.

## <a name="install"></a>Installazione

Per configurare la pipeline, passare alla pagina [Power BI soluzione per Azure IOT Central V3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) nel sito **Microsoft AppSource** . Selezionare **Get it now**e seguire le istruzioni.

Quando si apre il file PBIX, assicurarsi di leggere e seguire le istruzioni nella pagina di copertina. Queste istruzioni descrivono come connettere il report al database SQL.

## <a name="report"></a>Report

Il file PBIX contiene i **dispositivi e il report di telemetria** Mostra una visualizzazione cronologica dei dati di telemetria inviati dai dispositivi. Fornisce una suddivisione dei diversi tipi di dati di telemetria e Mostra anche i dati di telemetria più recenti inviati dai dispositivi.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Report dispositivi Power BI e telemetria":::

## <a name="pipeline-resources"></a>Risorse pipeline

È possibile accedere a tutte le risorse di Azure che costituiscono la pipeline nell'portale di Azure. Tutte le risorse si trovano nel gruppo di risorse creato quando si configura la pipeline.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Visualizzazione portale di Azure del gruppo di risorse":::

Nell'elenco seguente viene descritto il ruolo di ogni risorsa nella pipeline:

### <a name="azure-functions"></a>Funzioni di Azure

L'app per le funzioni di Azure viene attivata ogni volta che IoT Central scrive un nuovo file nell'archivio BLOB. Le funzioni estraggono i dati dai BLOB di dati di telemetria, dispositivi e modelli di dispositivo per popolare le tabelle SQL intermedie utilizzate Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory si connette al database SQL come servizio collegato. Esegue stored procedure per elaborare i dati e archiviarli nelle tabelle di analisi.

Azure Data Factory viene eseguito ogni 15 minuti per trasformare il batch più recente di dati da caricare nelle tabelle SQL, ovvero il numero minimo corrente per il **trigger di finestra a cascata**.

### <a name="azure-sql-database"></a>database SQL di Azure

Azure Data Factory genera un set di tabelle di analisi per Power BI. È possibile esplorare questi schemi in Power BI e usarli per creare visualizzazioni personalizzate.

## <a name="estimated-costs"></a>Costi stimati

La pagina [Power BI soluzione per Azure IOT Central V3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) nel sito Microsoft AppSource include un collegamento a una stima dei costi per le risorse distribuite.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come visualizzare i dati in Power BI, il passaggio successivo suggerito consiste nell'apprendere [come gestire i dispositivi](howto-manage-devices.md).
