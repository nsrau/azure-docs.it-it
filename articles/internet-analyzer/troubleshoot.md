---
title: Risoluzione dei problemi relativi a Azure Internet Analyzer - Documenti Microsoft
description: Informazioni di riferimento sulla risoluzione dei problemi per Azure Internet Analyzer.The troubleshooting reference for Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069218"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Risoluzione dei problemi di Azure Internet AnalyzerAzure Internet Analyzer troubleshooting

In questo articolo contiene la procedura per la risoluzione dei problemi comuni di Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Aspetti da considerare
- Lo script client deve essere incorporato in un sito Web **HTTPS.** Le misurazioni non verranno raccolte se lo script viene eseguito in un sito Web di testo non crittografato (**http://**) o locale (**file://**).
- I dati di misurazione verranno raccolti solo se lo script client del profilo Internet Analyzer è stato incorporato in un'applicazione che riceve traffico utente reale. Il traffico sintetico (ad esempio, i test delle prestazioni di Azure WebApp) in genere non esegue codice Javascript incorporato, pertanto non verranno generate misurazioni da tale tipo di traffico.

## <a name="azure-portal"></a>Portale di Azure
**"Impossibile generare una scorecard per la combinazione di filtri selezionata" nella sezione Scorecard**
- Le scorecard vengono generate su base giornaliera (alla fine di ogni giorno, ora UTC).
- Le scorecard vengono generate solo se sono state raccolte più di 100 misurazioni per la combinazione di filtri selezionata (Test, Periodo di tempo, Paese e così via).

**"Conteggio misurazioni totali" è zero per uno o entrambi gli endpoint in un test**
- Le serie temporali e i conteggi delle misure vengono calcolati una volta all'ora, pertanto è necessario attendere almeno la quantità di tempo per visualizzare i nuovi dati di misurazione.
- Internet Analyzer conta solo le misurazioni riuscite (cioè, risposte HTTP 200) per la sua analisi. Se uno o entrambi gli endpoint in un test non sono raggiungibili o restituiscono un codice HTTP non 200, verranno visualizzati con zero misurazioni totali.

## <a name="next-steps"></a>Passaggi successivi
Leggi le [domande frequenti su Internet Analyzer](internet-analyzer-faq.md)
