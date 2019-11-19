---
title: Errori e avvisi dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Questo articolo fornisce informazioni e soluzioni per gli errori e gli avvisi comuni che possono verificarsi durante l'arricchimento di intelligenza artificiale in Azure ricerca cognitiva.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6b51581b5a8f94419dba60eee72669a3e1261b24
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151584"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Risoluzione di errori e avvisi comuni dell'indicizzatore in Azure ricerca cognitiva

Questo articolo fornisce informazioni e soluzioni per gli errori e gli avvisi comuni che possono verificarsi durante l'indicizzazione e l'arricchimento di intelligenza artificiale in Azure ricerca cognitiva.

L'indicizzazione viene arrestata quando il numero di errori supera [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se si vuole che gli indicizzatori ignorino questi errori (e ignorino i "documenti non riusciti"), provare ad aggiornare il `maxFailedItems` e `maxFailedItemsPerBatch` come descritto [qui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Ogni documento con errori insieme alla relativa chiave del documento (se disponibile) verrà visualizzato come errore nello stato di esecuzione dell'indicizzatore. È possibile utilizzare l' [API index](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per caricare manualmente i documenti in un secondo momento se l'indicizzatore è stato impostato in modo da tollerare gli errori.

Le informazioni sull'errore in questo articolo possono essere utili per risolvere gli errori, consentendo la continuazione dell'indicizzazione.

Gli avvisi non interrompono l'indicizzazione, ma indicano condizioni che potrebbero causare risultati imprevisti. Il fatto di intervenire o meno dipende dai dati e dallo scenario.

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Errore: non è stato possibile leggere il documento

L'indicizzatore non è stato in grado di leggere il documento dall'origine dati. Questo problema può verificarsi a causa di:

| Motivo | Dettagli/esempio | Risoluzione |
| --- | --- | --- |
| tipi di campi incoerenti tra documenti diversi | Il tipo di valore non corrisponde al tipo di colonna. Impossibile archiviare `'{47.6,-122.1}'` nella colonna autori.  Il tipo previsto è JArray. | Verificare che il tipo di ogni campo sia lo stesso in documenti diversi. Ad esempio, se il primo documento `'startTime'` campo è un valore DateTime e nel secondo documento si tratta di una stringa, l'errore verrà raggiunto. |
| errori dal servizio sottostante dell'origine dati | (da Cosmos DB) `{"Errors":["Request rate is large"]}` | Controllare l'istanza di archiviazione per assicurarsi che sia integro. Potrebbe essere necessario modificare la scalabilità e il partizionamento. |
| problemi temporanei | si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: provider TCP, errore: 0-una connessione esistente è stata chiusa forzatamente dall'host remoto | Occasionalmente si verificano problemi di connettività imprevisti. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-document-content"></a>Errore: non è stato possibile estrarre il contenuto del documento
L'indicizzatore con un'origine dati BLOB non è stato in grado di estrarre il contenuto dal documento, ad esempio un file PDF. Questo problema può verificarsi a causa di:

| Motivo | Dettagli/esempio | Risoluzione |
| --- | --- | --- |
| il BLOB supera il limite di dimensioni | Il documento è `'150441598'` byte, che supera la dimensione massima `'134217728'` byte per l'estrazione del documento per il livello di servizio corrente. | [errori di indicizzazione BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| il tipo di contenuto del BLOB non è supportato | Il tipo di contenuto del documento non è supportato `'image/png'` | [errori di indicizzazione BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| il BLOB è crittografato | Non è stato possibile elaborare il documento perché potrebbe essere crittografato o protetto da password. | È possibile ignorare il BLOB con [le impostazioni BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| problemi temporanei | Errore durante l'elaborazione del BLOB: la richiesta è stata interrotta: la richiesta è stata annullata. | Occasionalmente si verificano problemi di connettività imprevisti. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Errore: non è stato possibile analizzare il documento
L'indicizzatore ha letto il documento dall'origine dati, ma si è verificato un problema durante la conversione del contenuto del documento nello schema di mapping dei campi specificato. Questo problema può verificarsi a causa di:

