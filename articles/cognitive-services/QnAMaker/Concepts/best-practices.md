---
title: Procedure consigliate - QnA Maker
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: bbd6c55337eb30c883ecbd542abbdfc0f842e3f9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563117"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Procedure consigliate per una knowledge base di QnA Maker

Il [ciclo di vita di sviluppo della knowledge base](../Concepts/development-lifecycle-knowledge-base.md) fornisce informazioni sulla gestione della KB dall'inizio alla fine. Usare queste procedure consigliate per migliorare la Knowledge base e ottenere risultati migliori per l'applicazione client o per gli utenti finali di chat bot.

## <a name="extraction"></a>Estrazione

Il servizio QnA Maker migliora continuamente gli algoritmi di estrazione di domande e risposte dal contenuto, espandendo l'elenco di file e formati di pagina HTML supportati. Seguire le [linee guida](../Concepts/data-sources-supported.md) per l'estrazione di dati in base al tipo di documento. 

In generale, le pagine di domande frequenti devono essere autonome e non combinate con altre informazioni. I manuali di prodotti devono avere titoli chiari e preferibilmente una pagina di indice. 

### <a name="configuring-multi-turn"></a>Configurazione della multifunzione

Crea la Knowledge base con l'estrazione a più turni abilitata. Se la Knowledge base è o deve supportare la gerarchia delle domande, questa gerarchia può essere estratta dal documento o creata dopo l'estrazione del documento. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Creazione di domande e risposte di qualità

### <a name="good-questions"></a>Domande di qualità

Le domande migliori sono quelle semplici. Considerare la parola chiave o la frase per ogni domanda e quindi creare una domanda semplice basata su tale parola chiave o frase. 

È possibile aggiungere tutte le domande alternative che si ritiene necessario, mantenendole però semplici. L'aggiunta di parole o frasi che non fanno parte dell'obiettivo principale della domanda non aiuta QnA Maker a trovare una corrispondenza. 


### <a name="add-relevant-alternative-questions"></a>Aggiungere domande alternative rilevanti

È possibile che `toner cartridge`l'utente entri in una domanda con stile di testo `How do I add a toner cartridge to my printer?` colloquiale o con una ricerca di parole chiave, ad esempio. La Knowledge base deve disporre di entrambi gli stili di domande per restituire correttamente la risposta migliore. Se non si è certi delle parole chiave che un cliente sta immettendo, usare Application Insights dati per analizzare le query.

### <a name="good-answers"></a>Risposte di qualità

