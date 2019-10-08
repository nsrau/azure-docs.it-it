---
title: Errori e avvisi comuni-ricerca di Azure
description: Questo articolo fornisce informazioni e soluzioni per gli errori e gli avvisi comuni che possono verificarsi durante l'arricchimento di intelligenza artificiale in ricerca di Azure.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028312"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Errori e avvisi comuni della pipeline di arricchimento di intelligenza artificiale in ricerca di Azure

Questo articolo fornisce informazioni e soluzioni per gli errori e gli avvisi comuni che possono verificarsi durante l'arricchimento di intelligenza artificiale in ricerca di Azure.

## <a name="errors"></a>Errors
L'indicizzazione viene arrestata quando il numero di errori supera [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se si vuole che gli indicizzatori ignorino questi errori (e ignorino i "documenti non riusciti"), provare ad aggiornare il `maxFailedItems` e `maxFailedItemsPerBatch`, come descritto [qui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Ogni documento con errori insieme alla relativa chiave del documento (se disponibile) verrà visualizzato come errore nello stato di esecuzione dell'indicizzatore. È possibile utilizzare l' [API index](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per caricare manualmente i documenti in un secondo momento se l'indicizzatore è stato impostato in modo da tollerare gli errori.

Le sezioni seguenti consentono di risolvere gli errori, consentendo la continuazione dell'indicizzazione.

### <a name="could-not-read-document"></a>Non è stato possibile leggere il documento
L'indicizzatore non è stato in grado di leggere il documento dall'origine dati. Questo problema può verificarsi a causa di:

| `Reason` | Esempio | Azione |
| --- | --- | --- |
| tipi di campi incoerenti tra documenti diversi | Il tipo di valore non corrisponde al tipo di colonna. Non è stato possibile archiviare `'{47.6,-122.1}'` nella colonna authors.  Il tipo previsto è JArray. | Verificare che il tipo di ogni campo sia lo stesso in documenti diversi. Ad esempio, se il primo documento `'startTime'` campo è un valore DateTime e nel secondo documento si tratta di una stringa, l'errore verrà raggiunto. |
| errori dal servizio sottostante dell'origine dati | (da Cosmos DB) `{"Errors":["Request rate is large"]}` | Controllare l'istanza di archiviazione per assicurarsi che sia integro. Potrebbe essere necessario modificare la scalabilità e il partizionamento. |
| problemi temporanei | si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: Provider TCP, errore: 0: una connessione esistente è stata chiusa forzatamente dall'host remoto | Occasionalmente si verificano problemi di connettività imprevisti. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |

### <a name="could-not-extract-document-content"></a>Non è stato possibile estrarre il contenuto del documento
L'indicizzatore con un'origine dati BLOB non è stato in grado di estrarre il contenuto dal documento, ad esempio un file PDF. Questo problema può verificarsi a causa di:

| `Reason` | Esempio | Azione |
| --- | --- | --- |
| il BLOB supera il limite di dimensioni | Il documento è `'150441598'` byte, che supera la dimensione massima `'134217728'` byte per l'estrazione del documento per il livello di servizio corrente. | [errori di indicizzazione BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| il tipo di contenuto del BLOB non è supportato | Il tipo di contenuto del documento non è supportato `'image/png'` | [errori di indicizzazione BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| il BLOB è crittografato | Non è stato possibile elaborare il documento perché potrebbe essere crittografato o protetto da password. | [impostazioni BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problemi temporanei | Errore durante l'elaborazione del BLOB: La richiesta è stata interrotta: La richiesta è stata annullata. | Occasionalmente si verificano problemi di connettività imprevisti. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |

### <a name="could-not-parse-document"></a>Non è stato possibile analizzare il documento
L'indicizzatore ha letto il documento dall'origine dati, ma si è verificato un problema durante la conversione del contenuto del documento nello schema di mapping dei campi specificato. Questo problema può verificarsi a causa di:

