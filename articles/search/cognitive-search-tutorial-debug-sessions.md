---
title: 'Esercitazione: Usare le sessioni di debug per diagnosticare, correggere ed eseguire il commit delle modifiche apportate al set di competenze'
titleSuffix: Azure Cognitive Search
description: Le sessioni di debug (anteprima) forniscono un'interfaccia basata su portale per la valutazione e la correzione di problemi ed errori nel set di competenze
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: b6164ef955ac92a7ef8776e560ea4d3a92abaf8d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935977"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Esercitazione: Diagnosticare, correggere ed eseguire il commit delle modifiche apportate al set di competenze

In questo articolo si userà il portale di Azure per accedere alle sessioni di debug per correggere i problemi relativi al set di competenze fornito. Il set di competenze presenta alcuni errori che devono essere corretti. In questa esercitazione verrà eseguita una sessione di debug per identificare e risolvere i problemi relativi agli input e agli output delle competenze.

> [!Important]
> Le sessioni di debug sono funzionalità di anteprima fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) o usare la sottoscrizione corrente
> * Un'istanza del servizio Ricerca cognitiva di Azure
> * Un account di archiviazione Azure
> * [App desktop Postman](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Creare i servizi e caricare i dati

Questa esercitazione usa i servizi Ricerca cognitiva di Azure e Archiviazione di Azure.

* [Scaricare i dati di esempio](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) costituiti da 19 file.

* [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal) o [trovare un account esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Per evitare addebiti dovuti alla larghezza di banda, scegliere la stessa area di Ricerca cognitiva di Azure.
   
   Scegliere il tipo di account Archiviazione V2 (utilizzo generico V2).

* Aprire le pagine dei servizi di archiviazione e creare un contenitore. È consigliabile specificare il livello di accesso "privato". Assegnare al contenitore il nome `clinicaltrialdataset`.

* Nel contenitore fare clic su **Carica** per caricare i file di esempio scaricati e decompressi nel primo passaggio.

* [Creare un servizio di Ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). È possibile usare un servizio gratuito per questo avvio rapido.

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-postman/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="create-data-source-skillset-index-and-indexer"></a>Creare l'origine dati, il set di competenze, l'indice e l'indicizzatore

In questa sezione verranno usati Postman e una raccolta fornita per creare l'origine dati, il set di competenze, l'indice e l'indicizzatore del servizio di ricerca.

1. Se non si ha Postman, è possibile [scaricare l'app desktop Postman qui](https://www.getpostman.com/).
1. [Scaricare la raccolta DebugSessions di Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Avviare Postman
1. In **Files** > **New** (File > Nuovo) selezionare la raccolta da importare.
1. Dopo aver importato la raccolta, espandere l'elenco delle azioni (...).
1. Fare clic su **Modifica**.
1. Immettere il nome di searchService (ad esempio, se l'endpoint è `https://mydemo.search.windows.net`, il nome del servizio sarà "`mydemo`").
1. Immettere l'apiKey con la chiave primaria o secondaria del servizio di ricerca.
1. Immettere la storageConnectionString dalla pagina Chiavi dell'account di archiviazione di Azure.
1. Immettere il containerName del contenitore creato nell'account di archiviazione.

> [!div class="mx-imgBorder"]
> ![Modificare le variabili in Postman](media/cognitive-search-debug/postman-enter-variables.png)

La raccolta contiene quattro diverse chiamate REST che vengono usate per completare questa sezione.

La prima chiamata crea l'origine dati. `clinical-trials-ds`. La seconda chiamata crea il set di competenze, `clinical-trials-ss`. La terza chiamata crea l'indice, `clinical-trials`. La quarta e ultima chiamata crea l'indicizzatore, `clinical-trials-idxr`. Una volta completate tutte le chiamate della raccolta, chiudere Postman e tornare al portale di Azure.

> [!div class="mx-imgBorder"]
> ![Uso di Postman per creare l'origine dati](media/cognitive-search-debug/postman-create-data-source.png)

## <a name="check-the-results"></a>Controllare i risultati

Il set di competenze contiene alcuni errori comuni. In questa sezione, l'esecuzione di una query vuota per restituire tutti i documenti visualizzerà diversi errori. Nei passaggi successivi i problemi verranno risolti usando una sessione di debug.

1. Passare al servizio di ricerca nel portale di Azure. 
1. Selezionare la scheda **Indici**. 
1. Selezionare l'indice `clinical-trials`.
1. Fare clic su **Cerca** per eseguire una query vuota. 

Al termine della ricerca, nella finestra vengono visualizzati due campi senza dati, "organizations" e "locations". Seguire la procedura per individuare tutti i problemi prodotti dal set di competenze.

1. Tornare alla pagina Panoramica del servizio di ricerca.
1. Selezionare la scheda **Indicizzatori**. 
1. Fare clic su `clinical-trials-idxr` e selezionare la notifica degli avvisi. 

Ci sono diversi problemi con i mapping dei campi di output delle proiezioni e nella pagina tre sono presenti avvisi perché uno o più input delle competenze non sono validi.

Tornare alla schermata Panoramica del servizio di ricerca.

## <a name="start-your-debug-session"></a>Avviare la sessione di debug

> [!div class="mx-imgBorder"]
> ![Avviare una nuova sessione di debug](media/cognitive-search-debug/new-debug-session-screen-required.png)

1. Fare clic sulla scheda Sessioni di debug (anteprima).
1. Selezionare +NewDebugSession
1. Assegnare un nome alla sessione. 
1. Connettere la sessione al proprio account di archiviazione. 
1. Specificare il nome dell'indicizzatore. L'indicizzatore contiene riferimenti all'origine dati, al set di competenze e all'indice.
1. Per il documento di debug accettare l'impostazione predefinita, che corrisponde al primo documento della raccolta. 
1. Selezionare **Salva** per salvare la sessione. Salvando la sessione viene avviata la pipeline di arricchimento tramite intelligenza artificiale in base a quanto definito dal set di competenze.

> [!Important]
> Una sessione di debug funziona solo con un singolo documento. È possibile selezionare un documento specifico nel set di dati, altrimenti la sessione seleziona il primo documento per impostazione predefinita.

> [!div class="mx-imgBorder"]
> ![Nuova sessione di debug avviata](media/cognitive-search-debug/debug-execution-complete1.png)

Al termine dell'esecuzione della sessione di debug, per impostazione predefinita la sessione si apre sulla scheda AI Enrichments (Arricchimenti tramite intelligenza artificiale), con la voce Skill Graph (Grafo delle competenze) evidenziata.

+ Il grafo delle competenze fornisce una gerarchia visiva del set di competenze e il suo ordine di esecuzione dall'alto verso il basso. Le competenze affiancate nel grafo vengono eseguite in parallelo. La codifica a colori delle competenze nel grafo indica i tipi di competenze in esecuzione nel set di competenze. Nell'esempio le competenze verdi sono testo e la competenza rossa è la visione. Se si fa clic su una singola competenza nel grafo, nel riquadro destro della finestra della sessione vengono visualizzati i dettagli di quell'istanza della competenza. Le impostazioni della competenza, un editor JSON, i dettagli dell'esecuzione e gli errori ed avvisi sono tutti disponibili per la revisione e la modifica.
+ La scheda Enriched Data Structure (Struttura dei dati arricchiti) contiene informazioni dettagliate sui nodi dell'albero di arricchimento generati dalle competenze dei contenuti del documento di origine.

La scheda Errori/Avvisi fornirà un elenco molto più ridotto rispetto a quello visualizzato in precedenza, poiché visualizza solo i dettagli degli errori relativi a un singolo documento. Come con l'elenco visualizzato dall'indicizzatore, è possibile fare clic su un messaggio di avviso per visualizzare i dettagli corrispondenti.

## <a name="fix-missing-skill-input-value"></a>Correggere un valore di input della competenza mancante

La scheda Errori/Avvisi contiene un errore per un'operazione contrassegnata come `Enrichment.NerSkillV2.#1`. I dettagli dell'errore spiegano che c'è un problema con il valore di input della competenza "/document/languageCode". 

1. Tornare alla scheda AI Enrichments (Arricchimenti tramite intelligenza artificiale).
1. Fare clic su **Skill Graph** (Grafo delle competenze).
1. Fare clic sulla competenza #1 per visualizzarne i dettagli nel riquadro a destra.
1. Individuare l'input per "languageCode".
1. Selezionare il simbolo **</>** all'inizio della riga e aprire l'analizzatore di espressioni.
1. Fare clic sul pulsante **Valuta** per verificare che l'espressione restituisca un errore. Verrà confermato che la proprietà "languageCode" non è un input valido.

> [!div class="mx-imgBorder"]
> ![Analizzatore di espressioni](media/cognitive-search-debug/expression-evaluator-language.png)

È possibile cercare questo errore nella sessione in due modi. Il primo consiste nell'esaminare la provenienza dell'input: quale competenza nella gerarchia dovrebbe produrre questo risultato? La scheda Esecuzioni nel riquadro dei dettagli della competenza dovrebbe visualizzare l'origine dell'input. L'assenza di origine indica un errore di mapping del campo.

1. Fare clic sulla scheda **Esecuzioni**.
1. In INPUTS trovare "languageCode". Per questo input non è elencata alcuna origine. 
1. Nel riquadro sinistro passare alla scheda Enriched Data Structure (Struttura dei dati arricchiti). Non è presente un percorso mappato corrispondente a "languageCode".

> [!div class="mx-imgBorder"]
> ![Struttura dei dati arricchiti](media/cognitive-search-debug/enriched-data-structure-language.png)

È presente un percorso mappato per "language". C'è quindi un errore di ortografia nelle impostazioni della competenza. Per correggerlo, è necessario aggiornare l'espressione "/document/language" nella competenza #1.

1. Aprire l'analizzatore di espressioni **</>** per il percorso "language".
1. Copiare l'espressione. Chiudere la finestra.
1. Passare alle impostazioni delle competenze per la competenza #1 e aprire l'analizzatore di espressioni **</>** per l'input "languageCode".
1. Incollare il nuovo valore "/document/language" nella casella Espressione e fare clic su **Valuta**.
1. Dovrebbe essere visualizzato l'input corretto "en". Fare clic su Applica per aggiornare l'espressione.
1. Fare clic su **Salva** nel riquadro dei dettagli della competenza a destra.
1. Fare clic su **Esegui** nel menu della finestra della sessione. Verrà avviata un'altra esecuzione del set di competenze usando il documento. 

Al termine dell'esecuzione della sessione di debug, fare clic sulla scheda Errori/Avvisi, dove si vedrà che l'errore "Enrichment.NerSkillV2.#1" è scomparso. Ci sono però ancora due avvisi che indicano che il servizio non è stato in grado di eseguire il mapping dei campi di output organizations e locations all'indice di ricerca. Mancano i valori "/document/merged_content/organizations" e "/document/merged_content/locations".

## <a name="fix-missing-skill-output-values"></a>Correggere i valori di output della competenza mancanti

> [!div class="mx-imgBorder"]
> ![Errori e avvisi](media/cognitive-search-debug/warnings-missing-value-locations-organizations.png)

Mancano dei valori di output da una competenza. Per identificare la competenza con l'errore, passare alla scheda Enriched Data Structure (Struttura dei dati arricchiti), trovare il nome del valore e osservare la relativa origine. I valori organizations e locations mancanti sono output della competenza #1. Aprendo l'analizzatore di espressioni </> per ogni percorso, le espressioni verranno visualizzate rispettivamente come "/document/content/organizations" e "/document/content/locations".

> [!div class="mx-imgBorder"]
> ![Entità organizations dell'analizzatore di espressioni](media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png)

L'output di queste entità è vuoto e non dovrebbe esserlo. Quali sono gli input che producono questo risultato?

1. Passare a **Skill Graph** (Grafo delle competenze) e selezionare la competenza #1.
1. Selezionare la scheda **Esecuzioni** nel riquadro dei dettagli della competenza a destra.
1. Aprire l'analizzatore di espressioni **</>** per la proprietà INPUTS "text".

> [!div class="mx-imgBorder"]
> ![Input per la competenza text](media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png)

Il risultato visualizzato per questo input non sembra un input di testo, ma piuttosto un'immagine racchiusa tra nuove righe. La mancanza di testo indica che non ci sono entità identificabili. In base alla gerarchia delle competenze, il contenuto viene prima elaborato dalla competenza #6 (OCR) e quindi viene passato alla competenza #5 (Merge). 

1. Selezionare la competenza #5 (Merge) in **Skill Graph** (Grafo delle competenze).
1. Selezionare la scheda **Esecuzioni** nel riquadro dei dettagli della competenza a destra e aprire l'analizzatore di espressioni **</>** per la proprietà OUTPUTS "mergedText".

> [!div class="mx-imgBorder"]
> ![Output della competenza Merge](media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png)

Qui il testo è associato all'immagine. Osservando l'espressione "/document/merged_content" si può notare l'errore nei percorsi "organizations" e "locations"per la competenza #1. Invece di usare "/document/content" dovrebbe usare "/document/merged_content" per la proprietà INPUTS "text".

1. Copiare l'espressione dell'output "mergedText" e chiudere la finestra dell'analizzatore di espressioni.
1. Selezionare la competenza #1 in **Skill Graph** (Grafo delle competenze).
1. Selezionare la scheda **Skill Settings** (Impostazioni competenza) nel riquadro dei dettagli della competenza a destra.
1. Aprire l'analizzatore di espressioni **</>** per la proprietà INPUTS "text".
1. Incollare la nuova espressione nella casella. Fare clic su **Valuta**.
1. Dovrebbe essere visualizzato l'input corretto con il testo aggiunto. Fare clic su **Applica** per aggiornare le impostazioni della competenza.
1. Fare clic su **Salva** nel riquadro dei dettagli della competenza a destra.
1. Fare clic su **Esegui** nel menu della finestra della sessione. Verrà avviata un'altra esecuzione del set di competenze usando il documento.

Al termine dell'esecuzione dell'indicizzatore, gli errori sono ancora presenti. Tornare alla competenza #1 per individuare la causa. L'input della competenza è stato corretto da "content" a "merged_content". Quali sono gli output per queste entità nella competenza?

1. Selezionare la scheda **AI Enrichments** (Arricchimenti tramite intelligenza artificiale).
1. Selezionare **Skill Graph** (Grafo delle competenze) e fare clic sulla competenza #1.
1. In **Skill Settings** (Impostazioni competenza) individuare "outputs".
1. Aprire l'analizzatore di espressioni **</>** per l'entità "organizations".

> [!div class="mx-imgBorder"]
> ![Output dell'entità organizations](media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png)

La valutazione del risultato dell'espressione restituisce il risultato corretto. La competenza lavora per identificare il valore corretto dell'entità "organizations". Tuttavia il mapping dell'output nel percorso dell'entità continua a generare un errore. Confrontando il percorso di output nella competenza con il percorso di output nell'errore, si nota che la competenza padre delle entità outputs, organizations e locations è nel nodo /document/content, mentre il mapping dei campi di output prevede che i risultati siano assegnati a una competenza padre nel nodo /document/merged_content. Nel passaggio precedente l'input è stato modificato da "/document/content" a "/document/merged_content". Per assicurare che l'output venga generato con il contesto corretto, occorre modificare il contesto nelle impostazioni della competenza.

1. Selezionare la scheda **AI Enrichments** (Arricchimenti tramite intelligenza artificiale).
1. Selezionare **Skill Graph** (Grafo delle competenze) e fare clic sulla competenza #1.
1. In **Skill Settings** (Impostazioni competenza) individuare "context".
1. Fare doppio clic sull'impostazione relativa a "context" e modificarla in "/document/merged_content".
1. Fare clic su **Salva** nel riquadro dei dettagli della competenza a destra.
1. Fare clic su **Esegui** nel menu della finestra della sessione. Verrà avviata un'altra esecuzione del set di competenze usando il documento.

> [!div class="mx-imgBorder"]
> ![Correzione del contesto nell'impostazione della competenza](media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png)

Tutti gli errori sono stati risolti.

## <a name="commit-changes-to-the-skillset"></a>Eseguire il commit delle modifiche apportate al set di competenze

Quando è stata avviata la sessione di debug, il servizio di ricerca ha creato una copia del set di competenze. Questa copia è stata creata affinché le modifiche apportate non influissero sul sistema di produzione. Una volta terminato il debug del set di competenze, è possibile eseguire il commit delle correzioni nel sistema di produzione, sovrascrivendo il set di competenze originale. Se si vuole continuare ad apportare modifiche al set di competenze senza alcun impatto sul sistema di produzione, è possibile salvare la sessione di debug per riaprirla in un secondo momento.

1. Fare clic su **Commit changes** (Esegui il commit delle modifiche) nel menu principale di Sessioni di debug.
1. Fare clic su **OK** per confermare che si vuole aggiornare il set di competenze.
1. Chiudere la sessione di debug e selezionare la scheda **Indicizzatori**.
1. Aprire "clinical-trials-idxr".
1. Fare clic su **Reimposta**.
1. Fare clic su **Esegui**. Fare clic su **OK** per confermare.

Al termine dell'esecuzione dell'indicizzatore, dovrebbe essere visualizzato un segno di spunta verde e la parola Success (Operazione riuscita) accanto al timestamp dell'esecuzione più recente nella scheda Cronologia esecuzione. Per verificare che le modifiche siano state applicate:

1. Uscire da **Indicizzatore** e selezionare la scheda **Indice**.
1. Aprire l'indice "clinical-trials" e quindi nella scheda Esplora ricerche fare clic su **Cerca**.
1. Nella finestra dei risultati si dovrebbe vedere che le entità organizations e locations sono ora popolate con i valori previsti.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui set di competenze](./cognitive-search-working-with-skillsets.md)
> [Altre informazioni sull'arricchimento incrementale e la memorizzazione nella cache](./cognitive-search-incremental-indexing-conceptual.md)