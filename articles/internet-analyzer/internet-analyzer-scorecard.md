---
title: Interpretazione della scorecard | Microsoft Docs
description: Domande frequenti su Azure Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512886"
---
# <a name="interpreting-your-scorecard"></a>Interpretazione della scorecard

La scheda scorecard contiene i risultati aggregati e analizzati dei test. Ogni test ha le proprie scorecard. Le scorecard forniscono riepiloghi rapidi e significativi dei risultati di misurazione per fornire risultati basati sui dati per i requisiti di rete. Internet Analyzer si occupa dell'analisi, consentendo di concentrarsi sulla decisione.

La scheda scorecard è disponibile nel menu della risorsa Internet Analyzer. 


## <a name="filters"></a>Filtri

* ***Test:*** Selezionare il test per il quale si desidera visualizzare i risultati. ogni test ha una propria scorecard. I dati di test vengono visualizzati quando sono disponibili dati sufficienti per completare l'analisi. nella maggior parte dei casi, l'operazione dovrebbe essere entro 24 ore. 
* ***Data & fine del periodo di tempo:*** Vengono generate tre scorecard ogni giorno: ogni scorecard riflette un periodo di aggregazione diverso, ovvero le 24 ore precedenti (giorno), i sette giorni precedenti (settimana) e i 30 giorni precedenti (mese). Usare il filtro "Data fine" per selezionare l'ultimo giorno del periodo di tempo che si vuole visualizzare. 
* ***Paese:*** Per ogni paese di cui si dispone gli utenti finali, viene generata una scorecard. Il filtro globale contiene tutti gli utenti finali.

## <a name="measurement-count"></a>Conteggio misure

Il numero di misurazioni influisca sulla confidenza dell'analisi. Maggiore è il numero, più precisa è il risultato. Almeno, i test devono puntare per un minimo di 100 misure per endpoint al giorno. Se i conteggi delle misure sono troppo bassi, configurare il client JavaScript per l'esecuzione più frequente nell'applicazione. Il numero di misurazioni per gli endpoint A e B dovrebbe essere molto simile anche se sono previste piccole differenze e OK. Nel caso di grandi differenze, i risultati non devono essere considerati attendibili.

## <a name="percentiles"></a>Percentili

La latenza, misurata in millisecondi, è una metrica diffusa per misurare la velocità tra un'origine e una destinazione in Internet. I dati di latenza non vengono in genere distribuiti (ovvero non seguono una "curva a campana") perché esiste una "lunga coda" di valori di latenza elevata che alterano i risultati quando si utilizzano statistiche quali la media aritmetica. In alternativa, i percentile forniscono un metodo "senza distribuzione" per analizzare i dati. Ad esempio, il mediano, o il cinquantesimo percentile, riepiloga la metà della distribuzione: la metà dei valori è superiore e la metà è inferiore. Un valore del 75 ° percentile significa che è maggiore del 75% di tutti i valori nella distribuzione. Internet Analyzer fa riferimento ai percentile in forma abbreviata come P50, P75 e P95.

Internet Analyzer percentile sono _metriche di esempio_. Questo si differenzia dalla _metrica di popolamento_reale. Ad esempio, la latenza media di popolazione reale giornaliera tra gli studenti presso la University of Southern California e Microsoft è il valore mediano di latenza di tutte le richieste durante tale giorno. In pratica, la misurazione del valore di tutte le richieste è poco pratica, quindi si presuppone che un campione ragionevolmente grande rappresenti la vera popolazione.

Per finalità di analisi, P50 (mediana) è utile come valore previsto per una distribuzione di latenza. I percentile più elevati, ad esempio P95, sono utili per identificare la latenza elevata nei casi peggiori. Se si è interessati a comprendere la latenza dei clienti in generale, P50 è la metrica corretta a cui concentrarsi. Se si è interessati a comprendere le prestazioni per i clienti con prestazioni peggiori, P95 dovrebbe essere lo stato attivo. P75 è un equilibrio tra questi due.


## <a name="deltas"></a>Delta

Un Delta è la differenza nei valori delle metriche per gli endpoint A e B. i Delta vengono calcolati per mostrare il vantaggio di B su un. i valori positivi indicano che B è stato eseguito meglio di un, mentre i valori negativi indicano che le prestazioni di B sono peggiori. I Delta possono essere assoluti (ad esempio 10 millisecondi) o relativi (5%).

## <a name="confidence-interval"></a>intervallo di confidenza 

Gli intervalli di confidenza (CI) sono un intervallo di valori con probabilità che contenga la metrica di popolamento, ad esempio mediana, p75 o media. Si segue la convenzione statistica comune relativa all'uso del 95% CI.

Per Internet Analyzer, un intervallo di confidenza ridotto è valido perché mostra che la metrica di esempio è probabilmente molto vicina alla metrica effettiva della popolazione. Un intervallo di confidenza ampio indica una minore certezza che la metrica di esempio rispecchi la vera metrica della popolazione. Il modo migliore per migliorare l'integrazione continua consiste nell'aumentare i conteggi delle misurazioni.

## <a name="time-series"></a>Serie temporale 

Una serie temporale Mostra come cambia una metrica nel tempo. Su Internet sono presenti molti fattori temporali che influiscono sulle prestazioni, ad esempio i periodi di picco del traffico, le differenze di popolazione del giorno della settimana e le festività.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [Panoramica di Internet Analyzer](internet-analyzer-overview.md).