| Motivo | Dettagli/esempio | Risoluzione |
| --- | --- | --- |
| Manca la chiave del documento | La chiave del documento non può essere mancante o vuota | Verificare che tutti i documenti dispongano di chiavi di documento valide |
| La chiave del documento non è valida | La chiave del documento non può contenere più di 1024 caratteri | Modificare la chiave del documento per soddisfare i requisiti di convalida. |
| Non è stato possibile applicare il mapping dei campi a un campo | Non è stato possibile applicare la funzione di mapping `'functionName'` al campo `'fieldName'`. La matrice non può essere null. Nome parametro: bytes | Controllare i [mapping dei campi](search-indexer-field-mappings.md) definiti nell'indicizzatore e confrontarli con i dati del campo specificato del documento non riuscito. Potrebbe essere necessario modificare i mapping dei campi o i dati del documento. |
| Impossibile leggere il valore del campo | Impossibile leggere il valore della colonna `'fieldName'` in corrispondenza dell'indice `'fieldIndex'`. si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: provider TCP, errore: 0-una connessione esistente è stata chiusa forzatamente dall'host remoto.) | Questi errori sono in genere causati da problemi di connettività imprevisti con il servizio sottostante dell'origine dati. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Errore: non è stato possibile eseguire la competenza
L'indicizzatore non è stato in grado di eseguire una competenza nel grado di competenze.

