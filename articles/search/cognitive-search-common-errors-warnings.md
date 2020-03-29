---
title: Errori e avvisi dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Questo articolo fornisce informazioni e soluzioni a errori e avvisi comuni che possono verificarsi durante l'arricchimento dell'iaformazione in Ricerca cognitiva di Azure.This article provides information and solutions to common errors and warnings you might encounter during AI enrichment in Azure Cognitive Search.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72bf08dce36d857c1fe91bbe9806336dfa185f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671983"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Risoluzione degli errori e degli avvisi comuni dell'indicizzatore in Ricerca cognitiva di AzureTroubleshooting common indexer errors and warnings in Azure Cognitive Search

Questo articolo fornisce informazioni e soluzioni a errori e avvisi comuni che possono verificarsi durante l'indicizzazione e l'arricchimento dell'iaformazione in Ricerca cognitiva di Azure.This article provides information and solutions to common errors and warnings you might encounter during indexing and AI enrichment in Azure Cognitive Search.

L'indicizzazione si interrompe quando il numero di errori supera ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se si desidera che gli indicizzatori ignorino questi errori `maxFailedItems` `maxFailedItemsPerBatch` (e ignorino i "documenti non riusciti"), è consigliabile aggiornare e come descritto [di seguito](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Ogni documento non riuscito insieme alla relativa chiave di documento (se disponibile) verrà visualizzato come errore nello stato di esecuzione dell'indicizzatore. È possibile utilizzare [l'API dell'indice](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per caricare manualmente i documenti in un secondo momento se l'indicizzatore è stato impostato per tollerare gli errori.

Le informazioni sugli errori contenute in questo articolo consentono di risolvere gli errori, consentendo di continuare l'indicizzazione.

Gli avvisi non interrompono l'indicizzazione, ma indicano condizioni che potrebbero generare risultati imprevisti. L'intervento o meno dipende dai dati e dallo scenario.

A partire `2019-05-06`dalla versione dell'API , gli errori e gli avvisi dell'indicizzatore a livello di elemento sono strutturati per fornire una maggiore chiarezza sulle cause e sui passaggi successivi. Contengono le seguenti proprietà:

| Proprietà | Descrizione | Esempio |
| --- | --- | --- |
| Key | ID documento del documento interessato dall'errore o dall'avviso. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Nome dell'operazione che descrive dove si è verificato l'errore o l'avviso. Questo viene generato dalla seguente struttura: [categoria]. [sottocategoria]. [resourceType]. [ResourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Descrizione generale dell'errore o dell'avviso. | Impossibile eseguire la competenza perché la richiesta Web Api non è riuscita. |
| dettagli | Eventuali dettagli aggiuntivi che possono essere utili per diagnosticare il problema, ad esempio la risposta WebApi se l'esecuzione di una competenza personalizzata non è riuscita. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 fonte,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... resto della traccia dello stack... |
| documentazioneLink | Un collegamento alla documentazione pertinente con informazioni dettagliate per eseguire il debug e risolvere il problema. Questo collegamento spesso punta a una delle sezioni seguenti in questa pagina. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Errore: impossibile leggere il documento

L'indicizzatore non è riuscito a leggere il documento dall'origine dati. Questo può accadere a causa di:

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| Tipi di campi incoerenti in documenti diversi | "Il tipo di valore ha una mancata corrispondenza con il tipo di colonna. Impossibile archiviare `'{47.6,-122.1}'` la colonna authors.  Il tipo previsto è JArray."  "Errore durante la conversione del tipo di dati nvarchar in float."  "Conversione non riuscita durante la conversione del valore nvarchar '12 months' nel tipo di dati int."  "Errore di runtime: si è verificato un errore di overflow aritmetico durante la conversione del tipo di dati da espressione a int". | Assicurarsi che il tipo di ogni campo sia lo stesso tra documenti diversi. Ad esempio, se `'startTime'` il primo campo del documento è un DateTime e nel secondo documento è una stringa, questo errore verrà visualizzato. |
| errori dal servizio sottostante dell'origine dati | (da Cosmos DB)`{"Errors":["Request rate is large"]}` | Controllare l'istanza di archiviazione per assicurarsi che sia integra. Potrebbe essere necessario regolare il ridimensionamento/partizionamento. |
| questioni transitorie | si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: Provider TCP, errore: 0 - Una connessione esistente è stata chiusa con la rete dall'host remoto | Occasionalmente si sono verificati problemi di connettività imprevisti. Provare a eseguire nuovamente il documento tramite l'indicizzatore in un secondo momento. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Errore: impossibile estrarre contenuto o metadati dal documento
L'indicizzatore con un'origine dati BLOB non è stato in grado di estrarre il contenuto o i metadati dal documento, ad esempio un file PDF. Questo può accadere a causa di:

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| blob è oltre il limite di dimensione | Il `'150441598'` documento è byte, che `'134217728'` supera i byte di dimensioni massime per l'estrazione del documento per il livello di servizio corrente. | [errori di indicizzazione BLOBBLOB indexing errors](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| il BLOB ha un tipo di contenuto non supportato | Il documento ha un tipo di contenuto non supportato`'image/png'` | [errori di indicizzazione BLOBBLOB indexing errors](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob è crittografato | Impossibile elaborare il documento: potrebbe essere crittografato o protetto da password. | È possibile ignorare il BLOB con [le impostazioni BLOB.](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| questioni transitorie | "Errore durante l'elaborazione del BLOB: la richiesta è stata interrotta: la richiesta è stata annullata." "Timeout documento durante l'elaborazione." | Occasionalmente si sono verificati problemi di connettività imprevisti. Provare a eseguire nuovamente il documento tramite l'indicizzatore in un secondo momento. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Errore: impossibile analizzare il documento
L'indicizzatore ha letto il documento dall'origine dati, ma si è verificato un problema durante la conversione del contenuto del documento nello schema di mapping dei campi specificato. Questo può accadere a causa di:

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| La chiave del documento non è presente | Chiave documento non può essere mancante o vuota | Assicurarsi che tutti i documenti dispongano di chiavi di documento valide |
| La chiave del documento non è valida | La chiave del documento non può essere più lunga di 1024 caratteri | Modificare la chiave del documento per soddisfare i requisiti di convalida. |
| Impossibile applicare il mapping dei campi a un campo | Impossibile applicare `'functionName'` la `'fieldName'`funzione di mapping al campo . Matrice non può essere null. Nome parametro: byte | Controllare i [mapping dei campi](search-indexer-field-mappings.md) definiti nell'indicizzatore e confrontarli con i dati del campo specificato del documento non riuscito. Potrebbe essere necessario modificare i mapping dei campi o i dati del documento. |
| Impossibile leggere il valore del campo | Impossibile leggere il `'fieldName'` valore `'fieldIndex'`della colonna in corrispondenza dell'indice. si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: Provider TCP, errore: 0 - Connessione in corso interrotta forzatamente dall'host remoto). | Questi errori sono in genere dovuti a problemi di connettività imprevisti con il servizio sottostante dell'origine dati. Provare a eseguire nuovamente il documento tramite l'indicizzatore in un secondo momento. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Errore: impossibile eseguire la competenza
L'indicizzatore non è stato in grado di eseguire una competenza nel set di competenze.

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| Problemi di connettività temporanei | Si è verificato un errore temporaneo. Riprovare più tardi. | Occasionalmente si sono verificati problemi di connettività imprevisti. Provare a eseguire nuovamente il documento tramite l'indicizzatore in un secondo momento. |
| Potenziale bug del prodotto | Si è verificato un errore imprevisto. | Ciò indica una classe sconosciuta di errore e può significare che c'è un bug del prodotto. Si prega di presentare un ticket di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) per ottenere aiuto. |
| Un'abilità ha rilevato un errore durante l'esecuzione | (Da competenza unione) Uno o più valori di offset non sono validi e non è stato possibile analizzarli. Gli elementi sono stati inseriti alla fine del testo | Utilizzare le informazioni nel messaggio di errore per risolvere il problema. Questo tipo di errore richiederà un'azione per risolvere. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Errore: impossibile eseguire la competenza perché la richiesta API Web non è riuscita
Esecuzione della competenza non riuscita perché la chiamata all'API Web non è riuscita. In genere, questa classe di errore si verifica quando vengono utilizzate competenze personalizzate, nel qual caso sarà necessario eseguire il debug del codice personalizzato per risolvere il problema. Se invece l'errore proviene da una competenza incorporata, fare riferimento al messaggio di errore per informazioni sulla risoluzione del problema.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Errore: impossibile eseguire la competenza perché la risposta alle competenze dell'API Web non è valida
Esecuzione della competenza non riuscita perché la chiamata all'API Web ha restituito una risposta non valida. In genere, questa classe di errore si verifica quando vengono utilizzate competenze personalizzate, nel qual caso sarà necessario eseguire il debug del codice personalizzato per risolvere il problema. Se invece il fallimento proviene da un'abilità integrata, si prega di presentare un ticket di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) per ottenere assistenza.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Errore: l'abilità non è stata eseguita entro il limite di tempo
Ci sono due casi in cui è possibile riscontrare questo messaggio di errore, ognuno dei quali dovrebbe essere trattato in modo diverso. Si prega di seguire le istruzioni riportate di seguito a seconda di quale abilità ha restituito questo errore per voi.

