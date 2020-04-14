---
title: Monitorare Azure Data Explorer usando Integrità risorseMonitor Azure Data Explorer using Resource Health
description: Usare Azure Resource Health per monitorare le risorse di Azure Data Explorer.Use Azure Resource Health to monitor Azure Data Explorer resources.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: ee42f45d8c96e908061142b5a8e6b2e5cee21993
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262061"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Monitorare Azure Data Explorer usando Integrità risorse (anteprima)Monitor Azure Data Explorer using Resource Health (Preview)

[Integrità delle risorse](/azure/service-health/resource-health-overview) per Azure Data Explorer informa l'utente dell'integrità della risorsa Azure Data Explorer e fornisce consigli fruibili per la risoluzione dei problemi. L'integrità delle risorse viene aggiornata ogni 1-2 minuti e segnala l'integrità corrente e passata delle risorse. 

Integrità risorse determina l'integrità della risorsa Azure Data Explorer esaminando vari controlli dello stato di integrità, ad esempio:Resource Health determines the health of your Azure Data Explorer resource by examining various health status checks such as:
* Disponibilità delle risorse
* Errori di query

## <a name="access-resource-health-reporting"></a>Accedere ai report sull'integrità delle risorseAccess Resource Health reporting

1. Nel [portale di Azure](https://portal.azure.com/)selezionare **Monitora** dall'elenco dei servizi.
1. Selezionare**Integrità integrità** **servizio** > .
1. Nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione. Nell'elenco a discesa Tipo di **risorsa** selezionare **Azure Data Explorer**.
1. Nella tabella risultante sono elencate tutte le risorse nella sottoscrizione scelta. Ogni risorsa diverrà un'icona dello stato di integrità che indica l'integrità della risorsa.
1. Selezionare la risorsa per visualizzarne [lo stato di integrità](#resource-health-status) e i suggerimenti.

    ![Panoramica](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Stato di integrità delle risorse

L'integrità di una risorsa viene visualizzata con uno dei seguenti stati, disponibile, non disponibile e sconosciuto.

### <a name="available"></a>Disponibile

Lo stato di integrità **Disponibile** indica che la risorsa di Azure Data Explorer è integra e non ha problemi.

![Disponibile](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Non disponibile

Lo stato di integrità **Non disponibile** indica che si è verificato un problema in corso con la risorsa di Azure Data Explorer che la rende non disponibile per le query e l'inserimento. Ad esempio, i nodi nella risorsa Azure Data Explorer potrebbero essere stati riavviati in modo imprevisto. Se la risorsa di Azure Data Explorer rimane in questo stato per un periodo di tempo prolungato, contattare il supporto tecnico.

![Non disponibile](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Unknown

Lo stato di integrità **Sconosciuto** indica che **Integrità risorse** non ha ricevuto informazioni su questa risorsa di Azure Data Explorer per più di 10 minuti. Questo stato non è un'indicazione definitiva dell'integrità delle risorse di Azure Data Explorer, ma è un punto dati importante nel processo di risoluzione dei problemi. Se il cluster di Azure Data Explorer funziona come previsto, lo stato cambierà in **Disponibile** entro pochi minuti. Lo stato di integrità **Sconosciuto** può suggerire che un evento nella piattaforma interessa la risorsa. 

> [!TIP]
> L'integrità della risorsa cluster di Azure Data Explorer sarà **Sconosciuta** se si è arrestata.

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Informazioni cronologiche

Nel riquadro **Integrità risorse** > cronologia **di integrità**, accedere a un massimo di quattro settimane di informazioni sullo stato dell'integrità delle risorse. Selezionare la freccia per ulteriori informazioni sui problemi relativi agli eventi di integrità segnalati in questo riquadro. 

![Cronologia](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Passaggi successivi

* [Configurazione degli avvisi di integrità delle risorseConfiguring Resource Health alerts](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Esercitazione: Eseguire l'inserimento dei dati e il monitoraggio delle query in Esplora dati di AzureTutorial: Ingest and query monitoring data in Azure Data Explorer](ingest-data-no-code.md)
* [Monitorare le prestazioni, l'integrità e l'utilizzo di Azure Data Explorer con le metricheMonitor Azure Data Explorer performance, health, and usage with metrics](using-metrics.md)