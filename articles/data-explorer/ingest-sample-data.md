---
title: Inserire i dati di esempio in Esplora dati di Azure
description: Informazioni su come inserire (caricare) i dati di esempio relativi al meteo in Esplora dati di Azure.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7fdd32f9263b4d1694a0516a98b681ba8744ab6b
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394579"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Inserire i dati di esempio in Esplora dati di Azure

Questo articolo illustra come inserire (caricare) i dati di esempio in un database di Esplora dati di Azure. Esistono [diversi modi per inserire i dati](ingest-data-overview.md); questo articolo è incentrato su un approccio di base adatto a scopo di test.

> [!NOTE]
> Si dispone già di questi dati se è stato completato [Avvio rapido: inserire dati usando la libreria Python di Esplora dati di Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Prerequisiti

[Un cluster e un database di test](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Inserire dati

Il set di dati di esempio **StormEvents** contiene dati relativi al meteo del [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Accedere a [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. In alto a sinistra dell'applicazione, selezionare **Aggiungi cluster**.

1. Nella finestra di dialogo **Add cluster** (Aggiungi cluster) immettere l'URL del cluster nel modulo`https://<ClusterName>.<Region>.kusto.windows.net/`, quindi selezionare **Aggiungi**.

1. Incollare il comando seguente e selezionare **Esegui**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Al termine dell'inserimento, incollare la query seguente, selezionare la query nella finestra e selezionare **Esegui**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    La query restituisce i risultati seguenti dai dati di esempio inseriti.

    ![Risultati query](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Passaggi successivi

[Scrivere query](write-queries.md)

[Inserimento dati in Esplora dati di Azure](ingest-data-overview.md)