| `Reason` | Esempio | Azione |
| --- | --- | --- |
| Manca la chiave del documento | La chiave del documento non può essere mancante o vuota | Verificare che tutti i documenti dispongano di chiavi di documento valide |
| La chiave del documento non è valida | La chiave del documento non può contenere più di 1024 caratteri | Modificare la chiave del documento per soddisfare i requisiti di convalida. |
| Non è stato possibile applicare il mapping dei campi a un campo | Impossibile applicare la funzione di mapping `'functionName'` al campo `'fieldName'`. La matrice non può essere null. Nome parametro: bytes | Controllare i [mapping dei campi](search-indexer-field-mappings.md) definiti nell'indicizzatore e confrontarli con i dati del campo specificato del documento non riuscito. Potrebbe essere necessario modificare i mapping dei campi o i dati del documento. |
| Impossibile leggere il valore del campo | Impossibile leggere il valore della colonna `'fieldName'` in corrispondenza dell'indice `'fieldIndex'`. si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: Provider TCP, errore: 0: una connessione esistente è stata chiusa forzatamente dall'host remoto. | Questi errori sono in genere causati da problemi di connettività imprevisti con il servizio sottostante dell'origine dati. Provare a eseguire di nuovo il documento tramite l'indicizzatore in un secondo momento. |

### <a name="could-not-index-document"></a>Impossibile indicizzare il documento
Il documento è stato letto ed elaborato, ma l'indicizzatore non è stato in grado di aggiungerlo all'indice di ricerca. Questo problema può verificarsi a causa di:

