---
title: Visualizzare i dati di Azure IoT Central in un dashboard di Power BI | Microsoft Docs
description: Usare Power BI Solution for Azure IoT Central per visualizzare e analizzare i dati di IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: 897b72dd7b292492e45cdc7401fe1acc5c86ca20
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877464"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualizzare e analizzare i dati di Azure IoT Central in un dashboard di Power BI

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Questo argomento riguarda gli amministratori.*

![Pipeline della soluzione Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Usare Power BI Solution for Azure IoT Central per creare un potente dashboard di Power BI con cui monitorare le prestazioni dei dispositivi IoT. Nel dashboard di Power BI è possibile:
- Monitorare la quantità di dati che i dispositivi inviano nel corso del tempo
- Confrontare il volume di dati tra telemetria, stati ed eventi
- Identificare i dispositivi che inviano un numero elevato di misurazioni
- Osservare le tendenze cronologiche delle misurazioni dei dispositivi
- Identificare i dispositivi problematici che segnalano un numero elevato di eventi critici

Questa soluzione consente di configurare la pipeline che riceve i dati nell'account di archiviazione BLOB di Azure dall'[esportazione continua dei dati](howto-export-data.md). I dati passano attraverso Funzioni di Azure, Azure Data Factory e Database SQL di Azure per elaborare e trasformare i dati. L'output può essere visualizzato e analizzato in un report di Power BI che si può scaricare come file PBIX. Tutte queste risorse vengono create nella sottoscrizione di Azure, in modo da poter personalizzare ogni componente in base alle proprie esigenze.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Ottenere [Power BI Solution for Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) da Microsoft AppSource.

## <a name="prerequisites"></a>Prerequisiti
Per configurare la soluzione, è necessario quanto segue:
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

## <a name="architecture"></a>Architettura
È possibile accedere a tutte le risorse create dal portale di Azure. Tutti gli elementi devono far parte di un unico gruppo di risorse.

![Vista del gruppo di risorse nel portale di Azure](media/howto-connect-powerbi/azure-deployment.PNG)

Le specifiche di ogni risorsa e le modalità d'uso sono descritte di seguito.

### <a name="azure-functions"></a>Funzioni di Azure
L'app per le funzioni di Azure viene attivata ogni volta che un nuovo file viene scritto nell'archiviazione BLOB. Le funzioni estraggono i campi all'interno di ogni misurazione, dispositivo e file di modelli di dispositivo e popolano diverse tabelle SQL intermedie che verranno usate da Azure Data Factory.

### <a name="azure-data-factory"></a>Data factory di Azure
Azure Data Factory si connette al database SQL come servizio collegato. Esegue le attività delle stored procedure che elaborano i dati e li archiviano nelle tabelle di analisi.

### <a name="azure-sql-database"></a>Database SQL di Azure
Queste tabelle vengono create automaticamente per popolare i report predefiniti. Esplorare questi schemi in Power BI per poter creare visualizzazioni personalizzate per i dati.

| Nome tabella |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Costi stimati

Di seguito viene fornita una stima dei costi di Azure (Funzioni di Azure, Data Factory, Azure SQL). Tutti i prezzi sono indicati in dollari USA. Tenere presente che i prezzi variano in base all'area, quindi è sempre consigliabile cercare informazioni sui prezzi più recenti dei singoli servizi per conoscere i prezzi effettivi.
Le impostazioni predefinite seguenti sono definite automaticamente nel modello (è possibile modificarle dopo aver completato la configurazione):

- Funzioni di Azure: Piano di servizio app S1, $ 74,40/mese
- Azure SQL S1, circa $ 30/mese

È consigliabile esaminare le diverse opzioni di prezzo e modificare la configurazione in base alle esigenze.

## <a name="resources"></a>Risorse

Visitare AppSource per ottenere [Power BI Solution for Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come visualizzare i dati in Power BI, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come gestire i dispositivi](howto-manage-devices.md)