### <a name="built-in-cognitive-service-skills"></a>Competenze integrate nel servizio cognitivo
Molte delle competenze cognitive incorporate, ad esempio il rilevamento della lingua, il riconoscimento di entità o l'OCR, sono supportate da un endpoint dell'API del servizio cognitivo. A volte ci sono problemi temporanei con questi endpoint e una richiesta stimerà. Per problemi temporanei, non vi è alcun rimedio se non aspettare e riprovare. Per una attenuazione, è consigliabile impostare l'indicizzatore in modo che [venga eseguito in base](search-howto-schedule-indexers.md)a una pianificazione. L'indicizzazione pianificata riprende dal punto in cui era stata interrotta. Supponendo che i problemi temporanei vengano risolti, l'indicizzazione e l'elaborazione delle competenze cognitive dovrebbero essere in grado di continuare alla successiva esecuzione pianificata.

Se si continua a vedere questo errore sullo stesso documento per una competenza cognitiva incorporata, si prega di presentare un ticket di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) per ottenere assistenza, in quanto questo non è previsto.

### <a name="custom-skills"></a>Competenze personalizzate
Se si verifica un errore di timeout con una competenza personalizzata creata, ci sono un paio di cose che puoi provare. In primo luogo, rivedere la vostra abilità personalizzata e assicurarsi che non si blocca in un ciclo infinito e che sta restituendo un risultato in modo coerente. Una volta confermato che è il caso, determinare ciò che il tempo di esecuzione della vostra abilità è. Se non hai impostato `timeout` in modo esplicito un valore `timeout` nella definizione di abilità personalizzata, il valore predefinito è 30 secondi. Se 30 secondi non sono sufficienti per l'esecuzione `timeout` della tua abilità, puoi specificare un valore più alto nella tua definizione di abilità personalizzata. Di seguito è riportato un esempio di definizione di competenza personalizzata in cui il timeout è impostato su 90 secondi:Here is an example of a custom skill definition where the timeout is set to 90 seconds:

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

