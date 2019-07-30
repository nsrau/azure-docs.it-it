---
title: Funzionalità-LUIS
titleSuffix: Azure Cognitive Services
description: L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e85f9a90af778a7e68bf761d8d2159e808cf122d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639203"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funzionalità di elenco di frasi nell'app LUIS

Nell'apprendimento automatico, una *funzionalità* è un tratto distintivo o un attributo di dati rilevato dal sistema. 

L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare. Le funzionalità consentono a LUIS di riconoscere sia le finalità sia le entità, tuttavia le funzionalità non sono le finalità o le entità. Al contrario, le funzionalità potrebbero fornire esempi di termini correlati.  

## <a name="what-is-a-phrase-list-feature"></a>Che cos'è una funzionalità di un elenco di frasi?
Un elenco di frasi è un elenco di parole o frasi che sono significative per l'app, più di altre parole nelle espressioni. Un elenco di frasi viene aggiunto al vocabolario del dominio dell'app come segnale aggiuntivo per LUIS in merito a tali parole. Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. L'elenco non è un'[entità elenco](luis-concept-entity-types.md#types-of-entities) chiusa di corrispondenze di testo esatte.

Gli elenchi di frasi non sono utili per lo stemming, quindi è necessario aggiungere esempi di espressioni che usano un'ampia gamma di stemming per tutte le parole e le frasi significative del vocabolario.

## <a name="phrase-lists-help-all-models"></a>Gli elenchi di frasi sono utili per tutti i modelli

Gli elenchi di frasi non sono collegati a una finalità o a un'entità specifica, ma vengono aggiunti come strumento di ottimizzazione per tutte le finalità e tutte le entità. Il loro scopo è migliorare il rilevamento delle finalità e la classificazione delle entità.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi

[Creare un](luis-how-to-add-features.md) elenco di frasi quando l'app contiene parole o frasi importanti per l'app, ad esempio:

* termini di settore
* gergo
* abbreviazioni
* linguaggio specifico di un'azienda
* linguaggio derivato da un'altra lingua, ma usato spesso nell'app
* parole chiave o frasi nelle espressioni di esempio

Dopo aver immesso alcuni parole o frasi, usare la caratteristica **Recommend** (Consiglia) per trovare i valori correlati. Verificare i valori correlati prima di aggiungerli ai valori dell'elenco di frasi.

|Tipo elenco|Scopo|
|--|--|
|Intercambiabile|Sinonimi o parole che, quando usati al posto di un'altra parola nell'elenco hanno la stessa finalità ed estrazione di entità.|
|Non intercambiabile|Vocabolario dell'app, specifico per l'app più di altre parole in generale in tale lingua.|

### <a name="interchangeable-lists"></a>Elenchi intercambiabili

Un elenco di frasi *intercambiabile* è progettato per valori che sono sinonimi. Ad esempio, se si vogliono trovare tutti i corpi d'acqua e sono disponibili espressioni di esempio come le seguenti: 

* Quali città sono vicine ai Grandi Laghi? 
* Quale strada costeggia il Lago di Como?
* Dove inizia e finisce il Nilo? 

Ogni espressione deve essere determinata sia per la finalità che per le entità indipendentemente dal corpo d'acqua: 

* Quali città sono vicine a [corpoDiAcqua]?
* Quale strada costeggia [corpoDiAcqua]?
* Dove inizia e finisce [corpoDiAcqua]? 

Dal momento che le parole o le frasi per il corpo d'acqua sono sinonimi e possono essere usate in modo intercambiabile nelle espressioni, usare l'impostazione **intercambiabile** nell'elenco di frasi. 

### <a name="non-interchangeable-lists"></a>Elenchi non intercambiabili

Un elenco di frasi non intercambiabile è un segnale che potenzia il rilevamento per LUIS. L'elenco di frasi indica parole o frasi che sono più significative rispetto alle altre parole. Ciò è utile sia per la determinazione della finalità che per il rilevamento delle entità. Ad esempio, si supponga di usare il dominio di interesse Viaggi globale, ovvero valido per più impostazioni cultura, ma comunque in una sola lingua. Sono presenti parole e frasi che sono importanti per l'app, ma non sono sinonimi. 

Sempre a titolo di esempio, si potrebbe usare un elenco di frasi non intercambiabile anche per le parole rare, brevettate e straniere. LUIS potrebbe non essere in grado di riconoscere le parole rare e brevettate, nonché le parole straniere (diverse rispetto alla impostazioni cultura dell'app). L'impostazione di non intercambiabilità indica che il set di parole rare forma una classe che LUIS deve imparare a riconoscere, ma che queste parole non sono sinonimi o intercambiabili tra loro.

Non aggiungere ogni possibile parola o frase all'elenco di frasi, ma aggiungere alcune parole o frasi alla volta e quindi ripetere il training e pubblicare. 

Man mano che le dimensioni dell'elenco di frasi aumentano nel tempo, si potrebbe riscontrare che alcuni termini hanno molte forme (sinonimi). Suddividere questi casi in un altro elenco di frasi intercambiabile. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Gli elenchi di frasi consentono di identificare semplici entità intercambiabili
Gli elenchi di frasi intercambiabili sono un ottimo modo per ottimizzare le prestazioni della propria app LUIS. Se l'applicazione ha difficoltà a prevedere espressioni con le finalità corrette o a riconoscere le entità, valuta se le espressioni contengono o meno parole insolite o che potrebbero avere un significato ambiguo. Queste parole sono buoni candidati da includere in un elenco di frasi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Gli elenchi di frasi consentono di identificare le finalità grazie a una migliore comprensione del contesto
Un elenco di frasi non è un'istruzione impartita a LUIS perché quest’ultimo esegua una corrispondenza rigorosa o etichetti sempre tutti i termini nell'elenco di frasi allo stesso modo. È semplicemente un suggerimento. Un elenco di frasi potrebbe ad esempio indicare che "Patti" e "Selma" sono i nomi, ma LUIS riesce a usare le informazioni contestuali per appurare che il loro significato è diverso rispetto a "Prenota per 2 a cena presso Patti's Diner" e "Trovami il percorso in auto per Selma in Georgia". 

L'aggiunta di un elenco di frasi è un'alternativa all'aggiunta di ulteriori espressioni di esempio a una finalità. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando usare gli elenchi di frasi e le entità elenco
Sebbene sia un elenco di frasi che un [elenco di entità](reference-entity-list.md) possono influenzare le espressioni in tutti gli Intent, ognuno esegue questa operazione in modo diverso. Usa un elenco di frasi per influire sul punteggio di previsione delle finalità. Usa un'entità elenco per influire sull'estrazione di entità per ottenere una corrispondenza testuale esatta. 

### <a name="use-a-phrase-list"></a>Usare un elenco di frasi
Con un elenco di frasi, LUIS riesce sempre tener conto del contesto e di generalizzare per identificare gli elementi che sono simili ma sono non una corrispondenza esatta, in quanto elementi in un elenco. Se è necessario che l'app LUIS generalizzi e identifichi i nuovi elementi in una categoria, usa un elenco di frasi. 

Quando si vuole essere in grado di riconoscere nuove istanze di un'entità, ad esempio un'utilità di pianificazione della riunione che deve riconoscere i nomi dei nuovi contatti o un'app di inventario che dovrebbe riconoscere nuovi prodotti, usare un altro tipo di entità appresa dal computer, ad esempio un'entità semplice. Quindi, crea un elenco di frasi composto da parole e frasi che consenta a LUIS di trovare altre parole simili all'entità. Questo elenco guida LUIS attraverso il riconoscimento degli esempi di entità mediante l'aggiunta di ulteriore significato al valore di tali parole. 

Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità. Un uso comune di un elenco di frasi è quello dei nomi propri, ad esempio i nomi di città. Un nome di città può essere costituito da più parole, inclusi trattini o apostrofi.
 
### <a name="dont-use-a-phrase-list"></a>Non usare un elenco di frasi 
Un'entità elenco definisce in modo esplicito ogni valore che un'entità può richiedere e identifica solo i valori con corrispondenza esatta. Un'entità elenco potrebbe essere opportuna per un'app in cui tutte le istanze di un'entità sono note e non vengono modificate spesso, ad esempio le pietanze nel menu di un ristorante che vengono cambiate raramente. Se occorre disporre di una corrispondenza testuale esatta di un'entità, non usare un elenco di frasi. 

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.
