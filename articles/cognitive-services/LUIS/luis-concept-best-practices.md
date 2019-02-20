---
title: Procedure consigliate
titleSuffix: Language Understanding - Azure Cognitive Services
description: Informazioni sulle procedure consigliate LUIS per ottenere i risultati migliori con il modello di app LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: diberry
ms.openlocfilehash: ba51da8b71406cb1bf7446bd66818a6a74e61317
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243417"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Procedure consigliate per la creazione di un'app di riconoscimento vocale con Servizi cognitivi
Usare il processo di creazione dell'app per compilare l'app LUIS. 

* Creare il modello linguistico
* Aggiungere alcune espressioni di esempio di training (10-15 per finalità)
* Pubblicare 
* Eseguire test dall'endpoint 
* Aggiungere caratteristiche

Dopo che l'app è stata [pubblicata](luis-how-to-publish-app.md), usare il ciclo di creazione per aggiungere caratteristiche, pubblicare e testare dall'endpoint. Non iniziare il ciclo di creazione successivo aggiungendo ulteriori espressioni di esempio. Questo non consente a LUIS di apprendere il modello con espressioni utente reali. 

Affinché LUIS assolva alla sua funzione di apprendimento in modo efficiente, non espandere le espressioni finché il set corrente di espressioni di esempio e di endpoint non restituiscono punteggi di stima elevati. Migliorare i punteggi usando l'[apprendimento attivo](luis-concept-review-endpoint-utterances.md), [criteri](luis-concept-patterns.md) ed [elenchi di frasi](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Procedure consigliate e procedure rischiose
L'elenco seguente include le procedure consigliate per le app LUIS:

