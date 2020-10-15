---
title: Domande frequenti su Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Domande frequenti sul servizio metrica Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 42b23876761afa213b07f07b3a61e125dcf0824b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046809"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Domande frequenti su Metrics Advisor

### <a name="what-is-the-cost-of-my-instance"></a>Qual è il costo dell'istanza?

Attualmente non è previsto alcun costo per l'uso dell'istanza durante l'anteprima.

### <a name="why-is-the-demo-website-readonly"></a>Perché il sito Web demo è ReadOnly?

Il [sito Web demo](https://anomaly-detector.azurewebsites.net/) è disponibile pubblicamente. Questa istanza viene resa di sola lettura per evitare il caricamento accidentale di dati.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Perché non è possibile creare la risorsa? Il "piano tariffario" non è disponibile e indica che è già stato creato 1 S0 per questa sottoscrizione.

:::image type="content" source="media/pricing.png" alt-text="Messaggio quando esiste già una risorsa F0":::

Durante l'anteprima pubblica, è consentito creare solo un'istanza di metriche Advisor in una sottoscrizione, in un'area.

Se è già stata creata un'istanza nella stessa area usando la stessa sottoscrizione, è possibile provare un'area diversa o una sottoscrizione diversa per creare una nuova istanza. È anche possibile eliminare un'istanza esistente per crearne una nuova.

Se l'istanza esistente è già stata eliminata ma viene comunque visualizzato l'errore, attendere circa 20 minuti dopo l'eliminazione della risorsa prima di creare una nuova istanza.

## <a name="basic-concepts"></a>Concetti fondamentali

### <a name="what-is-multi-dimensional-time-series-data"></a>Che cosa sono i dati di serie temporali multidimensionali?

Vedere la definizione della [metrica multidimensionale](glossary.md#multi-dimensional-metric)  nel glossario.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Quanti dati sono necessari per il rilevamento delle anomalie da parte di metriche Advisor?

Come minimo, un punto dati può attivare il rilevamento delle anomalie. Questa operazione non comporta tuttavia la migliore accuratezza. Il servizio presuppone una finestra di punti dati precedenti usando il valore specificato come regola "riempimento Gap" durante la creazione del feed di dati.

È consigliabile avere alcuni dati prima del timestamp su cui si vuole eseguire il rilevamento.
A seconda della granularità dei dati, la quantità di dati consigliata varia come indicato di seguito.

| Granularità | Quantità di dati consigliata per il rilevamento |
| ----------- | ------------------------------------- |
| Meno di 5 minuti | 4 giorni di dati |
| da 5 minuti a 1 giorno | 28 giorni di dati |
| Più di un giorno, a 31 giorni | 4 anni di dati |
| Maggiore di 31 giorni | 48 anni di dati |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Perché metrica Advisor non rileva le anomalie dai dati cronologici?

Metrica Advisor è progettato per il rilevamento dei dati di streaming live. Esiste una limitazione della lunghezza massima dei dati cronologici che il servizio riguarderà ed eseguirà il rilevamento delle anomalie. Ciò significa che solo i punti dati dopo un determinato timestamp più vecchio avranno risultati di rilevamento anomalie. Il timestamp meno recente dipende dalla granularità dei dati.

In base alla granularità dei dati, le lunghezze dei dati cronologici che avranno i risultati del rilevamento delle anomalie sono le seguenti.

| Granularità | Lunghezza massima dei dati cronologici per il rilevamento delle anomalie |
| ----------- | ------------------------------------- |
| Meno di 5 minuti | Tempo di onboarding: 13 ore |
| da 5 minuti a meno di un'ora | Tempo di onboarding: 4 giorni  |
| da 1 ora a meno di un giorno | Tempo di onboarding: 14 giorni  |
| 1 giorno | Tempo di onboarding: 28 giorni  |
| Maggiore di 1 giorno, minore di 31 giorni | Tempo di onboarding: 2 anni  |
| Maggiore di 31 giorni | Tempo di onboarding: 24 anni   |

### <a name="more-concepts-and-technical-terms"></a>Altri concetti e termini tecnici

Per ulteriori informazioni, vedere anche il [Glossario](glossary.md) .

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Ricerca per categorie scrivere una query valida per l'inserimento dei dati?  

Per l'inserimento dei dati in metriche Advisor, è necessario creare una query che restituisca le dimensioni dei dati in un singolo timestamp. La query viene eseguita più volte da metrica Advisor per ottenere i dati da ogni timestamp. 

Si noti che la query deve restituire al massimo un record per ogni combinazione di dimensione, a un timestamp specificato. Tutti i record restituiti devono avere lo stesso timestamp. Non devono essere presenti record duplicati restituiti dalla query.

Si supponga, ad esempio, di creare la query seguente per una metrica giornaliera: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Assicurarsi di usare la granularità corretta per la serie temporale. Per una metrica oraria, usare: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Si noti che queste query restituiscono solo i dati in un singolo timestamp e contengono tutte le combinazioni di dimensioni che devono essere inserite da metrica Advisor. 

:::image type="content" source="media/query-result.png" alt-text="Messaggio quando esiste già una risorsa F0" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Ricerca per categorie rilevare picchi & DIP come anomalie?

Se si dispone di soglie rigide predefinite, è possibile impostare manualmente "soglia rigida" nelle [configurazioni di rilevamento anomalie](how-tos/configure-metrics.md#anomaly-detection-methods).
Se non sono presenti soglie, è possibile usare il "rilevamento intelligente", che è basato su intelligenza artificiale. Per informazioni dettagliate, vedere [ottimizzare la configurazione di rilevamento](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Ricerca per categorie rilevare i modelli regolari (stagionali) come anomalie?

"Rilevamento intelligente" è in grado di apprendere il modello dei dati, inclusi i modelli stagionali. Rileva quindi i punti dati che non sono conformi ai criteri normali come anomalie. Per informazioni dettagliate, vedere [ottimizzare la configurazione di rilevamento](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Ricerca per categorie rilevare le linee piatte come anomalie?

Se i dati sono in genere piuttosto instabili e fluttuano molto e si vuole ricevere un avviso quando diventa troppo stabile o addirittura una linea piatta, è possibile configurare la "soglia di modifica" per rilevare questi punti dati quando la modifica è troppo piccola.
Per informazioni dettagliate, vedere [configurazioni di rilevamento anomalie](how-tos/configure-metrics.md#anomaly-detection-methods) .

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica di Advisor metriche](overview.md)
- [Prova il sito demo](quickstarts/explore-demo.md)
- [Usare il portale Web](quickstarts/web-portal.md)