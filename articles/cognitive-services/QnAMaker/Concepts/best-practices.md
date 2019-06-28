---
title: Procedure consigliate - QnA Maker
titlesuffix: Azure Cognitive Services
description: Usare queste procedure consigliate per migliorare la knowledge base e fornire risultati migliori per gli utenti finali dell'applicazione/chat bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c796114d124c64ac1c373baacabe00c7dcd70aa7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447640"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Procedure consigliate per una knowledge base di QnA Maker

Il [ciclo di vita di sviluppo della knowledge base](../Concepts/development-lifecycle-knowledge-base.md) fornisce informazioni sulla gestione della KB dall'inizio alla fine. Usare queste procedure consigliate per migliorare la tua knowledge base e fornire risultati migliori per l'applicazione client o avvia una chat agli utenti finali del bot.

## <a name="extraction"></a>Estrazione

Il servizio QnA Maker migliora continuamente gli algoritmi di estrazione di domande e risposte dal contenuto, espandendo l'elenco di file e formati di pagina HTML supportati. Seguire le [linee guida](../Concepts/data-sources-supported.md) per l'estrazione di dati in base al tipo di documento. 

In generale, le pagine di domande frequenti devono essere autonome e non combinate con altre informazioni. I manuali di prodotti devono avere titoli chiari e preferibilmente una pagina di indice. 

### <a name="configuring-multi-turn"></a>Configurazione di multi-attiva

Creare la knowledge base con multi-turni abilitata l'estrazione. Se la knowledge base o deve supportare la gerarchia di domanda, questa gerarchia può essere estratto dal documento o creata dopo che il documento viene estratto. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Creazione di domande e risposte di qualità

### <a name="good-questions"></a>Domande di qualità

Le domande migliori sono quelle semplici. Considerare la parola chiave o la frase per ogni domanda e quindi creare una domanda semplice basata su tale parola chiave o frase. 

È possibile aggiungere tutte le domande alternative che si ritiene necessario, mantenendole però semplici. L'aggiunta di parole o frasi che non fanno parte dell'obiettivo principale della domanda non aiuta QnA Maker a trovare una corrispondenza. 


### <a name="add-relevant-alternative-questions"></a>Aggiungere rilevanti domande alternative

L'utente può immettere domande con uno stile colloquiale di testo `How do I add a toner cartridge to my printer?` o una parola chiave di ricerca, ad esempio `toner cartridge`. La knowledge base deve avere entrambi gli stili di domande per non restituire la risposta migliore in modo corretto. Se non si è certi di quali un cliente sta immettendo parole chiave, usare i dati di Application Insights per analizzare le query.

### <a name="good-answers"></a>Risposte di qualità

