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
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420960"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Domande frequenti su Metrics Advisor

### <a name="what-is-the-cost-of-my-instance"></a>Qual è il costo dell'istanza?

Attualmente non è previsto alcun costo per l'uso dell'istanza durante l'anteprima.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Perché non è possibile creare la risorsa? Il "piano tariffario" non è disponibile e indica che è già stato creato 1 S0 per questa sottoscrizione.

:::image type="content" source="media/pricing.png" alt-text="Messaggio quando esiste già una risorsa F0":::

Durante l'anteprima pubblica, è possibile creare solo un'istanza di metriche Advisor per area in una sottoscrizione.

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

:::image type="content" source="media/query-result.png" alt-text="Risultato di una query con un timestamp" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Ricerca per categorie rilevare picchi & DIP come anomalie?

Se si dispone di soglie rigide predefinite, è possibile impostare manualmente "soglia rigida" nelle [configurazioni di rilevamento anomalie](how-tos/configure-metrics.md#anomaly-detection-methods).
Se non sono presenti soglie, è possibile usare il "rilevamento intelligente", che è basato su intelligenza artificiale. Per informazioni dettagliate, vedere [ottimizzare la configurazione di rilevamento](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Ricerca per categorie rilevare i modelli regolari (stagionali) come anomalie?

"Rilevamento intelligente" è in grado di apprendere il modello dei dati, inclusi i modelli stagionali. Rileva quindi i punti dati che non sono conformi ai criteri normali come anomalie. Per informazioni dettagliate, vedere [ottimizzare la configurazione di rilevamento](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Ricerca per categorie rilevare le linee piatte come anomalie?

Se i dati sono in genere piuttosto instabili e fluttuano molto e si vuole ricevere un avviso quando diventa troppo stabile o addirittura una linea piatta, è possibile configurare la "soglia di modifica" per rilevare questi punti dati quando la modifica è troppo piccola.
Per informazioni dettagliate, vedere [configurazioni di rilevamento anomalie](how-tos/configure-metrics.md#anomaly-detection-methods) .

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>Come configurare le impostazioni di posta elettronica e abilitare gli avvisi tramite posta elettronica?

1.  Un utente con privilegi di amministratore della sottoscrizione o del gruppo di risorse deve passare alla risorsa Advisor di metrica creata nella portale di Azure e selezionare la scheda **controllo di accesso (IAM)** . 
2.  Selezionare **Aggiungi assegnazioni di ruolo**
3.  Selezionare un ruolo di **amministratore di Advisor metrica di servizi cognitivi** , selezionare l'account come nell'immagine seguente.
4.  Fare clic sul pulsante **Save (Salva** ). l'operazione è stata aggiunta come amministratore della risorsa Advisor di metrica. Si noti che tutte le azioni precedenti devono essere eseguite dall'amministratore della sottoscrizione o dall'amministratore del gruppo di risorse. 

:::image type="content" source="media/access-control.png" alt-text="Pagina di controllo di accesso (IAM) con aggiungere un'assegnazione di ruolo selezionata, seguita da una casella assegnando l'accesso all'utente selezionato visualizzato con un ruolo di accesso dell'amministratore di metrica di servizi cognitivi, seguito dal pulsante Salva dell'interfaccia utente selezionata per illustrare i passaggi per la ricerca di un utente e l'aggiunta di un particolare livello di autorizzazioni di accesso." lightbox="media/access-control.png":::


5.  La propagazione delle autorizzazioni potrebbe richiedere fino a un minuto. Selezionare quindi l'area di lavoro di Advisor di metrica e selezionare l'opzione **impostazione posta elettronica** nel riquadro di spostamento a sinistra. Inserire gli elementi necessari, in particolare le informazioni relative a SMTP. 
6.  Selezionare **Save (Salva** ) per impostare la configurazione della posta elettronica. È possibile creare nuovi hook e sottoscrivere le anomalie delle metriche per gli avvisi quasi in tempo reale. 

## <a name="advanced-concepts"></a>Concetti avanzati

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>In che modo la metrica Advisor compila un albero degli eventi imprevisti per le metriche multidimensionali?

Una metrica può essere suddivisa in più serie temporali in base alle dimensioni. Ad esempio, la metrica `Response latency` viene monitorata per tutti i servizi di proprietà del team. La `Service` categoria può essere utilizzata come dimensione per arricchire la metrica, in modo che venga `Response latency` divisa per `Service1` , `Service2` e così via. Ogni servizio può essere distribuito in computer diversi in più data center, quindi la metrica potrebbe essere ulteriormente divisa da `Machine` e `Data center` .

|Servizio| Data center| Computer  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

A partire dal totale `Response latency` , è possibile eseguire il drill-down nella metrica per `Service` , `Data center` e `Machine` . Tuttavia, potrebbe essere opportuno che i proprietari del servizio usino il percorso `Service`  ->  `Data center`  ->  `Machine` o forse è più opportuno che i tecnici dell'infrastruttura usino il percorso `Data Center`  ->  `Machine`  ->  `Service` . Tutto dipende dai singoli requisiti aziendali degli utenti. 

In metrica Advisor gli utenti possono specificare qualsiasi percorso di cui si desidera eseguire il drill-down o il rollup da un nodo della topologia gerarchica. Più precisamente, la topologia gerarchica è un grafo aciclici diretto anziché una struttura ad albero. Esiste una topologia gerarchica completa costituita da tutte le possibili combinazioni di dimensioni, come la seguente: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="diagramma della topologia gerarchica costituito da più vertici e bordi interconnessi con più dimensioni denominate S, DC e M con numeri corrispondenti compresi tra 1 e 6." lightbox="media/dimension-combinations-view.png":::

In teoria, se la dimensione `Service` presenta `Ls` valori distinti, la dimensione `Data center` dispone di `Ldc` valori distinti e la dimensione `Machine` ha `Lm` valori distinti, è possibile che `(Ls + 1) * (Ldc + 1) * (Lm + 1)` nella topologia gerarchica siano presenti combinazioni di dimensioni. 

In genere, tuttavia, non tutte le combinazioni di dimensioni sono valide, il che può ridurre significativamente la complessità. Attualmente, se gli utenti aggregano la metrica, il numero di dimensioni non è limitato. Se è necessario usare la funzionalità rollup fornita da metrica Advisor, il numero di dimensioni non deve essere superiore a 6. Tuttavia, viene limitato il numero di serie temporali espanse per dimensioni per una metrica a un valore inferiore a 10.000.

Lo strumento dell' **albero degli eventi imprevisti** nella pagina di diagnostica Mostra solo i nodi in cui è stata rilevata un'anomalia anziché l'intera topologia. Questo consente di concentrarsi sul problema corrente. È anche possibile che non vengano visualizzate tutte le anomalie all'interno della metrica e che vengano invece visualizzate le anomalie più frequenti in base al contributo. In questo modo è possibile individuare rapidamente l'effetto, l'ambito e il percorso di distribuzione dei dati anomali. Che riduce significativamente il numero di anomalie di cui è necessario concentrarsi e consente agli utenti di comprendere e individuare i problemi principali. 
 
Quando si verifica un'anomalia, ad esempio, `Service = S2 | Data Center = DC2 | Machine = M5` la deviazione dell'anomalia influisce sul nodo padre `Service= S2` che ha rilevato anche l'anomalia, ma l'anomalia non influisce sull'intero data center in `DC2` e su tutti i servizi in `M5` . La struttura ad albero dell'evento imprevisto verrà compilata come nello screenshot seguente, l'anomalia principale viene acquisita in `Service = S2` e la causa radice può essere analizzata in due percorsi che entrambi portano a `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 vertici con etichetta con due percorsi distinti connessi da bordi con un nodo comune con etichetta S2. L'anomalia superiore viene acquisita in Service = S2 e la causa principale può essere analizzata dai due percorsi che entrambi portano a Service = S2 | Data Center = DC2 | Computer = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica di Advisor metriche](overview.md)
- [Usare il portale Web](quickstarts/web-portal.md)