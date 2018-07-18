---
title: Comprensione delle funzionalità delle applicazioni LUIS in Azure | Microsoft Docs
description: Informazioni sulle funzionalità che consentono di migliorare le prestazioni dell'app LUIS. Tali funzionalità includono elenchi di frasi e criteri per il riconoscimento di espressioni regolari.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35379073"
---
# <a name="phrase-list-features-in-luis"></a>Funzionalità dell'elenco di frasi in LUIS

Nell'apprendimento automatico, una *funzionalità* è un tratto distintivo o un attributo di dati rilevato dal sistema. 

L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare. Le funzionalità consentono a LUIS di riconoscere sia le finalità sia le entità, tuttavia le funzionalità non sono le finalità o le entità. Al contrario, le funzionalità potrebbero fornire esempi di termini correlati.  

## <a name="what-is-a-phrase-list-feature"></a>Che cos'è una funzionalità di un elenco di frasi?
Un elenco di frasi comprende un gruppo di valori (parole o frasi) che appartengono alla stessa classe e che devono essere trattati in modo analogo (ad esempio, i nomi di città o prodotti). Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. Non si tratta di un'[entità elenco](luis-concept-entity-types.md#types-of-entities) chiuso (corrispondenze testuali esatte) di parole con corrispondenze.

Viene integrato un elenco di frasi al vocabolario del dominio dell'applicazione come secondo segnale di LUIS in merito a tali parole.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi
In un'applicazione per agenzie di viaggi, crea un elenco di frasi denominato "Città" che contiene i valori Londra, Parigi e Cairo. Se etichetti uno di questi valori come entità semplice in un'[espressione di esempio](luis-how-to-add-example-utterances.md#add-simple-entity-label) di una finalità, LUIS imparerà a riconoscerne gli altri. 

Un elenco di frasi può essere intercambiabile o non intercambiabile. Un elenco di frasi *intercambiabile* fa riferimento a valori che risultano essere sinonimi, mentre uno *non intercambiabile* viene usato per i valori che non risultano sinonimi, ma sono in altro modo simili. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Gli elenchi di frasi consentono di identificare semplici entità intercambiabili
Gli elenchi di frasi intercambiabili sono un ottimo modo per ottimizzare le prestazioni dell'app LUIS. Se l'applicazione ha difficoltà a prevedere espressioni con le finalità corrette o a riconoscere le entità, valuta se le espressioni contengono o meno parole insolite o che potrebbero avere un significato ambiguo. Queste parole sono buoni candidati da includere in un elenco di frasi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Gli elenchi di frasi consentono di identificare le finalità grazie a una migliore comprensione del contesto
Usa gli elenchi di frasi per parole rare, proprietarie e straniere. LUIS potrebbe non essere in grado di riconoscere parole rare e proprietarie, nonché parole straniere (diverse rispetto alla impostazioni cultura dell'app) e pertanto queste dovranno essere aggiunte a un elenco di frasi. Tale elenco deve essere contrassegnato come non intercambiabile per indicare che il set di parole rare costituisce una classe che LUIS dovrebbe imparare a riconoscere, ma tali parole non sono sinonimi o non sono intercambiabili tra loro.

Un elenco di frasi non è un'istruzione impartita a LUIS perché quest’ultimo esegua una corrispondenza rigorosa o etichetti sempre tutti i termini nell'elenco di frasi allo stesso modo. È semplicemente un suggerimento. Ad esempio, un elenco di frasi potrebbe indicare che "Patti" e "Selma" sono i nomi, ma LUIS riesce ancora usare le informazioni contestuali per appurare che il loro significato è diverso rispetto a "Prenota per 2 a cena presso Patti's Diner" e "Trovami il percorso in auto per Selma in Georgia". 

L'aggiunta di un elenco di frasi è un'alternativa all'aggiunta di ulteriori espressioni di esempio a una finalità. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando usare gli elenchi di frasi e le entità elenco
Sia un elenco di frasi sia le entità elenco possono influire sulle espressioni di qualsiasi finalità, tuttavia le modalità con cui ciò avviene sono diverse. Usa un elenco di frasi per influire sul punteggio di previsione delle finalità. Usa un'entità elenco per influire sull'estrazione di entità per ottenere una corrispondenza testuale esatta. 

### <a name="use-a-phrase-list"></a>Usare un elenco di frasi
Con un elenco di frasi, LUIS riesce sempre tener conto del contesto e di generalizzare per identificare gli elementi che sono simili ma sono non una corrispondenza esatta, in quanto elementi in un elenco. Se è necessario che l'app LUIS generalizzi e identifichi i nuovi elementi in una categoria, usa un elenco di frasi. 

Quando desideri riconoscere le nuove istanze di un'entità, ad esempio un pianificatore di riunioni che riconosca i nomi dei nuovi contatti o un'app di inventario che riconosca i nuovi prodotti, usa un altro tipo di entità basato sulla Machine Learning, ad esempio un'entità semplice o gerarchica. Quindi, crea un elenco di frasi composto da parole e frasi che consenta a LUIS di trovare altre parole simili all'entità. Questo elenco guida LUIS attraverso il riconoscimento degli esempi di entità mediante l'aggiunta di ulteriore significato al valore di tali parole. 

Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità. Un uso comune di un elenco di frasi è quello dei nomi propri, ad esempio i nomi di città. Un nome di città può essere costituito da più parole, inclusi trattini o apostrofi.
 
### <a name="dont-use-a-phrase-list"></a>Non usare un elenco di frasi 
Un'entità elenco definisce in modo esplicito ogni valore che un'entità può richiedere e identifica solo i valori con corrispondenza esatta. Un'entità elenco potrebbe essere appropriata per un'app in cui sono note tutte le istanze di un'entità e che non vengono modificate spesso, ad esempio alimenti in un menu di un ristorante che vengono modificati raramente. Se occorre disporre di una corrispondenza testuale esatta di un'entità, non usare un elenco di frasi. 

## <a name="best-practices"></a>Procedure consigliate
Informazioni sulle [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.