Le risposte migliori sono semplici risposte ma non troppo semplici. Non usare risposte quali `yes` e. `no` Se la risposta deve essere collegata ad altre origini o fornire un'esperienza avanzata con i supporti e i collegamenti, usare l'assegnazione di tag [dei metadati](./knowledge-base.md#key-knowledge-base-concepts) per distinguere le risposte, quindi [inviare la query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) con i `strictFilters` tag dei metadati nella proprietà per ottenere la risposta corretta Versione.

## <a name="chit-chat"></a>Chit Chat
Aggiungere una chit-chat al bot, per renderlo più colloquiale e accattivante con uno sforzo minimo. È possibile aggiungere facilmente set di dati Chit-Chat da personali predefinite durante la creazione della Knowledge base e modificarli in qualsiasi momento. Come [aggiungere chit-chat alla KB](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Scelta della personalità
Chit-Chat è supportata per diverse personali predefinite: 

|Personalità |QnA Maker file di set di dati |
|---------|-----|
|Professionale |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Gentile |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Spiritoso |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Occuparsi |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasta |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Le risposte possono andare da quelle formali a quelle informali e irriverenti. È consigliabile selezionare la personalità più allineata al tono desiderato per il bot. È possibile visualizzare i [set di impostazioni](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)e sceglierne uno che funge da base per il bot, quindi personalizzare le risposte. 

### <a name="edit-bot-specific-questions"></a>Modifica delle domande specifiche per i bot
Esistono alcune domande specifiche per i bot che fanno parte del set di dati chit-chat e a cui sono state associate risposte generiche. Modificare queste risposte per riflettere al meglio i dettagli del bot. 

È consigliabile rendere le seguenti QnA di chit-chat più specifiche:

* Identificarsi
* Cosa fare
* Quanti anni hai?
* Chi ti ha creato?
* Salve
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Aggiunta di una Chit-Chat personalizzata con un tag di metadati

Se si aggiungono le proprie coppie di QnA Chit-Chat, assicurarsi di aggiungere metadati in modo che vengano restituite le risposte. La coppia nome/valore dei metadati `editorial:chitchat`è.

## <a name="searching-for-answers"></a>Ricerca di risposte

L'API GenerateAnswer usa entrambe le domande e la risposta per cercare le risposte migliori alla query di un utente.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Ricerca di domande solo quando la risposta non è pertinente

Utilizzare se [`RankerType=QuestionOnly`](#choosing-ranker-type) non si desidera cercare le risposte. 

Un esempio si verifica quando la Knowledge base è un catalogo di acronimi come domande con la forma completa come risposta. Il valore della risposta non consente di cercare la risposta appropriata.

## <a name="rankingscoring"></a>Classificazione e assegnazione dei punteggi
Assicurarsi di usare al meglio le funzionalità di classificazione supportate da QnA Maker. In questo modo sarà possibile migliorare la probabilità che una specifica query utente riceva una risposta appropriata.

### <a name="choosing-a-threshold"></a>Scelta della soglia

Il [Punteggio](confidence-score.md) di confidenza predefinito usato come soglia è 50, ma è possibile [modificare la soglia](confidence-score.md#set-threshold) per la KB in base alle esigenze. Poiché ogni KB è diversa, occorre testare e scegliere la soglia più adatta alla propria KB. 

### <a name="choosing-ranker-type"></a>Scelta del tipo di rango
Per impostazione predefinita, QnA Maker cerca le domande e le risposte. Se si desidera cercare solo le `RankerType=QuestionOnly` domande, per generare una risposta, utilizzare nel corpo post della richiesta GenerateAnswer.

### <a name="add-alternate-questions"></a>Aggiungere domande alternative
Le [domande alternative](../How-To/edit-knowledge-base.md) aumentano la probabilità di una corrispondenza con una query utente. Le domande alternative sono utili quando ci sono più modi per porre la stessa domanda. Ciò può includere modifiche alla struttura della frase e allo stile delle parole.

|Query originale|Query alternative|Cambia| 
|--|--|--|
|È disponibile il parcheggio?|Si dispone di parcheggio?|struttura della frase|
 |Ciao|Ehi<br>Salve!|stile delle parole o gergo|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Usare i tag di metadati per filtrare domande e risposte

I [metadati](../How-To/edit-knowledge-base.md) aggiungono la possibilità che un'applicazione client sappia che non deve rispondere a tutte le risposte, bensì per limitare i risultati di una query utente basata sui tag dei metadati. La risposta della knowledge base può essere diversa in base al tag di metadati, anche se la query è la stessa. Ad esempio, la domanda *"Dove si trova il parcheggio?"* può avere risposte diverse se l'attributo Location del ramo relativo al ristorante è diverso, ovvero se i metadati sono *Location: Seattle* piuttosto che *Location: Redmond*.

### <a name="use-synonyms"></a>Usare sinonimi
Sebbene esista un supporto per i sinonimi nella lingua inglese, usare le modifiche di parola senza distinzione tra maiuscole e minuscole tramite l' [API Alters](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) per aggiungere sinonimi a parole chiave che accettano forme diverse. I sinonimi vengono aggiunti a livello di servizio QnA Maker e condivisi da tutte le Knowledge base nel servizio.

|Parola originale|Sinonimi|
|--|--|
|acquistare|acquistare<br>NET-Banking<br>servizi bancari NET|

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

[Apprendimento attivo](../How-to/improve-knowledge-base.md) garantisce migliori prestazioni nel suggerimento di domande alternative se è disponibile un'ampia gamma, in termini di qualità e quantità, di query basate sull'utente. È importante consentire alle query degli utenti delle applicazioni client di entrare nel ciclo di feedback relativo all'apprendimento attivo senza alcuna limitazione. Quando le domande sono consigliate nel portale di QnA Maker, è possibile **[filtrare in base ai suggerimenti](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , quindi esaminare e accettare o rifiutare tali suggerimenti. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-to/edit-knowledge-base.md)
