---
title: Risoluzione dei problemi di Azure Internet Analyzer | Microsoft Docs
description: Informazioni di riferimento sulla risoluzione dei problemi di Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897390"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Risoluzione dei problemi di Azure Internet Analyzer

Questo articolo contiene le procedure per la risoluzione dei problemi comuni di Internet Analyzer.

## <a name="azure-portal"></a>Portale di Azure
**"Non sono state raccolte misurazioni sufficienti per questa scorecard" nella sezione scorecard**

Si noti che:
- I dati di misurazione verranno raccolti solo se lo script client del profilo di Internet Analyzer è stato incorporato in un'applicazione che riceve traffico utente reale. Il traffico sintetico, ad esempio i test delle prestazioni di Azure WebApp, in genere non esegue codice JavaScript incorporato, quindi nessuna misura verrà generata da tale tipo di traffico.
- Le serie temporali vengono generate una volta all'ora, quindi è necessario attendere almeno il periodo di tempo per la visualizzazione dei nuovi dati di misurazione.
- Le scorecard vengono generate su base giornaliera (alla fine di ogni giorno, ora UTC).
- Le scorecard vengono generate solo se sono state raccolte più di 100 misure per la combinazione di filtri selezionata (test, periodo di tempo, paese e così via).

## <a name="next-steps"></a>Passaggi successivi
Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
