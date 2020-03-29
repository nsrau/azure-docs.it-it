---
title: Procedure consigliate - QnA Maker
description: Usare queste procedure consigliate per migliorare la knowledge base e fornire risultati migliori per gli utenti finali dell'applicazione/chat bot.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053128"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Procedure consigliate per una knowledge base di QnA Maker

Il [ciclo di vita di sviluppo della knowledge base](../Concepts/development-lifecycle-knowledge-base.md) fornisce informazioni sulla gestione della KB dall'inizio alla fine. Usare queste procedure consigliate per migliorare la Knowledge Base e fornire risultati migliori agli utenti finali dell'applicazione client o del bot di chat.

## <a name="extraction"></a>Estrazione

Il servizio QnA Maker migliora continuamente gli algoritmi di estrazione di domande e risposte dal contenuto, espandendo l'elenco di file e formati di pagina HTML supportati. Seguire le [linee guida](../Concepts/content-types.md) per l'estrazione di dati in base al tipo di documento.

In generale, le pagine di domande frequenti devono essere autonome e non combinate con altre informazioni. I manuali di prodotti devono avere titoli chiari e preferibilmente una pagina di indice.

### <a name="configuring-multi-turn"></a>Configurazione di più giri

[Crea la tua knowledge base](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) con l'estrazione a più turni abilitata. Se la Knowledge Base supporta o deve supportare la gerarchia delle domande, questa gerarchia può essere estratta dal documento o creata dopo l'estrazione del documento.

## <a name="creating-good-questions-and-answers"></a>Creazione di domande e risposte di qualità

### <a name="good-questions"></a>Domande di qualità

Le domande migliori sono quelle semplici. Considerare la parola chiave o la frase per ogni domanda e quindi creare una domanda semplice basata su tale parola chiave o frase.

È possibile aggiungere tutte le domande alternative che si ritiene necessario, mantenendole però semplici. L'aggiunta di parole o frasi che non fanno parte dell'obiettivo principale della domanda non aiuta QnA Maker a trovare una corrispondenza.


### <a name="add-relevant-alternative-questions"></a>Aggiungere domande alternative pertinenti

L'utente può immettere domande con uno `How do I add a toner cartridge to my printer?` stile di testo `toner cartridge`conversazionale o una ricerca per parole chiave come . La Knowledge Base deve avere entrambi gli stili di domande per restituire correttamente la risposta migliore. Se non si è certi delle parole chiave immesse da un cliente, usare i dati di Application Insights per analizzare le query.

### <a name="good-answers"></a>Risposte di qualità

Le risposte migliori sono risposte semplici ma non troppo semplici. Non utilizzare risposte `yes` come `no`e . Se la risposta deve essere collegata ad altre origini o fornire un'esperienza completa con contenuti multimediali e `strictFilters` collegamenti, usa il [tagging dei metadati](../how-to/edit-knowledge-base.md#add-metadata) per distinguere tra le risposte, quindi invia la [query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) con i tag dei metadati nella proprietà per ottenere la versione corretta della risposta.

|Risposta|Prompt Follup-up|
|--|--|
|Spegnere il portatile Surface con il pulsante di accensione sulla tastiera.|- Combinazioni di tasti per dormire, spegnere e riavviare.<br>Come eseguire l'avvio hard-boot di un notebook Surface<br>Come modificare il BIOS per un computer portatile Surface<br>Differenze tra sospensione, arresto e riavvio|
|Il servizio clienti è disponibile tramite telefono, Skype e SMS 24 ore al giorno.|- Informazioni di contatto per le vendite.<br> - Uffici e negozi e ore per una visita di persona.<br> - Accessori per un computer portatile Surface.|

## <a name="chit-chat"></a>Chit Chat
Aggiungere una chit-chat al bot, per renderlo più colloquiale e accattivante con uno sforzo minimo. Puoi facilmente aggiungere set di dati di chit-chat da personalità predefinite durante la creazione della tua KB e modificarli in qualsiasi momento. Come [aggiungere chit-chat alla KB](../How-To/chit-chat-knowledge-base.md).

