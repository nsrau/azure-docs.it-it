---
title: Punteggio di attendibilità nel Centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come eseguire operazioni con il punteggio di attendibilità del Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106781"
---
# <a name="alert-confidence-score"></a>Punteggio di attendibilità degli avvisi 

Centro sicurezza di Azure offre visibilità tra le risorse eseguite in Azure e avvisa l'utente quando rileva potenziali problemi. Per un team responsabile per le operazioni di sicurezza, la mole di avvisi da verificare singolarmente può essere molto ampia, pertanto è necessario assegnare priorità agli avvisi da analizzare. L'analisi degli avvisi può essere complessa e richiedere molto tempo, di conseguenza, alcuni avvisi vengono ignorati.

Il punteggio di attendibilità nel Centro sicurezza consente la valutazione dei team e l'assegnazione di priorità degli avvisi. Il Centro sicurezza applica automaticamente le procedure consigliate, gli algoritmi intelligenti e i processi usati dagli analisti per determinare se una minaccia sia legittima e se vengono fornite informazioni dettagliate significative sotto forma di punteggio di attendibilità.

## <a name="how-the-confidence-score-is-triggered"></a>Come viene attivato il punteggio di attendibilità

Gli avvisi vengono generati quando vengono rilevati processi sospetti in esecuzione nelle macchine virtuali. Il Centro sicurezza verifica e analizza gli avvisi nelle macchine virtuali Windows in esecuzione in Azure. Esegue controlli automatici e correlazioni tramite algoritmi avanzati in più entità e origini dati in tutta l'organizzazione e in tutte le risorse di Azure e presenta un punteggio di attendibilità che è indicativo rispetto al livello di affidabilità del Centro sicurezza nei confronti dell'autenticità dell'avviso e che richiede attenzione.

## <a name="understanding-the-confidence-score"></a>Interpretare il punteggio di attendibilità

Il punteggio di attendibilità è compreso tra 1 e 100 e rappresenta il livello di attendibilità del Centro sicurezza rispetto agli avvisi da analizzare. Più elevato è il punteggio, maggiore è l'attendibilità del Centro sicurezza rispetto all'indicazione dell'avviso in merito ad attività dannose autentiche. Il punteggio di attendibilità include un elenco dei motivi principali sul conferimento all'avviso del punteggio di attendibilità. Il punteggio di attendibilità consente agli analisti della sicurezza di assegnare con maggiore facilità la priorità ai propri interventi sugli avvisi, risolvendo prima gli attacchi più critici e poi riducendo il tempo necessario per rispondere ad attacchi e violazioni della sicurezza.

Per visualizzare il punteggio di attendibilità:
- Nel portale del Centro sicurezza, aprire il pannello Avvisi di sicurezza.
-  Gli avvisi e gli eventi imprevisti sono ordinati da quello posizionato più alto a quello più in basso, vale a dire che maggiore è l'attendibilità del Centro sicurezza nel confermare che un avviso rappresenta una minaccia, più vicino questo sarà alla parte superiore della pagina. 


 ![Punteggio di attendibilità][1]

Per visualizzare i dati che hanno contribuito all'attendibilità del Centro sicurezza in un avviso:
- Nel pannello dell'avviso di Sicurezza, in **Attendibilità**, visualizzare le osservazioni che hanno contribuito al punteggio di attendibilità e hanno ottenuto informazioni dettagliate relative all'avviso. Ciò offre informazioni più dettagliate sulla natura delle attività che ha generato l'avviso.

  ![Punteggio di attendibilità sospetto][2]

Usare l'attendibilità del Centro sicurezza per definire le priorità di valutazione degli avvisi nell'ambiente in uso. Il punteggio di attendibilità consente di risparmiare tempo e fatica analizzando automaticamente gli avvisi, applicando procedure consigliate del settore e gli algoritmi intelligenti e funge da analista virtuale per determinare quali minacce sono reali e su cui è necessario concentrare l'attenzione.


## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato come utilizzare il punteggio di attendibilità per assegnare una priorità alle analisi degli avvisi. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
