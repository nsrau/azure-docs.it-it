---
title: Risoluzione dei problemi di Azure Internet Analyzer | Microsoft Docs
description: Informazioni di riferimento sulla risoluzione dei problemi di Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 74bf0422bbe6c2c1c84365c1e8f9329a01ff9fdd
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909031"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Risoluzione dei problemi di Azure Internet Analyzer

Questo articolo contiene le procedure per la risoluzione dei problemi comuni di Internet Analyzer.

## <a name="azure-portal"></a>Portale di Azure
**"Non sono state raccolte misurazioni sufficienti per questa scorecard" nella sezione scorecard**

Tenere presente quanto segue:
- Lo script client deve essere incorporato in un sito Web **https** . Le misurazioni non verranno raccolte se lo script viene eseguito in un sito Web di testo non crittografato (**http://** ) o locale (**file://** ).
- I dati di misurazione verranno raccolti solo se lo script client del profilo di Internet Analyzer è stato incorporato in un'applicazione che riceve traffico utente reale. Il traffico sintetico, ad esempio i test delle prestazioni di Azure WebApp, in genere non esegue codice JavaScript incorporato, quindi nessuna misura verrà generata da tale tipo di traffico.
- Le serie temporali vengono generate una volta all'ora, quindi è necessario attendere almeno il periodo di tempo per la visualizzazione dei nuovi dati di misurazione.
- Le scorecard vengono generate su base giornaliera (alla fine di ogni giorno, ora UTC).
- Le scorecard vengono generate solo se sono state raccolte più di 100 misure per la combinazione di filtri selezionata (test, periodo di tempo, paese e così via).

## <a name="next-steps"></a>Passaggi successivi
Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