Il valore massimo che è `timeout` possibile impostare per il parametro è 230 secondi.  Se la tua competenza personalizzata non è in grado `batchSize` di essere eseguita in modo coerente entro 230 secondi, potresti considerare la possibilità di ridurre la tua abilità personalizzata in modo che abbia meno documenti da elaborare all'interno di una singola esecuzione.  Se hai già `batchSize` impostato il tuo su 1, dovrai riscrivere la abilità per poter essere eseguita in meno di 230 secondi o dividerla in modo che il tempo di esecuzione per ogni singola abilità personalizzata sia massimo di 230 secondi. Per ulteriori informazioni, consultare la [documentazione delle competenze personalizzate.](cognitive-search-custom-skill-web-api.md)

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Errore: Impossibile`MergeOrUpload`' ' '`Delete`' documento all'indice di ricerca

Il documento è stato letto ed elaborato, ma l'indicizzatore non è riuscito ad aggiungerlo all'indice di ricerca. Questo può accadere a causa di:

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| Un campo contiene un termine troppo grande | Un termine nel documento è maggiore del [limite di 32 KB](search-limits-quotas-capacity.md#api-request-limits) | È possibile evitare questa restrizione assicurandosi che il campo non sia configurato come filtrabile, in modo comprensibile o ordinabile.
| Il documento è troppo grande per essere indicizzato | Un documento è maggiore della dimensione massima della [richiesta api](search-limits-quotas-capacity.md#api-request-limits) | [Come indicizzare set di dati di grandi dimensioniHow to index large data sets](search-howto-large-index.md)
| Il documento contiene troppi oggetti nella raccolta | Una raccolta nel documento supera il [limite massimo di elementi in tutte le raccolte complesse](search-limits-quotas-capacity.md#index-limits) "Il documento con chiave `'1000052'` contiene `'4303'` oggetti nelle raccolte (matrici JSON). La `'3000'` maggior parte degli oggetti può trovarsi nelle raccolte dell'intero documento. Rimuovere oggetti dalle raccolte e provare a indicizzare nuovamente il documento." | È consigliabile ridurre le dimensioni della raccolta complessa nel documento al di sotto del limite ed evitare un utilizzo elevato dello spazio di archiviazione.
| Problemi di connessione all'indice di destinazione (che persiste dopo i tentativi) perché il servizio è sottoposto a un altro carico, ad esempio l'esecuzione di query o l'indicizzazione. | Impossibile stabilire la connessione all'indice di aggiornamento. Il servizio di ricerca è sottoposto a un carico elevato. | [Scalabilità verticale del servizio di ricerca](search-capacity-planning.md)
| Il servizio di ricerca è sottoposto a patch per l'aggiornamento del servizio o è nel mezzo di una riconfigurazione della topologia. | Impossibile stabilire la connessione all'indice di aggiornamento. Il servizio di ricerca è attualmente inattivo/il servizio di ricerca è in fase di transizione. | Configurare il servizio con almeno 3 repliche per il 99,9% di disponibilità per [ogni documentazione del contratto](https://azure.microsoft.com/support/legal/sla/search/v1_0/) di servizioConfigure service with at least 3 replicas for 99.9% availability per SLA documentation
| Errore nella risorsa di calcolo/rete sottostante (raro)Failure in the underlying compute/networking resource (rare) | Impossibile stabilire la connessione all'indice di aggiornamento. Si è verificato un errore sconosciuto. | Configurare gli indicizzatori in modo che [vengano eseguiti in base](search-howto-schedule-indexers.md) a una pianificazione per il prelievo da uno stato di errore.
| Una richiesta di indicizzazione effettuata all'indice di destinazione non è stata riconosciuta entro un periodo di timeout a causa di problemi di rete. | Impossibile stabilire la connessione all'indice di ricerca in modo tempestivo. | Configurare gli indicizzatori in modo che [vengano eseguiti in base](search-howto-schedule-indexers.md) a una pianificazione per il prelievo da uno stato di errore. Inoltre, provare a ridurre la dimensione del [batch](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) dell'indicizzatore se questa condizione di errore persiste.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Errore: impossibile indicizzare il documento perché alcuni dei dati del documento non sono validi

Il documento è stato letto ed elaborato dall'indicizzatore, ma a causa di una mancata corrispondenza nella configurazione dei campi dell'indice e dei dati estratti ed elaborati dall'indicizzatore, non è stato possibile aggiungerlo all'indice di ricerca. Questo può accadere a causa di:

| Motivo | Dettagli/Esempio
| --- | ---
| Il tipo di dati dei campi estratti dall'indicizzatore non è compatibile con il modello di dati del campo dell'indice di destinazione corrispondente. | Il campo dati '_data_' nel documento con chiave '888' ha un valore non valido 'di tipo 'Edm.String''. Il tipo previsto era 'Collection(Edm.String)'. |
| Impossibile estrarre qualsiasi entità JSON da un valore stringa. | Impossibile analizzare il valore ' di tipo 'Edm.String'' del campo '_data_' come oggetto JSON. Errore: 'Dopo l'analisi di un valore è stato rilevato un carattere imprevisto: ''. Percorso '_percorso_', riga 1, posizione 3162.' |
| Impossibile estrarre una raccolta di entità JSON da un valore stringa.  | Impossibile analizzare il valore ' di tipo 'Edm.String'' del campo '_dati_' come matrice JSON. Errore: 'Dopo l'analisi di un valore è stato rilevato un carattere imprevisto: ''. Percorso '[0]', riga 1, posizione 27.' |
| È stato individuato un tipo sconosciuto nel documento di origine. | Impossibile indicizzare il tipo sconosciuto '_sconosciuto_ |
| Nel documento di origine è stata utilizzata una notazione incompatibile per i punti geografici. | I valori letterali stringa WKT POINT non sono supportati. Utilizzare i valori letterali punto GeoJson invece |

In tutti questi casi, fare riferimento a Tipi di [dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e Mapping dei tipi di dati per [gli indicizzatori](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) per assicurarsi di compilare correttamente lo schema dell'indice e di aver impostato i mapping dei [campi dell'indicizzatore](search-indexer-field-mappings.md)appropriati. Il messaggio di errore includerà dettagli che consentono di individuare l'origine della mancata corrispondenza.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Errore: Impossibile utilizzare i criteri di rilevamento delle modifiche integrati perché la tabella ha una chiave primaria composita

Ciò si applica alle tabelle SQL e in genere si verifica quando la chiave è definita come chiave composita o, quando la tabella ha definito un indice cluster univoco (come in un indice SQL, non un indice di Ricerca di Azure). Il motivo principale è che l'attributo chiave viene modificato per essere una chiave primaria composita nel caso di un [indice cluster univoco.](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15) In tal caso, assicurarsi che la tabella SQL non dispone di un indice cluster univoco o di eseguire il mapping del campo chiave a un campo che è garantito di non avere valori duplicati.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Errore: impossibile elaborare il documento entro la fase di esecuzione massima dell'indicizzatoreError: Could not process document within indexer max run time

Questo errore si verifica quando l'indicizzatore non è in grado di completare l'elaborazione di un singolo documento dall'origine dati entro il tempo di esecuzione consentito. [Il tempo](search-limits-quotas-capacity.md#indexer-limits) di esecuzione massimo è più breve quando vengono utilizzati i set di competenze. Quando si verifica questo errore, se si dispone di maxFailedItems impostato su un valore diverso da 0, l'indicizzatore ignora il documento in esecuzioni future in modo che l'indicizzazione può progredire. Se non è possibile permettersi di ignorare qualsiasi documento o se si sta vedendo questo errore in modo coerente, prendere in considerazione l'suddivisione dei documenti in documenti più piccoli in modo che sia possibile eseguire progressi parziali all'interno di una singola esecuzione dell'indicizzatore.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Errore: impossibile documento di progetto

Questo errore si verifica quando l'indicizzatore sta tentando di proiettare i [dati in un archivio informazioni](knowledge-store-projection-overview.md) e si è verificato un errore nel nostro tentativo di eseguire questa operazione.  Questo errore potrebbe essere coerente e risolvibile oppure potrebbe trattarsi di un errore temporaneo con il sink di output di proiezione che potrebbe essere necessario attendere e riprovare per risolvere.  Di seguito è riportato un insieme di stati di errore noti e le possibili soluzioni.

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| Impossibile aggiornare `'blobUri'` il blob di proiezione nel contenitore`'containerName'` |Il contenitore specificato non esiste. | L'indicizzatore controllerà se il contenitore specificato è stato creato in precedenza e lo creerà se necessario, ma esegue questo controllo solo una volta per ogni esecuzione dell'indicizzatore. Questo errore indica che un elemento ha eliminato il contenitore dopo questo passaggio.  Per risolvere questo errore, provare questo problema: lasciare in pace le informazioni sull'account di archiviazione, attendere il completamento dell'indicizzatore e quindi eseguire nuovamente l'indicizzatore. |
| Impossibile aggiornare `'blobUri'` il blob di proiezione nel contenitore`'containerName'` |Impossibile scrivere dati nella connessione di trasporto: una connessione esistente è stata chiusa con la chiusura con la rete dall'host remoto. | Si prevede che si tratti di un errore temporaneo con Archiviazione di Azure e pertanto deve essere risolto eseguendo nuovamente l'indicizzatore. Se si verifica questo errore in modo coerente, si prega di presentare un ticket di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) in modo che possa essere esaminato ulteriormente.  |
| Impossibile aggiornare la riga `'projectionRow'` nella tabella`'tableName'` | Il server è occupato. | Si prevede che si tratti di un errore temporaneo con Archiviazione di Azure e pertanto deve essere risolto eseguendo nuovamente l'indicizzatore. Se si verifica questo errore in modo coerente, si prega di presentare un ticket di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) in modo che possa essere esaminato ulteriormente.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Avviso: l'input delle competenze non è valido
Input per l'abilità mancante, di tipo errato o comunque non valido. Il messaggio di avviso indicherà l'impatto:
1) Impossibile eseguire abilità
2) Abilità eseguita ma potrebbe avere risultati imprevisti

Le abilità cognitive hanno input richiesti e input opzionali. Ad esempio, la [competenza di](cognitive-search-skill-keyphrases.md) `text`estrazione `languageCode`delle frasi chiave ha due input obbligatori, e nessun input facoltativo. Gli input di competenze personalizzate sono tutti considerati input facoltativi.

Se mancano degli input obbligatori o se un input non è del tipo corretto, la competenza viene ignorata e genera un avviso. Le abilità ignorate non generano output, quindi se altre abilità utilizzano output della abilità ignorata, possono generare avvisi aggiuntivi.

Se manca un input facoltativo, la competenza verrà comunque eseguita ma potrebbe produrre un output imprevisto a causa dell'input mancante.

In entrambi i casi, questo avviso potrebbe essere previsto a causa della forma dei dati. Ad esempio, se si dispone di un `firstName`documento `middleName`contenente `lastName`informazioni sulle persone con i campi `middleName`, e , è possibile disporre di alcuni documenti che non dispongono di una voce per . Se si `middleName` passa come input a una competenza nella pipeline, si prevede che in questo input di competenza manchi una parte del tempo. È necessario valutare i dati e lo scenario per determinare se è necessaria o meno un'azione in seguito a questo avviso.

Se si desidera fornire un valore predefinito in caso di input mancante, è possibile usare la [competenza Condizionale](cognitive-search-skill-conditional.md) per generare un valore predefinito e quindi usare l'output della [competenza Condizionale](cognitive-search-skill-conditional.md) come input di competenza.


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

| Motivo | Dettagli/Esempio | Risoluzione |
| --- | --- | --- |
| L'input delle competenze è del tipo errato | "L'input delle competenze `String`richiesto non era del tipo previsto. Nome: `text`, `/document/merged_content`Fonte: ."  "L'input delle competenze richiesto non era del formato previsto. Nome: `text`, `/document/merged_content`Fonte: ."  "Impossibile eseguire l'iterazione su non-array." `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`  "Impossibile `0` selezionare in `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`non matrice" | Alcune competenze prevedono input di tipi particolari, ad esempio [L'abilità Sentimento](cognitive-search-skill-sentiment.md) prevede `text` di essere una stringa. Se l'input specifica un valore non stringa, la competenza non viene eseguita e non genera output. Assicurati che il set di dati abbia valori di input uniformi nel tipo o usa una [competenza API Web personalizzata](cognitive-search-custom-skill-web-api.md) per pre-elaborare l'input. Se stai scorrendo l'abilità su una matrice, controlla `*` il contesto della competenza e l'input nelle posizioni corrette. In genere sia il contesto `*` che l'origine di input devono terminare con per le matrici. |
| L'input delle abilità non è presente | "Manca l'input di abilità richiesto. Nome: `text`, `/document/merged_content`Origine: " `/document/normalized_images/0/imageTags`"Valore mancante".  "Impossibile `0` selezionare `/document/pages` in `0`una matrice di lunghezza." | Se tutti i documenti ricevono questo avviso, molto probabilmente è presente un errore di battitura nei percorsi di input ed è necessario controllare le maiuscole/minuscole dei nomi delle proprietà, aggiuntive o mancanti `*` nel percorso e assicurarsi che i documenti dell'origine dati forniscano gli input necessari. |
| L'input del codice lingua delle competenze non è valido | L'input `languageCode` delle competenze ha i seguenti codici `X,Y,Z`lingua , almeno uno dei quali non è valido. | Vedi maggiori dettagli [qui sotto](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Attenzione: l'input di abilità 'languageCode' ha i seguenti codici di lingua 'X,Y,', almeno uno dei quali non è valido.
Uno o più valori passati `languageCode` nell'input facoltativo di una competenza a valle non sono supportati. Ciò può verificarsi se si passa l'output di [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) alle competenze successive e l'output è costituito da un numero di lingue superiore a quello supportato in tali competenze a valle.

Se si sa che il set di dati è tutto in `languageCode` una lingua, `defaultLanguageCode` è necessario rimuovere [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e l'input della competenza e utilizzare il parametro skill per tale competenza, presupponendo che la lingua sia supportata per tale competenza.

Se si sa che il set di dati contiene `languageCode` più lingue e pertanto sono necessari [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e input, è consigliabile aggiungere un [Oggetto ConditionalSkill](cognitive-search-skill-conditional.md) per filtrare il testo con lingue non supportate prima di passare il testo alla competenza a valle.  Ecco un esempio di come potrebbe apparire per EntityRecognitionSkill:

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
* [Linguaggi supportati](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) per Analisi del testo (per [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)e [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) dal traduttore (per [Text TranslationSkill](cognitive-search-skill-text-translation.md))
* [Testo SplitSkill](cognitive-search-skill-textsplit.md) Lingue supportate:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Avviso: l'input delle competenze è stato troncato
Le abilità cognitive hanno limiti alla lunghezza del testo che può essere analizzato contemporaneamente. Se l'input di testo di queste competenze supera tale limite, il testo verrà troncato per soddisfare il limite e quindi si esegua l'arricchimento su tale testo troncato. Ciò significa che la competenza viene eseguita, ma non su tutti i dati.

Nell'esempio LanguageDetectionSkill riportato di seguito, il `'text'` campo di input può attivare questo avviso se supera il limite di caratteri. È possibile trovare i limiti di inserimento delle competenze nella [documentazione relativa](cognitive-search-predefined-skills.md)alle competenze .

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

Se si desidera assicurarsi che tutto il testo venga analizzato, è consigliabile utilizzare la [competenza Dividi](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Avviso: la risposta alle competenze dell'API Web contiene avvisi
L'indicizzatore è stato in grado di eseguire una competenza nel set di competenze, ma la risposta dalla richiesta API Web ha indicato che si sono verificati avvisi durante l'esecuzione. Esaminare gli avvisi per comprendere l'impatto dei dati e se è necessaria o meno un'azione.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Avviso: la configurazione corrente dell'indicizzatore non supporta lo stato di avanzamento incrementale

Questo avviso si verifica solo per le origini dati Cosmos DB.

L'avanzamento incrementale durante l'indicizzazione assicura che, in caso di interruzione dell'esecuzione dell'indicizzatore a causa di errori temporanei o del limite del tempo di esecuzione, l'indicizzatore possa riprendere dal punto in cui è stato interrotto all'esecuzione successiva, invece di dovere ripetere dall'inizio l'indicizzazione dell'intera raccolta. Questo approccio risulta particolarmente importante in caso di indicizzazione di raccolte di grandi dimensioni.

La possibilità di riprendere un processo di indicizzazione non completato `_ts` si basa sulla disponibilità di documenti ordinati dalla colonna. L'indicizzatore utilizza il timestamp per determinare quale documento prelevare successivamente. Se `_ts` la colonna non è presente o se l'indicizzatore non è in grado di determinare se una query personalizzata viene ordinata in base a essa, l'indicizzatore inizia all'inizio e verrà visualizzato questo avviso.

È possibile eseguire l'override di questo comportamento, abilitando `assumeOrderByHighWatermarkColumn` lo stato di avanzamento incrementale ed eliminando questo avviso utilizzando la proprietà di configurazione.

Per ulteriori informazioni, vedere [Stato incrementale e query personalizzate](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Avviso: alcuni dati sono andati persi durante la proiezione. La riga 'X' nella tabella 'Y' ha la proprietà stringa ''' che era troppo lunga.

Il [servizio archiviazione tabelle](https://azure.microsoft.com/services/storage/tables) presenta limiti sulle dimensioni delle proprietà delle [entità.](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) Le stringhe possono avere al massimo 32.000 caratteri. Se viene proiettata una riga con una proprietà stringa più lunga di 32.000 caratteri, vengono mantenuti solo i primi 32.000 caratteri. Per risolvere questo problema, evitare di proiettare righe con proprietà stringa più lunghe di 32.000 caratteri.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Avviso: testo estratto in caratteri X troncato
Gli indicizzatori limitano la quantità di testo che può essere estratta da qualsiasi documento. Questo limite dipende dal piano tariffario: 32.000 caratteri per il livello gratuito, 64.000 per Basic, 4 milioni per Standard, 8 milioni per Standard S2 e 16 milioni per Standard S3. Il testo troncato non verrà indicizzato. Per evitare questo avviso, provare a suddividere i documenti con grandi quantità di testo in più documenti più piccoli. 

Per ulteriori informazioni, consultate [Limiti degli indicizzatori.](search-limits-quotas-capacity.md#indexer-limits)

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Avviso: Impossibile eseguire il mapping del campo di output 'X' all'indice di ricerca
I mapping dei campi di output che fanno riferimento a dati inesistenti/null genereranno avvisi per ogni documento e genereranno un campo indice vuoto. Per risolvere questo problema, controllare i percorsi di origine del mapping dei campi di output per eventuali errori di battitura oppure impostare un valore predefinito utilizzando la [competenza condizionale](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Avviso: il criterio di rilevamento delle modifiche ai dati è configurato per l'utilizzo della colonna chiave 'X'
I criteri di [rilevamento delle modifiche](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) dei dati hanno requisiti specifici per le colonne utilizzate per rilevare le modifiche. Uno di questi requisiti è che questa colonna viene aggiornata ogni volta che viene modificato l'elemento di origine. Un altro requisito è che il nuovo valore per questa colonna sia maggiore del valore precedente. Le colonne chiave non soddisfano questo requisito perché non cambiano a ogni aggiornamento. Per risolvere questo problema, selezionare una colonna diversa per il criterio di rilevamento delle modifiche.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Avviso: il testo del documento sembra essere codificato UTF-16, ma manca un indicatore per l'ordine dei byte

Le modalità di [analisi dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) devono sapere come viene codificato il testo prima di analizzarlo. I due modi più comuni di codifica del testo sono UTF-16 e UTF-8. UTF-8 è una codifica a lunghezza variabile in cui ogni carattere è compreso tra 1 byte e 4 byte di lunghezza. UTF-16 è una codifica a lunghezza fissa in cui ogni carattere è lungo 2 byte. UTF-16 ha due diverse varianti, "big endian" e "little endian". La codifica del testo è determinata da un "byte order mark", una serie di byte prima del testo.

| Codifica | Byte order mark |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 Little Endian | 0xFF 0xFE (0xFF 0xFE) |
| UTF-8 | 0xEF 0xBB 0xBF |

Se non è presente alcun indicatore dell'ordine dei byte, si presuppone che il testo venga codificato come UTF-8.

Per risolvere questo avviso, determinare la codifica del testo per questo BLOB e aggiungere il contrassegno dell'ordine dei byte appropriato.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Attenzione: la raccolta Cosmos DB 'X' ha una politica di indicizzazione differita. Alcuni dati potrebbero andare persi

Le raccolte con criteri di indicizzazione [lazy](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) non possono essere interrogate in modo coerente, determinando la mancata immissione dei dati nell'indicizzatore. Per risolvere questo avviso, modificare i criteri di indicizzazione in coerente.