| `Reason` | Esempio | Azione |
| --- | --- | --- |
| Un campo contiene un termine troppo grande | Un termine nel documento è superiore al limite di [32 KB](search-limits-quotas-capacity.md#api-request-limits) | È possibile evitare questa restrizione assicurandosi che il campo non sia configurato come filtrabile, facet o ordinabile.
| Il documento è troppo grande per essere indicizzato | Un documento è più grande delle [dimensioni massime delle richieste API](search-limits-quotas-capacity.md#api-request-limits) | [Come indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>L'input di competenza ' codiceLingua ' ha i seguenti codici di lingua ' X, Y, Z ', almeno uno dei quali non è valido.
Uno o più valori passati nell'input `languageCode` facoltativo di una competenza downstream non sono supportati. Questo problema può verificarsi se si passa l'output del [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) alle competenze successive e l'output è costituito da più lingue rispetto a quelle supportate nelle competenze downstream.

Se si è certi che il set di dati si trova in una sola lingua, è necessario rimuovere il [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e l'input di competenze `languageCode` e utilizzare il parametro skill `defaultLanguageCode` per tale competenza, supponendo che la lingua sia supportata per tale competenza.

Se si è certi che il set di dati contiene più lingue ed è quindi necessario l'input [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e `languageCode`, è consigliabile aggiungere un [ConditionalSkill](cognitive-search-skill-conditional.md) per filtrare il testo con lingue non supportate prima di passare il testo della competenza downstream.  Di seguito è riportato un esempio di ciò che potrebbe essere simile a EntityRecognitionSkill:

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

### <a name="skill-did-not-execute-within-the-time-limit"></a>L'esperienza non è stata eseguita entro il limite di tempo
Esistono due casi in cui è possibile che si verifichi questo messaggio di errore, ognuno dei quali deve essere trattato in modo diverso. Seguire le istruzioni riportate di seguito, a seconda della capacità che ha restituito l'errore.

#### <a name="built-in-cognitive-service-skills"></a>Competenze del servizio cognitivo predefinite
Molte delle competenze cognitive predefinite, ad esempio il rilevamento della lingua, il riconoscimento di entità o l'OCR, sono supportate da un endpoint API del servizio cognitivo. Talvolta si verificano problemi temporanei con questi endpoint e si verifica il timeout di una richiesta. Per i problemi temporanei, non esiste alcun rimedio ad eccezione dell'attesa e riprovare. Come mitigazione, provare a impostare l'indicizzatore per l' [esecuzione in base a una pianificazione](search-howto-schedule-indexers.md). L'indicizzazione pianificata preleva dal punto in cui è stata interrotta. Supponendo che si verifichino problemi temporanei, l'indicizzazione e l'elaborazione delle competenze cognitive dovrebbero essere in grado di continuare alla successiva esecuzione pianificata.

#### <a name="custom-skills"></a>Competenze personalizzate
Se si verifica un errore di timeout con un'abilità personalizzata creata, è possibile provare un paio di cose. Esaminare prima di tutto la propria abilità personalizzata e assicurarsi che non rimanga bloccata in un ciclo infinito e che restituisca un risultato coerente. Una volta confermata questa situazione, determinare il tempo di esecuzione delle proprie competenze. Se non è stato impostato in modo esplicito un valore `timeout` nella definizione di competenze personalizzate, il valore predefinito di `timeout` è 30 secondi. Se la capacità di esecuzione non è sufficiente per 30 secondi, è possibile specificare un valore `timeout` superiore per la definizione di abilità personalizzata. Di seguito è riportato un esempio di definizione di competenze personalizzate in cui il timeout è impostato su 90 secondi:

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

Il valore massimo che è possibile impostare per il parametro `timeout` è 230 secondi.  Se l'abilità personalizzata non è in grado di essere eseguita in modo coerente entro 230 secondi, è possibile valutare la possibilità di ridurre il `batchSize` dell'abilità personalizzata in modo da elaborare un minor numero di documenti all'interno di una singola esecuzione.  Se è già stato impostato il `batchSize` su 1, sarà necessario riscrivere la competenza per poter essere eseguita in meno di 230 secondi oppure suddividerla in più competenze personalizzate, in modo che il tempo di esecuzione per ogni singola skill personalizzata sia un massimo di 230 secondi. Per ulteriori informazioni, vedere la [documentazione relativa alle competenze personalizzate](cognitive-search-custom-skill-web-api.md) .

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Non è stato possibile ' `MergeOrUpload`' | documento ' `Delete`' nell'indice di ricerca

Il documento è stato letto ed elaborato, ma l'indicizzatore non è stato in grado di aggiungerlo all'indice di ricerca. Questo problema può verificarsi a causa di:

| `Reason` | Esempio | Azione |
| --- | --- | --- |
| Un termine nel documento è superiore al limite di [32 KB](search-limits-quotas-capacity.md#api-request-limits) | Un campo contiene un termine troppo grande | È possibile evitare questa restrizione assicurandosi che il campo non sia configurato come filtrabile, facet o ordinabile.
| Un documento è più grande delle [dimensioni massime delle richieste API](search-limits-quotas-capacity.md#api-request-limits) | Il documento è troppo grande per essere indicizzato | [Come indicizzare set di dati di grandi dimensioni](search-howto-large-index.md)
| Problemi di connessione all'indice di destinazione (persistente dopo i tentativi) perché il servizio è sottoposto ad altro carico, ad esempio l'esecuzione di query o l'indicizzazione. | Impossibile stabilire la connessione a Update index. Il servizio di ricerca è sottoposto a un carico elevato. | [Ridimensionare il servizio di ricerca](search-capacity-planning.md)
| È in corso la correzione del servizio di ricerca per l'aggiornamento del servizio o durante la riconfigurazione della topologia. | Impossibile stabilire la connessione a Update index. Il servizio di ricerca è attualmente inattivo o il servizio di ricerca è in fase di transizione. | Configurare il servizio con almeno 3 repliche per la disponibilità del 99,9% per ogni [contratto](https://azure.microsoft.com/support/legal/sla/search/v1_0/) di servizio
| Errore nella risorsa di calcolo/rete sottostante (rare) | Impossibile stabilire la connessione a Update index. Si è verificato un errore sconosciuto. | Configurare gli indicizzatori per [l'esecuzione in base a una pianificazione](search-howto-schedule-indexers.md) per riprendersi da uno stato di errore.

##  <a name="warnings"></a>Avvisi
Gli avvisi non interrompono l'indicizzazione, ma indicano condizioni che potrebbero causare risultati imprevisti. Il fatto di intervenire o meno dipende dai dati e dallo scenario.

### <a name="skill-input-was-truncated"></a>Input abilità troncato
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