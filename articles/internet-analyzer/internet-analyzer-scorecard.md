---
title: Interpretazione della tua scorecard Documenti Microsoft
description: Domande frequenti su Azure Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512886"
---
# <a name="interpreting-your-scorecard"></a>Interpretazione della scorecard

La scheda scorecard contiene i risultati aggregati e analizzati dei test. Ogni test ha le proprie scorecard. Le scorecard forniscono riepiloghi rapidi e significativi dei risultati delle misurazioni per fornire risultati basati sui dati per i requisiti di rete. Internet Analyzer si occupa dell'analisi, consentendo di concentrarsi sulla decisione.

La scheda scorecard è disponibile nel menu delle risorse di Internet Analyzer. 


## <a name="filters"></a>Filtri

* ***Test:*** Selezionare il test per cui si desidera visualizzare i risultati: ogni test ha una propria scorecard. I dati di test verranno visualizzati una volta che sono presenti dati sufficienti per completare l'analisi, nella maggior parte dei casi, questo dovrebbe essere entro 24 ore. 
* ***Periodo di tempo & data di fine:*** Vengono generate al giorno tre scorecard, ovvero ciascuna scorecard, in base a un periodo di aggregazione diverso, ovvero le 24 ore precedenti (giorno), i sette giorni precedenti (settimana) e i 30 giorni precedenti (mese). Utilizza il filtro "Data di fine" per selezionare l'ultimo giorno del periodo di tempo che desideri visualizzare. 
* ***Nazione:*** Per ogni paese in cui sono utenti finali, viene generata una scorecard. Il filtro globale contiene tutti gli utenti finali.

## <a name="measurement-count"></a>Conteggio misurazioni

Il numero di misurazioni influisce sulla fiducia dell'analisi. Più alto è il conteggio, più accurato è il risultato. Come minimo, i test dovrebbero puntare a un minimo di 100 misure per endpoint al giorno. Se i conteggi delle misurazioni sono troppo bassi, configurare il client JavaScript in modo che venga eseguito più frequentemente nell'applicazione. I conteggi di misurazione per gli endpoint A e B dovrebbero essere molto simili anche se ci si aspetta e va bene piccole differenze. In caso di grandi differenze, i risultati non devono essere considerati attendibili.

## <a name="percentiles"></a>Percentiles

La latenza, misurata in millisecondi, è una metrica comune per misurare la velocità tra un'origine e una destinazione su Internet. I dati di latenza non vengono normalmente distribuiti (cioè non segue una "curva campana") perché esiste una "lunga coda" di valori di latenza di grandi dimensioni che difalterano i risultati quando si utilizzano statistiche come la media aritmetica. In alternativa, i percentili forniscono un modo "senza distribuzione" per analizzare i dati. Ad esempio, la mediana, o 50esimo percentile, riassume la metà della distribuzione - metà dei valori sono al di sopra di esso e la metà sono al di sotto di esso. Un valore del 75esimo percentile indica che è maggiore del 75% di tutti i valori nella distribuzione. Internet Analyzer si riferisce ai percentili in forma abbreviata come P50, P75 e P95.

I percentili di Internet Analyzer sono _metriche di esempio._ Questo è in contrasto con la _metrica della popolazione_reale . Ad esempio, la latenza mediana giornaliera della popolazione vera tra gli studenti dell'Università della California meridionale e Microsoft è il valore di latenza mediano di tutte le richieste durante quel giorno. In pratica, misurare il valore di tutte le richieste è impraticabile, quindi presumiamo che un campione ragionevolmente grande sia rappresentativo della popolazione reale.

Ai fini dell'analisi, P50 (mediana) è utile come valore previsto per una distribuzione della latenza. Percentili più elevati, come P95, sono utili per identificare l'elevata latenza nei casi peggiori. Se sei interessato a comprendere la latenza dei clienti in generale, P50 è la metrica corretta su cui concentrarti. Se si è interessati a comprendere le prestazioni per i clienti con le prestazioni peggiori, P95 dovrebbe essere al centro dell'attenzione. P75 è un equilibrio tra questi due.


## <a name="deltas"></a>Delta

Un delta è la differenza nei valori metrici per gli endpoint A e B. I delta vengono calcolati per mostrare il vantaggio di B su A. I valori positivi indicano che B ha prestazioni migliori rispetto ad A, mentre i valori negativi indicano che le prestazioni di B sono peggiori. I delta possono essere assoluti (ad es. 10 millisecondi) o relativi (5%).

## <a name="confidence-interval"></a>Intervallo di confidenza 

Gli intervalli di confidenza (CI) sono un intervallo di valori con probabilità di contenere la metrica della popolazione, ad esempio mediana, P75 o media. Seguiamo la convenzione statistica comune di utilizzare il 95% CI.

Per Internet Analyzer, un intervallo di confidenza ridotto è valido perché mostra che la metrica di esempio è probabilmente molto simile alla metrica di popolazione effettiva. Un intervallo di confidenza ampio significa minore certezza che la metrica del campione rifletta la metrica della popolazione reale. Il modo migliore per migliorare il CI è aumentare i conteggi delle misurazioni.

## <a name="time-series"></a>Serie temporale 

Una serie temporale mostra come una metrica cambia nel tempo. Su Internet, ci sono molti fattori temporali che influenzano le prestazioni come i periodi di traffico di picco, le differenze di popolazione nei giorni feriali e le festività.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Panoramica](internet-analyzer-overview.md)di Internet Analyzer .
