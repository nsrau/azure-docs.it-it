---
title: Funzionalità
titleSuffix: Language Understanding - Azure Cognitive Services
description: L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 7a2d86bb61a27b9360d55775b64d16119fec0601
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474014"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funzionalità di elenco di frasi nell'app LUIS

Nell'apprendimento automatico, una *funzionalità* è un tratto distintivo o un attributo di dati rilevato dal sistema. 

L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare. Le funzionalità consentono a LUIS di riconoscere sia le finalità sia le entità, tuttavia le funzionalità non sono le finalità o le entità. Al contrario, le funzionalità potrebbero fornire esempi di termini correlati.  

## <a name="what-is-a-phrase-list-feature"></a>Che cos'è una funzionalità di un elenco di frasi?
Un elenco di frasi comprende un gruppo di valori (parole o frasi) che appartengono alla stessa classe e che devono essere trattati in modo analogo (ad esempio, i nomi di città o prodotti). Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. L'elenco non è un'[entità elenco](luis-concept-entity-types.md#types-of-entities) chiusa (esatte corrispondenze di testo) di parole associate.

Viene integrato un elenco di frasi al vocabolario del dominio dell'applicazione come secondo segnale di LUIS in merito a tali parole.

## <a name="phrase-lists-help-all-models"></a>Gli elenchi di frasi sono utili per tutti i modelli

Gli elenchi di frasi non sono collegati a una finalità o a un'entità specifica, ma vengono aggiunti come strumento di ottimizzazione per tutti i modelli. Il loro scopo è migliorare il rilevamento delle finalità e la classificazione delle entità.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi
Nell'[esercitazione sulle entità semplici](luis-quickstart-primary-and-secondary-data.md) dell'app per la gestione delle risorse umane, l'app usa un elenco di frasi denominato **Job** che include tipi di mansioni, ad esempio programmatore, costruttore di tetti e segretaria. Se si etichetta uno di questi valori come entità basata su Machine Learning, LUIS impara a riconoscere gli altri valori. 

Un elenco di frasi può essere intercambiabile o non intercambiabile. Un elenco di frasi *intercambiabile* fa riferimento a valori che sono sinonimi, mentre un elenco di frasi *non intercambiabile* viene usato come elenco del vocabolario specifico di un'app. Man mano che le dimensioni dell'elenco di frasi del vocabolario dell'app aumentano, si potrebbe riscontrare che alcuni termini hanno molte forme (sinonimi). Suddividere questi casi in un altro elenco di frasi intercambiabile. 

|Tipo di elenco|Scopo|
|--|--|
|Intercambiabile|Sinonimi o parole che, quando usati al posto di un'altra parola nell'elenco hanno la stessa finalità ed estrazione di entità.|
|Non intercambiabile|Vocabolario dell'app, specifico per l'app più di altre parole in generale in tale lingua.|

Gli elenchi di frasi non solo sono utili per il rilevamento delle entità ma anche per la classificazione delle finalità nei casi in cui la caratteristica non intercambiabile è appropriata, come l'aggiunta di parole del vocabolario non note nella lingua inglese.


<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Gli elenchi di frasi aiutano a identificare le entità intercambiabili semplici
Gli elenchi di frasi intercambiabili sono un ottimo modo per ottimizzare le prestazioni della propria app LUIS. Se l'applicazione ha difficoltà a prevedere espressioni con le finalità corrette o a riconoscere le entità, valuta se le espressioni contengono o meno parole insolite o che potrebbero avere un significato ambiguo. Queste parole sono buoni candidati da includere in un elenco di frasi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Gli elenchi di frasi consentono di identificare le finalità grazie a una migliore comprensione del contesto
Un elenco di frasi non è un'istruzione impartita a LUIS perché quest’ultimo esegua una corrispondenza rigorosa o etichetti sempre tutti i termini nell'elenco di frasi allo stesso modo. È semplicemente un suggerimento. Un elenco di frasi potrebbe ad esempio indicare che "Patti" e "Selma" sono i nomi, ma LUIS riesce a usare le informazioni contestuali per appurare che il loro significato è diverso rispetto a "Prenota per 2 a cena presso Patti's Diner" e "Trovami il percorso in auto per Selma in Georgia". 

L'aggiunta di un elenco di frasi è un'alternativa all'aggiunta di ulteriori espressioni di esempio a una finalità. 

## <a name="an-interchangeable-phrase-list"></a>Elenco frasi intercambiabile
Usare un elenco di frasi intercambiabile quando l'elenco di parole o frasi crea una classe o un gruppo. Un esempio è un elenco di mesi, ad esempio "Gennaio", "Febbraio", "Marzo", o nomi, ad esempio "Giovanni", "Maria", "Franco".  Questi elenchi sono intercambiabili in quanto l'affermazione sarebbe etichettata con la stessa finalità o entità anche se venisse usata una parola diversa nell'elenco di frasi. Se ad esempio "mostra il calendario di gennaio" ha la stessa finalità di "mostra il calendario di febbraio", le parole sono incluse in un elenco intercambiabile. 

## <a name="a-non-interchangeable-phrase-list"></a>Elenco di frasi non intercambiabile
Usare un elenco di frasi non intercambiabile per parole o frasi che non sono sinonimi e che possono essere raggruppate nel dominio. 

Usare ad esempio un elenco di frasi non intercambiabile per le parole rare, brevettate e straniere. LUIS potrebbe non essere in grado di riconoscere le parole rare e brevettate, nonché le parole straniere (diverse rispetto alla impostazioni cultura dell'app). L'impostazione di non intercambiabilità indica che il set di parole rare forma una classe che LUIS deve imparare a riconoscere, ma che queste parole non sono sinonimi o intercambiabili tra loro.

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando usare gli elenchi di frasi e le entità elenco
Sia un elenco di frasi sia le entità elenco possono influire sulle espressioni di qualsiasi finalità, tuttavia le modalità con cui ciò avviene sono diverse. Usa un elenco di frasi per influire sul punteggio di previsione delle finalità. Usa un'entità elenco per influire sull'estrazione di entità per ottenere una corrispondenza testuale esatta. 

### <a name="use-a-phrase-list"></a>Usare un elenco di frasi
Con un elenco di frasi, LUIS riesce sempre tener conto del contesto e di generalizzare per identificare gli elementi che sono simili ma sono non una corrispondenza esatta, in quanto elementi in un elenco. Se è necessario che l'app LUIS generalizzi e identifichi i nuovi elementi in una categoria, usa un elenco di frasi. 

Quando desideri riconoscere le nuove istanze di un'entità, ad esempio un pianificatore di riunioni che riconosca i nomi dei nuovi contatti o un'app di inventario che riconosca i nuovi prodotti, usa un altro tipo di entità basato sulla Machine Learning, ad esempio un'entità semplice o gerarchica. Quindi, crea un elenco di frasi composto da parole e frasi che consenta a LUIS di trovare altre parole simili all'entità. Questo elenco guida LUIS attraverso il riconoscimento degli esempi di entità mediante l'aggiunta di ulteriore significato al valore di tali parole. 

Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità. Un uso comune di un elenco di frasi è quello dei nomi propri, ad esempio i nomi di città. Un nome di città può essere costituito da più parole, inclusi trattini o apostrofi.
 
### <a name="dont-use-a-phrase-list"></a>Non usare un elenco di frasi 
Un'entità elenco definisce in modo esplicito ogni valore che un'entità può richiedere e identifica solo i valori con corrispondenza esatta. Un'entità elenco potrebbe essere opportuna per un'app in cui tutte le istanze di un'entità sono note e non vengono modificate spesso, ad esempio le pietanze nel menu di un ristorante che vengono cambiate raramente. Se occorre disporre di una corrispondenza testuale esatta di un'entità, non usare un elenco di frasi. 

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.