Chit-chat è supportato in [molte lingue](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Scelta della personalità
Chit-chat è supportato per diverse personalità predefinite:

|Personalità |File di dataset QnA Maker |
|---------|-----|
|Professionale |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Gentile |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Spiritoso |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Cura |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasta |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Le risposte possono andare da quelle formali a quelle informali e irriverenti. È consigliabile selezionare la personalità più allineata al tono desiderato per il bot. È possibile visualizzare [i set di dati](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)e sceglierne uno che funga da base per il bot e quindi personalizzare le risposte.

### <a name="edit-bot-specific-questions"></a>Modifica delle domande specifiche per i bot
Esistono alcune domande specifiche per i bot che fanno parte del set di dati chit-chat e a cui sono state associate risposte generiche. Modificare queste risposte per riflettere al meglio i dettagli del bot.

È consigliabile rendere le seguenti QnA di chit-chat più specifiche:

* informazioni sull'utente
* operazioni consentite
* Quanti anni hai?
* Chi ti ha creato?
* Ciao

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Aggiunta di chit-chat personalizzati con un tag di metadati

Se aggiungi le tue coppie QnA chit-chat, assicurati di aggiungere i metadati in modo che queste risposte vengano restituite. La coppia nome/valore `editorial:chitchat`dei metadati è .

## <a name="searching-for-answers"></a>Ricerca di risposte

L'API GenerateAnswer usa sia le domande che la risposta per cercare le risposte migliori alla query di un utente.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Ricerca di domande solo quando la risposta non è rilevante

Utilizzare [`RankerType=QuestionOnly`](#choosing-ranker-type) il se non si desidera cercare le risposte.

Un esempio di questo è quando la Knowledge Base è un catalogo di acronimi come domande con la loro forma completa come risposta. Il valore della risposta non aiuterà a cercare la risposta appropriata.

## <a name="rankingscoring"></a>Classificazione e assegnazione dei punteggi
Assicurarsi di usare al meglio le funzionalità di classificazione supportate da QnA Maker. In questo modo sarà possibile migliorare la probabilità che una specifica query utente riceva una risposta appropriata.

### <a name="choosing-a-threshold"></a>Scelta della soglia

Il [punteggio di confidenza](confidence-score.md) predefinito utilizzato come soglia è 0, tuttavia è possibile [modificare la soglia](confidence-score.md#set-threshold) per la knowledge base in base alle proprie esigenze. Poiché ogni KB è diversa, occorre testare e scegliere la soglia più adatta alla propria KB.

### <a name="choosing-ranker-type"></a>Scelta del tipo Ranker
Per impostazione predefinita, QnA Maker esegue ricerche tra domande e risposte. Se si desidera cercare solo le domande, per `RankerType=QuestionOnly` generare una risposta, utilizzare il nel corpo POST della richiesta GenerateAnswer.

### <a name="add-alternate-questions"></a>Aggiungere domande alternative
Le [domande alternative](../How-To/edit-knowledge-base.md) aumentano la probabilità di una corrispondenza con una query utente. Le domande alternative sono utili quando ci sono più modi per porre la stessa domanda. Ciò può includere modifiche alla struttura della frase e allo stile delle parole.

|Query originale|Query alternative|Modifica|
|--|--|--|
|È disponibile il parcheggio?|Si dispone di parcheggio?|struttura della frase|
 |Ciao|Ehi<br>Salve!|stile delle parole o gergo|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Usare i tag di metadati per filtrare domande e risposte

[I metadati](../How-To/edit-knowledge-base.md) aggiungono la possibilità per un'applicazione client di sapere che non deve accettare tutte le risposte, ma di restringere i risultati di una query utente in base ai tag di metadati. La risposta della knowledge base può essere diversa in base al tag di metadati, anche se la query è la stessa. Ad esempio, la domanda *"Dove si trova il parcheggio?"* può avere una risposta diversa se la posizione del ramo relativo al ristorante è diverso, ovvero se i metadati sono *Posizione: Seattle* anziché *Posizione: Redmond*.

### <a name="use-synonyms"></a>Usare sinonimi
Mentre è disponibile un certo supporto per i sinonimi nella lingua inglese, utilizzare le modifiche di parola senza distinzione tra maiuscole e minuscole tramite [l'API Alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) per aggiungere sinonimi alle parole chiave che assumono forma diversa. I sinonimi vengono aggiunti a livello di servizio QnA Maker e condivisi da tutte le knowledge base del servizio.

|Parola originale|Sinonimi|
|--|--|
|acquistare|acquisto<br>net-banking<br>servizi bancari NET|

### <a name="use-distinct-words-to-differentiate-questions"></a>Usare parole diverse per distinguere le domande
L'algoritmo di classificazione di QnA Maker, che mette in corrispondenza una query utente con una domanda nella knowledge base, funziona meglio se ogni domanda risponde a esigenze diverse. La ripetizione dello stesso set di parole tra domande riduce la probabilità di scelta della risposta corretta per una determinata query utente che usa tali parole.

Ad esempio, si potrebbero avere due QnA separate con le domande seguenti:

|QnA|
|--|
|dove si *trova* il parcheggio|
|dove si trova la *posizione* del bancomat|

Poiché queste due QnA sono formulate con parole molto simili, questa analogia potrebbe causare punteggi molto simili per molte query utente che hanno formula *"dove si `<x>` trova"*. Invece, cercare di distinguere chiaramente con query come *"dove si trova il parcheggio"* e *"dove è il bancomat",* evitando parole come "posizione" che potrebbero essere in molte domande nella vostra KB.

## <a name="collaborate"></a>Collaborare
QnA Maker permette agli utenti di [collaborare](../How-to/collaborate-knowledge-base.md) a una knowledge base. Per accedere alle knowledge base, gli utenti necessitano dell'accesso al gruppo di risorse di QnA Maker in Azure. Alcune organizzazioni potrebbero voler assegnare all'esterno le attività di modifica e manutenzione della knowledge base, mantenendo comunque la possibilità di proteggere l'accesso alle risorse di Azure. Questo modello di approvazione dell'editor di testo può essere ottenuto configurando due [servizi QnA Maker](../How-to/set-up-qnamaker-service-azure.md) identici in diverse sottoscrizioni e se si seleziona uno per il ciclo di test di modifica. Una volta completati i test, il contenuto della knowledge base può essere trasferito con un processo di [importazione-esportazione](../Tutorials/migrate-knowledge-base.md) al servizio QnA Maker del responsabile approvazione che infine pubblicherà la knowledge base e aggiornerà l'endpoint.



## <a name="active-learning"></a>Apprendimento attivo

[Apprendimento attivo](../How-to/use-active-learning.md) garantisce migliori prestazioni nel suggerimento di domande alternative se è disponibile un'ampia gamma, in termini di qualità e quantità, di query basate sull'utente. È importante consentire alle query degli utenti delle applicazioni client di entrare nel ciclo di feedback relativo all'apprendimento attivo senza alcuna limitazione. Una volta suggerite le domande nel portale QnA Maker, è possibile filtrare in **[base ai suggerimenti](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** e quindi rivedere e accettare o rifiutare tali suggerimenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-to/edit-knowledge-base.md)
