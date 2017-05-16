---
title: Elaborazione degli eventi in tempo reale con l&quot;elaborazione degli eventi dell&quot;analisi di flusso | Microsoft Docs
description: "Informazioni su come un set di servizi di Azure è in grado di interagire per consentire l’elaborazione e l’analisi in tempo reale degli eventi."
keywords: elaborazione in tempo reale, elaborazione di eventi, architettura di riferimento
services: stream-analytics,event-hubs,storage,sql-database
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: 
ms.assetid: 11af48bc-313c-4527-8c80-91088dc9f3c6
ms.service: stream-analytics
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8d2449f84ca0caf85e22a91e14f04ea3c329be03
ms.lasthandoff: 05/01/2017


---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architettura di riferimento: elaborazione di eventi in tempo reale con Analisi di flusso di Microsoft Azure
L'architettura di riferimento per l'elaborazione di eventi in tempo reale con Analisi dei flussi di Azure è destinata a fornire un progetto generico per la distribuzione in tempo reale di una soluzione di elaborazione dei flussi PaaS con Microsoft Azure.

## <a name="summary"></a>Riepilogo
In genere, le soluzioni di analisi sono basate su funzionalità, ad esempio ETL (estrazione, trasformazione e caricamento) e data warehouse, in cui sono archiviati i dati prima dell'analisi. Le esigenze di modifica, tra cui i dati in arrivo con una maggiore rapidità, spingono al limite il modello esistente. Una soluzione è rappresentata dalla possibilità di analizzare i dati all'interno di flussi in movimento prima dell'archiviazione, e sebbene non si tratti di una nuova funzionalità, l'approccio non è stato adottato ampiamente in tutti i settori industriali verticali. 

Microsoft Azure fornisce un catalogo esteso di tecnologie di analisi in grado di supportare una matrice di scenari di soluzioni e requisiti diversi. Scegliere i servizi Azure da distribuire per una soluzione end-to-end può essere difficile data la vasta gamma di offerte. Questo documento è progettato per descrivere le funzionalità e l'interazione tra i vari servizi Azure che supportano una soluzione di flusso di eventi. Vengono inoltre illustrati alcuni degli scenari in cui i clienti possono trarre vantaggio da questo tipo di approccio.

## <a name="contents"></a>Sommario
* Sunto
* Introduzione all'analisi in tempo reale
* Proposta di valore dei dati in tempo reale in Azure
* Scenari comuni per l’analisi in tempo reale
* Architettura e componenti
  * Origini dati
  * Livello di integrazione dei dati
  * Livello di analisi in tempo reale
  * Livello di archiviazione dei dati
  * Livello presentazione / consumo
* Conclusioni

**Autore:** Charles Feddersen, Solution Architect, Data Insights Center of Excellence, Microsoft Corporation

**Pubblicato:** gennaio 2015

**Revisione:** 1.0

**Download:** [Elaborazione di eventi in tempo reale con Analisi dei flussi di Microsoft Azure](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