|Procedura consigliata|Procedura rischiosa|
|--|--|
|[Definire le finalità distinte](#do-define-distinct-intents) |[Aggiungere molte espressioni di esempio alle finalità](#dont-add-many-example-utterances-to-intents) |
|[Trovare un compromesso tra troppo generico e troppo specifico per ogni finalità](#do-find-sweet-spot-for-intents)|[Usare LUIS come piattaforma di training](#dont-use-luis-as-a-training-platform)|
|[Creare l'app in modo iterativo](#do-build-the-app-iteratively)|[Aggiungere molte espressioni di esempio dello stesso formato, ignorando gli altri](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Aggiungere elenchi di frasi e criteri nelle iterazioni successive](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Combinare la definizione di finalità ed entità](#dont-mix-the-definition-of-intents-and-entities)|
|[Bilanciare le espressioni tra tutte le finalità](#balance-your-utterances-across-all-intents) ad eccezione della finalità None (Nessuna).<br>[Aggiungere espressioni di esempio alla finalità None (Nessuna)](#do-add-example-utterances-to-none-intent)|[Creare elenchi di frasi con tutti i valori possibili](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Usare la funzionalità suggerimento per l'apprendimento attivo](#do-leverage-the-suggest-feature-for-active-learning)|[Aggiungere troppi criteri](#dont-add-many-patterns)|
|[Monitorare le prestazioni dell'app](#do-monitor-the-performance-of-your-app)|[Eseguire il training e pubblicare con ogni singola espressione di esempio aggiunta](#dont-train-and-publish-with-every-single-example-utterance)|
|[Usare versioni per ogni iterazione di app](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definire finalità distinte
Assicurarsi che il vocabolario per ogni finalità sia specifico di quella finalità e che non si sovrapponga a un'altra. Ad esempio, se si vuole realizzare un'app per gestire i viaggi, ad esempio i voli aerei e gli hotel, è possibile scegliere di definire queste aree di interesse come finalità separate o una sola finalità con entità per dati specifici all'interno dell'espressione.

Se il vocabolario tra due finalità è lo stesso, combinare la finalità e usare le entità. 

Considerare le espressioni di esempio seguenti:

|Espressioni di esempio|
|--|
|Prenota un volo|
|Prenota un hotel|

"Prenota un volo" e "Prenota un hotel" usano lo stesso vocabolario di "prenota un". Questo formato è uguale, pertanto dovrebbe essere la stessa finalità con le parole diverse delle entità estratte volo e hotel. 

Per altre informazioni:
* Concetto: [Concetti relativi alle finalità nell'app LUIS](luis-concept-intent.md)
* Esercitazione: [Creare un'app LUIS per determinare le intenzioni dell'utente](luis-quickstart-intents-only.md)
* Procedura: [Aggiungere finalità per determinare l'intenzione delle espressioni dell'utente](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Trovare un compromesso per le finalità
Usare i dati di stima LUIS per determinare se le finalità si sovrappongono. Le finalità sovrapposte confondono LUIS. Il risultato è che la finalità con il punteggio più alto è troppo vicina a un'altra finalità. Poiché LUIS non usa ogni volta lo stesso percorso esatto attraverso i dati per il training, una finalità sovrapposta ha la possibilità di essere la prima o la seconda nel training. Si vuole che il punteggio dell'espressione per ogni finalità sia più distante affinché questo non si verifichi. La distinzione ottimale tra le finalità dovrebbe produrre ogni volta la finalità principale prevista. 
 
## <a name="do-build-the-app-iteratively"></a>Compilare l'app in modo iterativo
Mantenere un set separato di espressioni che non viene usato come [espressioni di esempio](luis-concept-utterance.md) o espressioni di endpoint. Continuare a migliorare l'app per il set di test. Adattare il set di test per riflettere espressioni utente reali. Usare questo set di test per valutare ogni iterazione o versione dell'app. 

Gli sviluppatori devono disporre di tre set di dati. Il primo è dato dalle espressioni di esempio per compilare il modello. Il secondo riguarda il test del modello in corrispondenza dell'endpoint. Il terzo è dato dai dati del blind test usati nel [test in batch](luis-how-to-batch-test.md). L'ultimo set non viene usato per il training dell'applicazione né inviato all'endpoint.  

Per altre informazioni:
* Concetto: [Ciclo di creazione per l'app LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Aggiungere elenchi di frasi e criteri nelle iterazioni successive
Gli [elenchi di frasi](luis-concept-feature.md) consentono di definire dizionari di parole correlate al dominio dell'app. Effettuare il seeding dell'elenco di frasi con alcune parole, quindi usare la funzionalità suggerimento in modo che LUIS apprenda un maggior numero di parole nel vocabolario specifiche dell'app. Non aggiungere ogni parola al vocabolario poiché l'elenco di frasi non è una corrispondenza esatta. 

Le espressioni utente reali dall'endpoint, molto simili tra loro, potrebbero rivelare criteri di scelta e posizionamento di parole. La caratteristica [criteri](luis-concept-patterns.md) apprende la scelta e il posizionamento delle parole oltre alle espressioni regolari per migliorare la precisione della stima. Un'espressione regolare nei criteri tiene conto delle parole e della punteggiatura che si intende ignorare mantenendo la corrispondenza ai criteri. 

Usare la [sintassi facoltativa](luis-concept-patterns.md) dei criteri per la punteggiatura per poterla ignorare. Usare l'[elenco esplicito](luis-concept-patterns.md#explicit-lists) per compensare i problemi di sintassi pattern.any. 

Non applicare queste procedure prima che l'app abbia ricevuto le richieste degli endpoint. È necessario comprendere il comportamento dell'app prima che vengano aggiunti elenchi di frasi e criteri. Dopo aver compreso come si comporta l'app senza queste caratteristiche, aggiungerne una alla volta in base alle esigenze. 

Non è sbagliato aggiungerle all'inizio della progettazione del modello, ma è più facile vedere come ogni caratteristica influisce sui risultati se questa viene aggiunta dopo che l'app è stata usata con il traffico reale. 

Non è necessario aggiungere queste caratteristiche con ogni iterazione o modificarle con ogni versione. 

Per altre informazioni:
* Concetto: [Ciclo di creazione per l'app LUIS](luis-concept-app-iteration.md)
* Concetto: [Caratteristiche di tipo elenco di frasi nell'app LUIS](luis-concept-feature.md)
* Concetto: [Migliorare la precisione della stima con i criteri](luis-concept-patterns.md)
* Procedura: [Usare gli elenchi di frasi in segnali boost dell'elenco di parole](luis-how-to-add-features.md)
* Procedura: [Come aggiungere criteri per migliorare la precisione della stima](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Bilanciare le espressioni tra tutte le finalità

Affinché LUIS possa produrre stime precise, la quantità di espressioni di esempio in ogni finalità (ad eccezione della finalità None), deve essere relativamente equivalente. 

In presenza di una finalità con 100 espressioni di esempio e una finalità con 20 espressioni di esempio, la finalità con 100 espressioni produrrà più stime.  

## <a name="do-add-example-utterances-to-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

Si tratta della finalità di fallback, che indica ogni cosa all'esterno dell'applicazione. Aggiungere un'espressione di esempio alla finalità None (Nessuna) ogni 10 espressioni di esempio nel resto dell'app LUIS.

Per altre informazioni:
* Concetto: [Comprendere quali sono le espressioni ottimali per l'app LUIS](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Usare la funzionalità suggerimento per l'apprendimento attivo

Usare regolarmente [Review endpoint utterances](luis-how-to-review-endoint-utt.md) (Rivedi espressioni endpoint) dell'**apprendimento attivo** anziché aggiungere più espressioni di esempio alle finalità. Poiché l'app riceve costantemente espressioni endpoint, questo elenco è destinato a crescere e a cambiare.

Per altre informazioni:
* Concetto: [Concetti per abilitare l'apprendimento attivo tramite la revisione delle espressioni di endpoint](luis-concept-review-endpoint-utterances.md)
* Esercitazione: [Esercitazione: Correggere le stime dubbie con la revisione delle espressioni di endpoint](luis-tutorial-review-endpoint-utterances.md)
* Procedura: [Come esaminare le espressioni dell'endpoint nel portale di LUIS](luis-how-to-review-endoint-utt.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Non monitorare le prestazioni dell'app

Monitorare la precisione della stima usando un set di [test batch](luis-concept-batch-test.md). 

## <a name="dont-add-many-example-utterances-to-intents"></a>Non aggiungere molte espressioni di esempio alle finalità

Dopo la pubblicazione dell'app, aggiungere solo le espressioni dall'apprendimento attivo nel processo iterativo. Se le espressioni sono troppo simili, aggiungere un criterio. 

## <a name="dont-use-luis-as-a-training-platform"></a>Non usare LUIS come piattaforma di training

LUIS è specifico di un dominio di modello di linguaggio. Non è progettato per l'uso come piattaforma di training in linguaggio naturale generale. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Non aggiungere molte espressioni di esempio dello stesso formato, ignorando gli altri

LUIS prevede variazioni nelle espressioni di una finalità. Le espressioni possono variare mantenendo lo stesso significato generale. Le variazioni possono includere lunghezza dell'espressione, scelta e posizionamento delle parole. 

|Non usare lo stesso formato|Usare formati diversi|
|--|--|
|Acquista un biglietto per Seattle<br>Acquista un biglietto per Parigi<br>Acquista un biglietto per Orlando|Acquista 1 biglietto per Seattle<br>Prenota due posti sul red eye per Parigi lunedì prossimo<br>Desidero prenotare 3 biglietti per Orlando per le vacanze di primavera|

La seconda colonna usa verbi diversi (acquista, prenota, riserva), quantità diverse (1, due, 3) e disposizioni diverse delle parole, ma tutti hanno la stessa intenzione di acquistare biglietti aerei. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Non combinare la definizione di finalità ed entità

Creare una finalità per qualsiasi azione che eseguirà il bot. Usare le entità come parametri che rendono possibile tale azione. 

Per un chatbot che prenoterà voli aerei, creare una finalità **BookFlight**. Non creare una finalità per ogni compagnia aerea o per ogni destinazione. Usare questi dati come [entità](luis-concept-entity-types.md) e contrassegnarle nelle espressioni di esempio. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Non creare elenchi di frasi con tutti i valori possibili

Fornire alcuni esempi negli [elenchi di frasi](luis-concept-feature.md), ma non ogni parola. LUIS generalizza e tiene conto del contesto. 

## <a name="dont-add-many-patterns"></a>Non aggiungere molti criteri

Non aggiungere troppi [criteri](luis-concept-patterns.md). LUIS apprenderà rapidamente con un minor numero di esempi. Non sovraccaricare il sistema inutilmente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Non eseguire il training né pubblicare con ogni singola espressione di esempio

Aggiungere 10 o 15 espressioni prima di procedere con il training e la pubblicazione. In questo modo, si vedrà l'impatto sulla precisione della stima. L'aggiunta di una singola espressione potrebbe non avere un impatto visibile sul punteggio. 

## <a name="do-use-versions-for-each-app-iteration"></a>Usare le versioni per ogni iterazione dell'app

Ogni ciclo di creazione deve essere all'interno di una nuova [versione](luis-concept-version.md), clonato da una versione esistente. LUIS non presenta alcun limite per le versioni. Il nome di una versione viene usato come parte della route API, pertanto è importante scegliere caratteri consentiti in un URL, nonché restare entro 10 caratteri per una versione. Sviluppare una strategia per l'assegnazione dei nomi alle versioni, in modo da mantenerle organizzate. 

Per altre informazioni:
* Concetto: [Comprendere come e quando usare una versione di LUIS](luis-concept-version.md)
* Procedura: [Usare le versioni per eseguire modifiche e test senza conseguenze per le app di staging o produzione](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [pianificazione dell'app](luis-how-plan-your-app.md) nell'app LUIS.
