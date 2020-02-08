---
title: Risoluzione dei problemi di Azure Internet Analyzer | Microsoft Docs
description: Informazioni di riferimento sulla risoluzione dei problemi di Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069218"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Risoluzione dei problemi di Azure Internet Analyzer

Questo articolo contiene le procedure per la risoluzione dei problemi comuni di Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Aspetti da considerare
- Lo script client deve essere incorporato in un sito Web **https** . Le misurazioni non verranno raccolte se lo script viene eseguito in un sito Web di testo non crittografato (**http://** ) o locale (**file://** ).
- I dati di misurazione verranno raccolti solo se lo script client del profilo di Internet Analyzer è stato incorporato in un'applicazione che riceve traffico utente reale. Il traffico sintetico, ad esempio i test delle prestazioni di Azure WebApp, in genere non esegue codice JavaScript incorporato, quindi nessuna misura verrà generata da tale tipo di traffico.

## <a name="azure-portal"></a>Portale di Azure
**"La scorecard non è stata generata per la combinazione di filtri selezionata" nella sezione scorecard**
- Le scorecard vengono generate su base giornaliera (alla fine di ogni giorno, ora UTC).
- Le scorecard vengono generate solo se sono state raccolte più di 100 misure per la combinazione di filtri selezionata (test, periodo di tempo, paese e così via).

**"Total Measurement count" è zero per uno o entrambi gli endpoint in un test**
- I conteggi delle serie temporali e delle misure vengono calcolati una volta all'ora, quindi è necessario attendere almeno il periodo di tempo per la visualizzazione dei nuovi dati di misurazione.
- Internet Analyzer conta solo le misurazioni con esito positivo (ad esempio, risposte HTTP 200) per l'analisi. Se uno o entrambi gli endpoint di un test non sono raggiungibili o restituiscono un codice HTTP non 200, verranno visualizzati con zero misurazioni totali.

## <a name="next-steps"></a>Passaggi successivi
Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
