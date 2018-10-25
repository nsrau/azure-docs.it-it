---
title: Procedure consigliate per la creazione di App con LUIS - Language Understanding
titleSuffix: Azure Cognitive Services
description: Informazioni sulle procedure consigliate LUIS per ottenere i risultati migliori.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 6b7b9c8a2a3e7da1628ef3e6f93a4b632ce38615
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637781"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Procedure consigliate per la creazione di un'app di riconoscimento vocale con Servizi cognitivi
Usare il processo di creazione dell'app per compilare l'app LUIS. 

* Compilare il modello linguistico
* Aggiungere alcune espressioni di esempio di training (10-15 per finalità)
* Pubblica 
* Test dall'endpoint 
* Aggiungere funzionalità

Dopo che l'app è stata [pubblicata](luis-how-to-publish-app.md), usare il ciclo di creazione per aggiungere funzionalità, pubblicare e testare dall'endpoint. Non iniziare il ciclo di creazione successivo aggiungendo ulteriori espressioni di esempio. Questo non consente a LUIS di apprendere il modello con espressioni utente reali. 

Affinché LUIS assolva alla sua funzione di apprendimento in modo efficiente, non espandere le espressioni finché il set corrente di espressioni di esempio e di endpoint non restituiscono punteggi di stima elevati. Migliorare i punteggi usando l'apprendimento attivo, [criteri](luis-concept-patterns.md) ed [elenchi di frasi](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Procedure consigliate e procedure rischiose
L'elenco seguente include le procedure consigliate per le app LUIS:

|Procedura consigliata|Procedura rischiosa|
|--|--|
|[Definire le finalità distinte](#do-define-distinct-intents) |[Aggiungere molte espressioni di esempio alle finalità](#dont-add-many-example-utterances-to-intents) |
|[Trovare un compromesso tra troppo generico e troppo specifico per ogni finalità](#do-find-sweet-spot-for-intents)|[Usare LUIS come piattaforma di training](#dont-use-luis-as-a-training-platform)|
|[Creare l'app in modo iterativo](#do-build-the-app-iteratively)|[Aggiungere molte espressioni di esempio dello stesso formato, ignorando gli altri](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Aggiungere elenchi di frasi e criteri nelle iterazioni successive](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Combinare la definizione di finalità ed entità](#dont-mix-the-definition-of-intents-and-entities)|
|[Aggiungere espressioni di esempio alla finalità None (Nessuna)](#do-add-example-utterances-to-none-intent)|[Creare elenchi di frasi con tutti i valori possibili](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Usare la funzionalità suggerimento per l'apprendimento attivo](#do-leverage-the-suggest-feature-for-active-learning)|[Aggiungere molti criteri](#dont-add-many-patterns)|
|[Monitorare le prestazioni dell'app](#do-monitor-the-performance-of-your-app)|[Eseguire il training e pubblicare con ogni singola espressione di esempio aggiunta](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definire finalità distinte
Assicurarsi che il vocabolario per ogni finalità sia specifico di quella finalità e che non si sovrapponga a un'altra. Ad esempio, se si desidera un'app per gestire i viaggi, ad esempio i voli aerei e gli hotel, è possibile scegliere di definire finalità separate o una sola finalità con entità per dati specifici all'interno dell'espressione.

Se il vocabolario tra due finalità è lo stesso, combinare la finalità e usare le entità. 

Considerare le espressioni di esempio seguenti:

```
Book a flight
Book a hotel
```

"Prenota un volo" e "Prenota un hotel" usano lo stesso vocabolario di "prenota un". È una sovrapposizione, pertanto dovrebbe essere la stessa finalità con le parole diverse delle entità estratte volo e hotel. 

## <a name="do-find-sweet-spot-for-intents"></a>Trovare un compromesso per le finalità
Usare i dati di stima LUIS per determinare se le finalità si sovrappongono. Le finalità sovrapposte confondono LUIS. Il risultato è che la finalità con il punteggio più alto è troppo vicina a un'altra finalità. Poiché LUIS non usa ogni volta lo stesso percorso esatto attraverso i dati per il training, una finalità sovrapposta ha la possibilità di essere la prima o la seconda nel training. Si desidera che il punteggio dell'espressione per ogni finalità sia più distante affinché questo non si verifichi. La distinzione ottimale tra le finalità dovrebbe produrre ogni volta la finalità principale prevista. 
 
## <a name="do-build-the-app-iteratively"></a>Compilare l'app in modo iterativo
Eseguire un set di *blind* test che non sia usato come [espressioni di esempio](luis-concept-utterance.md) o endpoint. Continuare a migliorare l'app per il set di test. Adattare il set di test per riflettere espressioni utente reali. Usare questo set di blind test per valutare ogni iterazione. 

Gli sviluppatori devono disporre di tre set di dati. Il primo è dato dalle espressioni di esempio per compilare il modello. Il secondo riguarda il test del modello in corrispondenza dell'endpoint. Il terzo è dato dai dati del blind test usati nel [test in batch](luis-how-to-batch-test.md). L'ultimo set non viene usato per il training dell'applicazione né inviato all'endpoint.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Aggiungere elenchi di frasi e criteri nelle iterazioni successive
Gli [elenchi di frasi](luis-concept-feature.md) consentono di definire dizionari di parole correlate al dominio dell'app. Effettuare il seeding dell'elenco di frasi con alcune parole, quindi usare la funzionalità suggerimento in modo che LUIS apprenda un maggior numero di parole nel vocabolario. Non aggiungere ogni parola al vocabolario poiché l'elenco di frasi non è una corrispondenza esatta. 

Le espressioni utente reali dall'endpoint, molto simili tra loro, potrebbero rivelare criteri di scelta e posizionamento di parole. La funzionalità [criteri](luis-concept-patterns.md) apprende la scelta e il posizionamento delle parole oltre alle espressioni regolari per migliorare la precisione della stima. Un'espressione regolare nei criteri tiene conto delle parole e della punteggiatura che si intende ignorare mantenendo la corrispondenza ai criteri. 

Usare la sintassi facoltativa dei criteri per la punteggiatura per poterla ignorare.

Non applicare queste procedure prima che l'app abbia ricevuto le richieste endpoint per non distorcere l'attendibilità.  

## <a name="do-add-example-utterances-to-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)
Si tratta della finalità di fallback, che indica ogni cosa all'esterno dell'applicazione. Aggiungere un'espressione di esempio alla finalità None (Nessuna) ogni 10 espressioni di esempio nel resto dell'app LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Usare la funzionalità suggerimento per l'apprendimento attivo
Usare regolarmente [Review endpoint utterances](luis-how-to-review-endoint-utt.md) (Rivedi espressioni endpoint) dell'**apprendimento attivo** anziché aggiungere più espressioni di esempio alle finalità. Poiché l'app riceve costantemente espressioni endpoint, questo elenco è destinato a crescere e a cambiare.

## <a name="do-monitor-the-performance-of-your-app"></a>Non monitorare le prestazioni dell'app
Monitorare la precisione della stima usando un set di test. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Non aggiungere molte espressioni di esempio alle finalità
Dopo la pubblicazione dell'app, aggiungere solo le espressioni dall'apprendimento attivo nel processo iterativo. Se le espressioni sono troppo simili, aggiungere un criterio. 

## <a name="dont-use-luis-as-a-training-platform"></a>Non usare LUIS come piattaforma di training
LUIS è specifico di un dominio di modello di linguaggio. Non è destinato a svolgere la funzione di piattaforma di training generica. 

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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [pianificazione dell'app](luis-how-plan-your-app.md) nell'app LUIS.