Le risposte migliori vengono fornite le risposte semplice ma non troppo semplice. Non usare, ad esempio le risposte `yes` e `no`. Se la risposta deve collegare ad altre origini o fornire un'esperienza avanzata con media e collegamenti, usare [l'assegnazione di tag di metadati](./knowledge-base.md#key-knowledge-base-concepts) per distinguere tra le risposte, quindi [inviare la query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) con tag di metadati nel `strictFilters` proprietà da ottenere la versione di risposta è corretta.

## <a name="chit-chat"></a>Chit Chat
Aggiungere una chit-chat al bot, per renderlo più colloquiale e accattivante con uno sforzo minimo. È possibile facilmente aggiungere set di dati di una chat chit dalla personalità predefiniti quando si crea la KB e modificarle in qualsiasi momento. Come [aggiungere chit-chat alla KB](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Scelta della personalità
Chit chat è supportata per diversi personalità predefiniti: 

|Personalità |File di set di dati di QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Gentile |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Dinamica, in gamba |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Necessario |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Fornendo informazioni |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Le risposte possono andare da quelle formali a quelle informali e irriverenti. È consigliabile selezionare la personalità più allineata al tono desiderato per il bot. È possibile visualizzare il [set di dati](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), sceglierne una che serve come base per il bot e quindi personalizzare le risposte. 

### <a name="edit-bot-specific-questions"></a>Modifica delle domande specifiche per i bot
Esistono alcune domande specifiche per i bot che fanno parte del set di dati chit-chat e a cui sono state associate risposte generiche. Modificare queste risposte per riflettere al meglio i dettagli del bot. 

È consigliabile rendere le seguenti QnA di chit-chat più specifiche:

* Chi si sta autenticando?
* Cosa fare
* Quanti anni hai?
* Chi ti ha creato?
* Ciao
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Aggiunta di una chat chit personalizzata con un tag di metadati

Se si aggiungono il proprio coppie di domande e risposte chit chat, assicurarsi di aggiungere i metadati in modo che queste risposte vengono restituite. La coppia nome/valore dei metadati è `editorial:chitchat`.

## <a name="searching-for-answers"></a>Ricerca delle risposte

API GenerateAnswer Usa domande sia la risposta per cercare le risposte alle query dell'utente.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>La ricerca di domande solo quando risposte non sono rilevante

Usare la [ `RankerType=QuestionOnly` ](#choosing-ranker-type) se non si desidera cercare le risposte. 

Un esempio è quando la knowledge base è un catalogo di acronimi come domande con relativa forma completa come la risposta. Il valore della risposta non consente di cercare la risposta appropriata.

## <a name="rankingscoring"></a>Classificazione e assegnazione dei punteggi
Assicurarsi di usare al meglio le funzionalità di classificazione supportate da QnA Maker. In questo modo sarà possibile migliorare la probabilità che una specifica query utente riceva una risposta appropriata.

### <a name="choosing-a-threshold"></a>Scelta della soglia

Il valore predefinito [punteggio di confidenza](confidence-score.md#) che viene usato come una soglia è 50, ma è possibile [modificare la soglia](confidence-score.md#set-threshold) per la Knowledge base in base alle esigenze. Poiché ogni KB è diversa, occorre testare e scegliere la soglia più adatta alla propria KB. 

### <a name="choosing-ranker-type"></a>Scelta del tipo permettendoti
Per impostazione predefinita, QnA Maker di Cerca con domande e risposte. Se si desidera cercare solo le domande, per generare una risposta, utilizzare il `RankerType=QuestionOnly` nel corpo del POST della richiesta GenerateAnswer.

### <a name="add-alternate-questions"></a>Aggiungere domande alternative
Le [domande alternative](../How-To/edit-knowledge-base.md) aumentano la probabilità di una corrispondenza con una query utente. Le domande alternative sono utili quando ci sono più modi per porre la stessa domanda. Ciò può includere modifiche alla struttura della frase e allo stile delle parole.

|Query originale|Query alternative|Modifica| 
|--|--|--|
|È disponibile il parcheggio?|Si dispone di parcheggio?|struttura della frase|
 |Ciao|Ehi<br>Salve!|stile delle parole o gergo|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Usare i tag di metadati per filtrare domande e risposte

[Metadati](../How-To/edit-knowledge-base.md) aggiunge la possibilità per un'applicazione client per sapere non deve accettare tutte le risposte, bensì per restringere i risultati di una query utente in base a tag di metadati. La risposta della knowledge base può essere diversa in base al tag di metadati, anche se la query è la stessa. Ad esempio, la domanda *"Dove si trova il parcheggio?"* può avere risposte diverse se l'attributo Location del ramo relativo al ristorante è diverso, ovvero se i metadati sono *Location: Seattle* piuttosto che *Location: Redmond*.

### <a name="use-synonyms"></a>Usare sinonimi
Nonostante alcune funzionalità di supporto per i sinonimi in lingua inglese, usare alterazioni tra maiuscole e minuscole word tramite il [alterazioni API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) aggiungere sinonimi alle parole chiave che hanno un formato diverso. I sinonimi vengono aggiunti a livello di servizio QnA Maker e condivise da tutte le knowledge base nel servizio.

|Parola originale|Sinonimi|
|--|--|
|acquistare|acquisto<br>NET-settore bancario<br>servizi bancari NET|

### <a name="use-distinct-words-to-differentiate-questions"></a>Usare parole diverse per distinguere le domande
L'algoritmo di classificazione di QnA Maker, che mette in corrispondenza una query utente con una domanda nella knowledge base, funziona meglio se ogni domanda risponde a esigenze diverse. La ripetizione dello stesso set di parole tra domande riduce la probabilità di scelta della risposta corretta per una determinata query utente che usa tali parole. 

Ad esempio, si potrebbero avere due QnA separate con le domande seguenti:

|QnA|
|--|
|dove si *trova* il parcheggio|
|dove si *trova* il bancomat|

Poiché queste due QnA sono formulate con parole molto simili, questa analogia potrebbe causare punteggi molto simili per molte query utente che hanno formula *"dove si `<x>` trova"* . Provare invece a differenziare chiaramente con query quali *"dov'è il parcheggio"* e *"dov'è il bancomat"* , evitando parole come "trova" che potrebbe ricorrere in molte domande nella KB. 

## <a name="collaborate"></a>Collaborare
QnA Maker permette agli utenti di [collaborare](../How-to/collaborate-knowledge-base.md) a una knowledge base. Per accedere alle knowledge base, gli utenti necessitano dell'accesso al gruppo di risorse di QnA Maker in Azure. Alcune organizzazioni potrebbero voler assegnare all'esterno le attività di modifica e manutenzione della knowledge base, mantenendo comunque la possibilità di proteggere l'accesso alle risorse di Azure. Questo modello di approvazione dell'editor di testo può essere ottenuto configurando due [servizi QnA Maker](../How-to/set-up-qnamaker-service-azure.md) identici in diverse sottoscrizioni e se si seleziona uno per il ciclo di test di modifica. Una volta completati i test, il contenuto della knowledge base può essere trasferito con un processo di [importazione-esportazione](../Tutorials/migrate-knowledge-base.md) al servizio QnA Maker del responsabile approvazione che infine pubblicherà la knowledge base e aggiornerà l'endpoint.



## <a name="active-learning"></a>Apprendimento attivo

[Apprendimento attivo](../How-to/improve-knowledge-base.md) garantisce migliori prestazioni nel suggerimento di domande alternative se è disponibile un'ampia gamma, in termini di qualità e quantità, di query basate sull'utente. È importante consentire alle query degli utenti delle applicazioni client di entrare nel ciclo di feedback relativo all'apprendimento attivo senza alcuna limitazione. Una volta domande vengono suggerite nel portale di QnA Maker, puoi **[Filtra in base a suggerimenti](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** quindi esaminare e accettare o rifiutare tali suggerimenti. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-to/edit-knowledge-base.md)