| Motivo | Dettagli/esempio | Risoluzione |
| --- | --- | --- |
| Un campo contiene un termine troppo grande | Un termine nel documento è superiore al limite di [32 KB](search-limits-quotas-capacity.md#api-request-limits) | È possibile evitare questa restrizione assicurandosi che il campo non sia configurato come filtrabile, facet o ordinabile.
| Il documento è troppo grande per essere indicizzato | Un documento è più grande delle [dimensioni massime delle richieste API](search-limits-quotas-capacity.md#api-request-limits) | [Come indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
| Problemi di connettività temporanei | Si è verificato un errore temporaneo. Riprovare più tardi. | Occasionalmente si verificano problemi di connettività imprevisti. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |
| Potenziale bug del prodotto | Si è verificato un errore imprevisto. | Indica una classe di errore sconosciuta e può indicare la presenza di un bug del prodotto. Inviare un [ticket di supporto](https://ms.portal.azure.com/#create/Microsoft.Support) per ottenere assistenza. |
| Si è verificato un errore durante l'esecuzione | (Da Merge skill) Uno o più valori di offset non sono validi e non possono essere analizzati. Gli elementi sono stati inseriti alla fine del testo | Utilizzare le informazioni contenute nel messaggio di errore per risolvere il problema. Questo tipo di errore richiederà un'azione da risolvere. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Errore: non è stato possibile eseguire la competenza perché la richiesta dell'API Web non è riuscita
L'esecuzione delle competenze non è riuscita perché la chiamata all'API Web non è riuscita. In genere, questa classe di errore si verifica quando vengono usate le competenze personalizzate, nel qual caso sarà necessario eseguire il debug del codice personalizzato per risolvere il problema. Se invece l'errore è dovuto a una competenza incorporata, fare riferimento al messaggio di errore per informazioni sulla risoluzione del problema.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Errore: non è stato possibile eseguire l'abilità perché la risposta alle competenze dell'API Web non è valida
L'esecuzione delle competenze non è riuscita perché la chiamata all'API Web ha restituito una risposta non valida. In genere, questa classe di errore si verifica quando vengono usate le competenze personalizzate, nel qual caso sarà necessario eseguire il debug del codice personalizzato per risolvere il problema. Se invece l'errore è dovuto a una competenza incorporata, inviare un [ticket di supporto](https://ms.portal.azure.com/#create/Microsoft.Support) per richiedere assistenza.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Errore: non è stata eseguita alcuna perizia entro il limite di tempo
Esistono due casi in cui è possibile che si verifichi questo messaggio di errore, ognuno dei quali deve essere trattato in modo diverso. Seguire le istruzioni riportate di seguito, a seconda della capacità che ha restituito l'errore.

### <a name="built-in-cognitive-service-skills"></a>Competenze del servizio cognitivo predefinite
Molte delle competenze cognitive predefinite, ad esempio il rilevamento della lingua, il riconoscimento di entità o l'OCR, sono supportate da un endpoint API del servizio cognitivo. Talvolta si verificano problemi temporanei con questi endpoint e si verifica il timeout di una richiesta. Per i problemi temporanei, non esiste alcun rimedio ad eccezione dell'attesa e riprovare. Come mitigazione, provare a impostare l'indicizzatore per l' [esecuzione in base a una pianificazione](search-howto-schedule-indexers.md). L'indicizzazione pianificata preleva dal punto in cui è stata interrotta. Supponendo che si verifichino problemi temporanei, l'indicizzazione e l'elaborazione delle competenze cognitive dovrebbero essere in grado di continuare alla successiva esecuzione pianificata.

### <a name="custom-skills"></a>Competenze personalizzate
Se si verifica un errore di timeout con un'abilità personalizzata creata, è possibile provare un paio di cose. Esaminare prima di tutto la propria abilità personalizzata e assicurarsi che non rimanga bloccata in un ciclo infinito e che restituisca un risultato coerente. Una volta confermata questa situazione, determinare il tempo di esecuzione delle proprie competenze. Se non è stato impostato in modo esplicito un valore `timeout` nella definizione di competenze personalizzate, il `timeout` predefinito è 30 secondi. Se la capacità di esecuzione non è sufficiente per 30 secondi, è possibile specificare un valore di `timeout` superiore nella definizione di competenze personalizzate. Di seguito è riportato un esempio di definizione di competenze personalizzate in cui il timeout è impostato su 90 secondi:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Il valore massimo che è possibile impostare per il parametro `timeout` è 230 secondi.  Se l'abilità personalizzata non è in grado di essere eseguita in modo coerente entro 230 secondi, è possibile valutare la possibilità di ridurre il `batchSize` dell'abilità personalizzata in modo da elaborare un minor numero di documenti in una singola esecuzione.  Se la `batchSize` è già stata impostata su 1, sarà necessario riscrivere la capacità per poter essere eseguita in meno di 230 secondi oppure suddividerla in più competenze personalizzate, in modo che il tempo di esecuzione per ogni singola skill personalizzata sia un massimo di 230 secondi. Per ulteriori informazioni, vedere la [documentazione relativa alle competenze personalizzate](cognitive-search-custom-skill-web-api.md) .

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Errore: non è stato possibile '`MergeOrUpload`' | documento '`Delete`' nell'indice di ricerca

Il documento è stato letto ed elaborato, ma l'indicizzatore non è stato in grado di aggiungerlo all'indice di ricerca. Questo problema può verificarsi a causa di:

| Motivo | Dettagli/esempio | Risoluzione |
| --- | --- | --- |
| Un termine nel documento è superiore al limite di [32 KB](search-limits-quotas-capacity.md#api-request-limits) | Un campo contiene un termine troppo grande | È possibile evitare questa restrizione assicurandosi che il campo non sia configurato come filtrabile, facet o ordinabile.
| Un documento è più grande delle [dimensioni massime delle richieste API](search-limits-quotas-capacity.md#api-request-limits) | Il documento è troppo grande per essere indicizzato | [Come indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
| Il documento contiene troppi oggetti nella raccolta | Una raccolta nel documento supera il [numero massimo di elementi in tutte le raccolte complesse](search-limits-quotas-capacity.md#index-limits) | Si consiglia di ridurre le dimensioni della raccolta complessa nel documento al di sotto del limite ed evitare un utilizzo elevato dello spazio di archiviazione.
| Problemi di connessione all'indice di destinazione (persistente dopo i tentativi) perché il servizio è sottoposto ad altro carico, ad esempio l'esecuzione di query o l'indicizzazione. | Impossibile stabilire la connessione a Update index. Il servizio di ricerca è sottoposto a un carico elevato. | [Ridimensionare il servizio di ricerca](search-capacity-planning.md)
| È in corso la correzione del servizio di ricerca per l'aggiornamento del servizio o durante la riconfigurazione della topologia. | Impossibile stabilire la connessione a Update index. Il servizio di ricerca è attualmente inattivo o il servizio di ricerca è in fase di transizione. | Configurare il servizio con almeno 3 repliche per la disponibilità del 99,9% per ogni [contratto](https://azure.microsoft.com/support/legal/sla/search/v1_0/) di servizio
| Errore nella risorsa di calcolo/rete sottostante (rare) | Impossibile stabilire la connessione a Update index. Si è verificato un errore sconosciuto. | Configurare gli indicizzatori per [l'esecuzione in base a una pianificazione](search-howto-schedule-indexers.md) per riprendersi da uno stato di errore.
| Una richiesta di indicizzazione effettuata all'indice di destinazione non è stata riconosciuta entro un periodo di timeout a causa di problemi di rete. | Non è stato possibile stabilire la connessione all'indice di ricerca in modo tempestivo. | Configurare gli indicizzatori per [l'esecuzione in base a una pianificazione](search-howto-schedule-indexers.md) per riprendersi da uno stato di errore. Inoltre, provare a ridurre le dimensioni del [batch](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) dell'indicizzatore se questa condizione di errore viene mantenute.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Errore: Impossibile indicizzare il documento perché i dati dell'indicizzatore da indicizzare non sono validi

Il documento è stato letto ed elaborato, ma a causa di una mancata corrispondenza tra la configurazione dei campi dell'indice e la natura dei dati estratti dall'indicizzatore, non è stato possibile aggiungerlo all'indice di ricerca. Questo problema può verificarsi a causa di:

| Motivo | Dettagli/esempio
| --- | ---
| Il tipo di dati dei campi estratti dall'indicizzatore non è compatibile con il modello di dati del campo indice di destinazione corrispondente. | Il campo dati '_Data_' nel documento con chiave '_Data_' contiene un valore non valido ' di tipo ' EDM. String ''. Il tipo previsto è' Collection (EDM. String)'. |
| Non è stato possibile estrarre alcuna entità JSON da un valore stringa. | Non è stato possibile analizzare il valore ' di tipo ' EDM. String '' del campo '_Data_' come oggetto JSON. Errore:' dopo l'analisi di un valore è stato rilevato un carattere imprevisto:''. Percorso '_path_', riga 1, posizione 3162 .' |
| Non è stato possibile estrarre una raccolta di entità JSON da un valore stringa.  | Non è stato possibile analizzare il valore ' di tipo ' EDM. String '' del campo '_Data_' come matrice JSON. Errore:' dopo l'analisi di un valore è stato rilevato un carattere imprevisto:''. Percorso ' [0]', riga 1, posizione 27 .' |
| Un tipo sconosciuto è stato individuato nel documento di origine. | Impossibile indicizzare il tipo sconosciuto '_Unknown_' |
| Una notazione incompatibile per i punti geografici è stata usata nel documento di origine. | I valori letterali stringa del punto WKT non sono supportati. Usare invece i valori letterali del punto GeoJSON |

In tutti questi casi, fare riferimento ai [tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e al mapping dei tipi di [dati per gli indicizzatori](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) per assicurarsi di compilare correttamente lo schema dell'indice e di aver configurato i mapping appropriati dei [campi dell'indicizzatore](search-indexer-field-mappings.md). Il messaggio di errore includerà i dettagli che consentono di tenere traccia dell'origine della mancata corrispondenza.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Errore: non è stato possibile elaborare il documento entro il tempo di esecuzione massimo dell'indicizzatore

Questo errore si verifica quando l'indicizzatore non è in grado di completare l'elaborazione di un singolo documento dall'origine dati entro il tempo di esecuzione consentito. Il [tempo di esecuzione massimo](search-limits-quotas-capacity.md#indexer-limits) è più breve quando si utilizzano skillsets. Quando si verifica questo errore, se maxFailedItems è impostato su un valore diverso da 0, l'indicizzatore ignora il documento nelle esecuzioni future in modo che l'indicizzazione possa avanzare. Se non è possibile permettersi di ignorare un documento o se questo errore viene visualizzato in modo coerente, provare a suddividere i documenti in documenti più piccoli, in modo da poter eseguire lo stato di avanzamento parziale all'interno di una singola esecuzione dell'indicizzatore.

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-could-not-execute-skill-because-a-skill-input-was-invalid"></a>Avviso: non è stato possibile eseguire l'abilità perché un input di competenze non è valido
L'indicizzatore non è stato in grado di eseguire una competenza nel livello di competenze perché manca un input per la skill, il tipo errato o altrimenti non è valido.

Le competenze cognitive hanno input obbligatori e input facoltativi. Ad esempio, l' [abilità di estrazione della frase chiave](cognitive-search-skill-keyphrases.md) ha due input obbligatori `text`, `languageCode`e nessun input facoltativo. Se gli input obbligatori non sono validi, l'abilità viene ignorata e viene generato un avviso. Le competenze ignorate non generano output, pertanto se altre competenze usano output della competenza ignorata, possono generare avvisi aggiuntivi.

Se si vuole fornire un valore predefinito in caso di input mancante, è possibile usare l' [abilità condizionale](cognitive-search-skill-conditional.md) per generare un valore predefinito e quindi usare l'output della [competenza condizionale](cognitive-search-skill-conditional.md) come input di competenze.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Motivo | Dettagli/esempio | Risoluzione |
| --- | --- | --- |
| Il tipo di input Skill è errato | Il tipo di input di competenze richiesto `X` non era del tipo previsto `String`. L'input di competenze richiesto `X` non è nel formato previsto. | Alcune competenze prevedono input di determinati tipi, ad esempio la [competenza](cognitive-search-skill-sentiment.md) di valutazione prevede che `text` sia una stringa. Se l'input specifica un valore non stringa, l'abilità non viene eseguita e non genera alcun output. Verificare che il set di dati includa valori di input uniformi nel tipo o usare una [competenza personalizzata per l'API Web](cognitive-search-custom-skill-web-api.md) per la pre-elaborazione dell'input. Se si sta scorrendo la competenza su una matrice, verificare che il contesto di competenza e l'input abbiano `*` nelle posizioni corrette. Il contesto e l'origine di input devono essere in genere terminati con `*` per le matrici. |
| Input skill mancante | Manca l'input di competenze richiesto `X`. | Se tutti i documenti ricevono questo avviso, è probabile che si verifichi un errore di digitazione nei percorsi di input ed è necessario controllare la combinazione di maiuscole e minuscole con il nome della proprietà, il `*` supplementare o mancante nel percorso e i documenti dell'origine dati definiscono gli input necessari. |
| Input codice lingua competenze non valido | L'input di competenze `languageCode` dispone dei seguenti codici di lingua `X,Y,Z`, almeno uno dei quali non è valido. | Vedere altri dettagli di [seguito](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Avviso: l'input skill ' codiceLingua ' ha i seguenti codici di lingua ' X, Y, Z ', almeno uno dei quali non è valido.
Uno o più valori passati nell'input `languageCode` facoltativo di una competenza downstream non è supportato. Questo problema può verificarsi se si passa l'output del [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) alle competenze successive e l'output è costituito da più lingue rispetto a quelle supportate nelle competenze downstream.

Se si è certi che il set di dati si trova in una sola lingua, è necessario rimuovere il [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e l'input `languageCode` skill e usare il `defaultLanguageCode` parametro skill per tale skill, supponendo che la lingua sia supportata per tale competenza.

Se si è certi che il set di dati contiene più lingue ed è quindi necessario [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e `languageCode` input, è consigliabile aggiungere un [ConditionalSkill](cognitive-search-skill-conditional.md) per filtrare il testo con lingue non supportate prima di passare il testo della competenza downstream.  Di seguito è riportato un esempio di ciò che potrebbe essere simile a EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Di seguito sono riportati alcuni riferimenti per le lingue attualmente supportate per ognuna delle competenze che possono generare questo messaggio di errore:
* [Analisi del testo le lingue supportate](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (per [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)e [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Lingue supportate da Translator](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (per il [TranslationSkill di testo](cognitive-search-skill-text-translation.md))
* [SplitSkill di testo](cognitive-search-skill-textsplit.md) Lingue supportate: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Avviso: l'input di competenze è stato troncato
Le competenze cognitive hanno limiti alla lunghezza del testo che può essere analizzato in una sola volta. Se l'input di testo di queste competenze supera tale limite, il testo verrà troncato per soddisfare il limite e quindi verrà eseguito l'arricchimento del testo troncato. Ciò significa che l'abilità viene eseguita, ma non su tutti i dati.

Nell'esempio LanguageDetectionSkill riportato di seguito, il campo di input `'text'` può attivare questo avviso se supera il limite di caratteri. È possibile trovare i limiti di input delle [competenze nella documentazione relativa alle competenze](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Per assicurarsi che tutto il testo venga analizzato, valutare la possibilità di usare l' [abilità Split](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Avviso: la risposta di competenza dell'API Web contiene avvisi
L'indicizzatore è stato in grado di eseguire una competenza nel grado di competenze, ma la risposta dalla richiesta dell'API Web indicava la presenza di avvisi durante l'esecuzione. Esaminare gli avvisi per comprendere in che modo i dati sono interessati e se è necessaria un'azione.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Avviso: la configurazione corrente dell'indicizzatore non supporta lo stato incrementale

Questo avviso viene generato solo per le origini dati Cosmos DB.

L'avanzamento incrementale durante l'indicizzazione assicura che, in caso di interruzione dell'esecuzione dell'indicizzatore a causa di errori temporanei o del limite del tempo di esecuzione, l'indicizzatore possa riprendere dal punto in cui è stato interrotto all'esecuzione successiva, invece di dovere ripetere dall'inizio l'indicizzazione dell'intera raccolta. Questo approccio risulta particolarmente importante in caso di indicizzazione di raccolte di grandi dimensioni.

La possibilità di riprendere un processo di indicizzazione non completata si basa sul fatto che i documenti siano ordinati in base alla colonna `_ts`. L'indicizzatore usa il timestamp per determinare quale documento scegliere successivamente. Se la colonna `_ts` è mancante o se l'indicizzatore non è in grado di determinare se una query personalizzata è ordinata, l'indicizzatore inizia all'inizio e verrà visualizzato questo avviso.

È possibile eseguire l'override di questo comportamento, abilitando lo stato incrementale ed eliminando questo avviso utilizzando la proprietà di configurazione `assumeOrderByHighWatermarkColumn`.

Per ulteriori informazioni, vedere [avanzamento incrementale e query personalizzate](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Avviso: durante la proiezione alcuni dati sono stati persi. La riga ' X ' nella tabella ' Y ' contiene la proprietà di stringa ' Z ' che è troppo lungo.

Il [servizio di archiviazione tabelle](https://azure.microsoft.com/services/storage/tables) presenta limiti per quanto possono essere le [proprietà di entità](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) di grandi dimensioni. Le stringhe possono contenere almeno 32.000 caratteri. Se viene proiettata una riga con una proprietà di stringa di lunghezza superiore a 32.000 caratteri, vengono conservati solo i primi 32.000 caratteri. Per ovviare a questo problema, evitare di proiettare righe con proprietà di stringa di lunghezza superiore a 32.000 caratteri.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Avviso: troncato testo estratto a caratteri X
Gli indicizzatori limitano la quantità di testo che è possibile estrarre da un documento. Questo limite dipende dal piano tariffario: 32.000 caratteri per il livello gratuito, 64.000 per Basic e 4 milioni per i livelli standard, standard S2 e S3 standard. Il testo troncato non verrà indicizzato. Per evitare questo avviso, provare a suddividere i documenti con grandi quantità di testo in più documenti più piccoli. 

Per altre informazioni, vedere [limiti dell'indicizzatore](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Avviso: Impossibile eseguire il mapping del campo di output ' X ' all'indice di ricerca
I mapping dei campi di output che fanno riferimento a dati inesistenti/null produrranno avvisi per ogni documento e restituiscono un campo di indice vuoto. Per aggirare questo problema, controllare i percorsi di origine del mapping dei campi di output per individuare possibili errori di digitazione o impostare un valore predefinito usando la [competenza condizionale](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Avviso: i criteri di rilevamento delle modifiche dei dati sono configurati per l'utilizzo della colonna chiave ' X '
I [criteri di rilevamento delle modifiche dei dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) presentano requisiti specifici per le colonne utilizzate per rilevare le modifiche. Uno di questi requisiti è che questa colonna viene aggiornata ogni volta che viene modificato l'elemento di origine. Un altro requisito è che il nuovo valore per la colonna sia maggiore del valore precedente. Le colonne chiave non soddisfano questo requisito perché non cambiano a ogni aggiornamento. Per risolvere questo problema, selezionare una colonna diversa per i criteri di rilevamento delle modifiche.