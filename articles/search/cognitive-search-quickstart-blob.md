---
title: 'Guida introduttiva: Creare un indice arricchito con intelligenza artificiale nel portale di Azure - Ricerca di Azure'
description: Estrazione dei dati, linguaggio naturale e competenze di elaborazione delle immagine in un portale di indicizzazione di Ricerca di Azure, usando il portale di Azure e dati di esempio.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.openlocfilehash: a4654e5895e9c7768b9fa6b975ef848294bcc8cc
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648914"
---
# <a name="quickstart-create-an-ai-indexing-pipeline-using-cognitive-skills-in-azure-search"></a>Guida introduttiva: Creare una pipeline di indicizzazione di intelligenza artificiale con competenze cognitive in Ricerca di Azure

Ricerca di Azure si integra con [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/) aggiungendo estrazione di contenuto, elaborazione del linguaggio naturale e competenze di elaborazione di immagini a una pipeline di indicizzazione di Ricerca di Azure, semplificando le ricerche di contenuto non ricercabile o non strutturato. 

Molte risorse di Servizi cognitivi, ad esempio [OCR](cognitive-search-skill-ocr.md), [rilevamento della lingua](cognitive-search-skill-language-detection.md) e [riconoscimento di entità](cognitive-search-skill-entity-recognition.md), solo per citarne alcune, possono essere collegate a un processo di indicizzazione. Gli algoritmi di intelligenza artificiale di Servizi cognitivi vengono usati per individuare modelli, funzionalità e caratteristiche nei dati di origine, restituendo strutture e contenuto testuale che è possibile usare nella soluzioni di ricerca full-text basate su Ricerca di Azure.

In questo argomento di avvio rapido è possibile creare la prima pipeline di arricchimento nel [portale di Azure](https://portal.azure.com) prima di scrivere una singola riga di codice:

> [!div class="checklist"]
> * Iniziare con i dati di esempio nell'archivio BLOB di Azure
> * Configurare l'[**Importazione guidata dati**](search-import-data-portal.md) per l'indicizzazione cognitiva e l'arricchimento 
> * Eseguire la procedura guidata (una competenza entità rileva gli utenti, la posizione e le organizzazioni)
> * Usare [**Esplora ricerche**](search-explorer.md) per eseguire query sui dati arricchiti

Questa guida di avvio rapido viene eseguita con il servizio gratuito, ma il numero di transazioni gratuite è limitato a 20 documenti al giorno. Se si vuole eseguire la guida più di una volta al giorno, usare un set di file più piccolo in modo da far rientrare più esecuzioni.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido.

I [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/) forniscono l'intelligenza artificiale. Questa guida di avvio rapido include la procedura per aggiungere queste risorse inline, quando si specifica la pipeline. Non è necessario configurare account in anticipo.

Per fornire gli input per la pipeline di indicizzazione, sono necessari i servizi di Azure. È possibile usare qualsiasi origine dati supportata dagli [indicizzatori di Ricerca di Azure](search-indexer-overview.md), ad eccezione di archiviazione tabelle di Azure, che non è supportata per l'indicizzazione dell'intelligenza artificiale. In questa guida si usa [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) come contenitore per i file delle origini dati. 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurare il servizio BLOB di Azure e caricare i dati di esempio

1. [Scaricare i dati di esempio](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) costituiti da un piccolo set di file di tipi diversi. 

1. [Iscriversi ad archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), creare un account di archiviazione, aprire le pagine dei servizi BLOB e creare un contenitore.  Creare l'account di archiviazione nella stessa area di Ricerca di Azure.

1. Nel contenitore creato fare clic su **Carica** per caricare i file di esempio nel passaggio precedente.

   ![File di origine nell'archivio BLOB di Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Creare la pipeline di arricchimento

Tornare alla pagina dashboard del servizio Ricerca di Azure e fare clic su **Importa dati** sulla barra dei comandi per impostare l'arricchimento cognitivo in quattro passaggi.

  ![Comando Importa dati](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

In **Definisci la connessione ai dati** scegliere **Archiviazione BLOB di Azure** e selezionare l'account e il contenitore creati. Assegnare un nome origine dati e utilizzare i valori predefiniti per il resto. 

  ![Configurazione del BLOB di Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

Passare alla pagina successiva.

  ![Pulsante Pagina successiva per la ricerca cognitiva](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>Passaggio 2: Aggiungere competenze cognitive

Successivamente, aggiungere i passaggi di arricchimento alla pipeline di indicizzazione. Se non si ha una risorsa di Servizi cognitivi, è possibile iscriversi per una versione gratuita che offre 20 transazioni al giorno. I dati di esempio sono costituiti da 14 file, quindi l'allocazione giornaliera risulterà usata quasi completamente dopo aver eseguito questa procedura guidata.

1. Espandere **Collega Servizi cognitivi** per visualizzare le opzioni per le API Servizi cognitivi. Ai fini di questa esercitazione, è possibile usare la risorsa **Gratuita**.

   ![Collega Servizi cognitivi](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Espandere **Aggiungi arricchimenti** e selezionare le competenze che eseguono l'elaborazione del linguaggio naturale. Per questa Guida introduttiva, scegliere il riconoscimento entità per gli utenti, le organizzazioni e i percorsi.

   ![Collega Servizi cognitivi](media/cognitive-search-quickstart-blob/skillset.png)

   Il portale offre competenze predefinite per l'analisi del testo e l'elaborazione OCR. Nel portale, un set di competenze agisce su un campo di origine singolo. Ciò potrebbe sembrare una destinazione di piccole dimensioni, ma per i BLOB di Azure il campo `content` contiene la maggior parte del documento BLOB (ad esempio, un documento Word o un di mazzo PowerPoint). Di conseguenza, questo campo è un input ideale perché contiene tutto il contenuto del BLOB.

3. Passare alla pagina successiva.

   ![Pagina successiva: personalizzazione indice](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> L'elaborazione del linguaggio naturale viene eseguita tramite il contenuto di testo nel set di dati di esempio. Poiché l'opzione OCR non è stata selezionata, i file JPEG e PNG trovati nel set di dati di esempio non verranno elaborati in questo argomento di avvio rapido. 

### <a name="step-3-configure-the-index"></a>Passaggio 3: Configurare l'indice

La procedura guidata in genere è in grado di dedurre un indice predefinito. In questo passaggio è possibile visualizzare lo schema dell'indice generato e potenzialmente rivedere le impostazioni. Di seguito è riportato l'indice predefinito creato per il set di dati dimostrativi BLOB.

Per questa Guida introduttiva, la procedura guidata ha imposta valori predefiniti ragionevoli in modo efficace: 

+ Il nome predefinito è *azureblob-index* in base al tipo di origine dati. 

+ I campi predefiniti si basano sul campo dati di origine iniziale (`content`) e i campi di output ( `people`, `organizations` e `locations`) creati dalla pipeline cognitiva. I tipi di dati predefiniti vengono dedotti dai metadati e dal campionamento dei dati.

+ La chiave predefinita è *metadata_storage_path* (questo campo contiene valori univoci).

+ Gli attributi predefiniti per questi campi sono **Recuperabile** e **Ricercabile**. **Ricercabile** indica un campo in cui è possibile eseguire ricerche. **Recuperabile** significa che può essere restituito nei risultati. La procedura guidata presuppone che si voglia rendere questi campi recuperabili e ricercabili perché sono stati creati con un insieme di competenze.

  ![Campi indice](media/cognitive-search-quickstart-blob/index-fields.png)

Osservare il testo barrato e il punto interrogativo sull'attributo **Recuperabile** accanto al campo `content`. Per i documenti BLOB a elevato utilizzo di testo, il campo `content` contiene la maggior parte del contenuto del file, eseguito potenzialmente in migliaia di righe. Se è necessario passare il contenuto del file al codice client, assicurarsi che il campo **Recuperabile** resti selezionato. In caso contrario, prendere in considerazione la cancellazione di questo attributo in `content` se gli elementi estratti (`people`, `organizations` e `locations`) sono sufficienti per gli scopi desiderati.

Contrassegnare un campo come **Recuperabile** non significa che il campo *debba* essere presente nei risultati della ricerca. È possibile controllare con precisione la composizione dei risultati della ricerca usando il parametro di query **$select** per specificare i campi da includere. Per i campi con grandi quantità di testo come `content`, il parametro **$select** è la soluzione per fornire risultati della ricerca gestibili agli utenti umani dell'applicazione, garantendo allo stesso tempo che il codice abbia accesso a tutte le informazioni necessarie attraverso l'attributo **Recuperabile**.
  
Passare alla pagina successiva.

  ![Pagina successiva: creazione indicizzatore](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>Passaggio 4: Configurare l'indicizzatore

L'indicizzatore è una risorsa di alto livello che guida il processo di indicizzazione. Specifica il nome dell'origine dati, l'indice di destinazione e la frequenza di esecuzione. Il risultato finale dell'**Importazione guidata dati** è sempre un indicizzatore che è possibile eseguire ripetutamente.

Nella pagina **Indicizzatore** si può accettare il nome predefinito e usare l'opzione di pianificazione **Esegui una volta** per l'esecuzione immediata. 

  ![Definizione di indicizzatore](media/cognitive-search-quickstart-blob/indexer-def.png)

Fare clic su **Invia** per creare e contemporaneamente eseguire l'indicizzatore.

## <a name="monitor-indexing"></a>Monitorare l'indicizzazione

I passaggi di arricchimento richiedono più tempo rispetto all'indicizzazione tipica basata su testo. La procedura guidata dovrebbe aprire l'elenco degli indicizzatori nella pagina di panoramica in modo che sia possibile monitorare lo stato di avanzamento. In caso contrario, passare alla pagina Panoramica e fare clic su **Indicizzatori**.

L'avviso viene visualizzato perché i file JPG e PNG sono file di immagine e la competenza OCR è stata omessa da questa pipeline. Verranno anche visualizzate notifiche di troncamento. Ricerca di Azure limita l'estrazione a 32.000 caratteri nel livello gratuito.

  ![Notifica di ricerca di Azure](./media/cognitive-search-quickstart-blob/indexer-notification.png)

L'indicizzazione e l'arricchimento possono richiedere tempo, per questo motivo sono consigliati set di dati più piccoli per l'esplorazione anticipata. 

## <a name="query-in-search-explorer"></a>Eseguire query in Esplora ricerche

Dopo aver creato un indice, è possibile inviare query per la restituzione di documenti dall'indice. Nel portale usare **Esplora ricerche** per eseguire query e visualizzare i risultati. 

1. Nella pagina del dashboard del servizio di ricerca fare clic su **Esplora ricerche** sulla barra dei comandi.

1. Selezionare **Cambia indice** in alto per selezionare l'indice creato.

1. Immettere una stringa di ricerca per una query nell'indice, ad esempio `search=Microsoft&searchFields=organizations`.

I risultati vengono restituiti in formato JSON, che può essere dettagliato e difficile da leggere, soprattutto in documenti di grandi dimensioni proveniente da BLOB di Azure. Se non è possibile analizzare facilmente i risultati, usare CTRL + F per cercare all'interno dei documenti. Per questa query, è possibile cercare termini specifici all'interno del codice JSON. 

CTRL + F consente inoltre di determinare il numero di documenti presenti in un determinato set di risultati. Per i BLOB di Azure, il portale sceglie "metadata_storage_path" come chiave perché ogni valore è univoco per il documento. Usando CTRL-F, cercare "metadata_storage_path" per ottenere il conteggio dei documenti. 

  ![Esempio di Esplora ricerche](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Risultati

Il primo esercizio di indicizzazione arricchita con risorse cognitive è stato completato. Lo scopo di questa Guida introduttiva era quello di introdurre i concetti importanti e assistere l'utente nella procedura guidata per creare un prototipo di soluzione di ricerca cognitiva usando i propri dati.

Alcuni concetti chiave ci auguriamo l'utente abbia compreso includono la dipendenza da origini dati di Azure. L'arricchimento della ricerca cognitiva è associato a indicizzatori e gli indicizzatori sono specifici di Azure e dell'origine. Anche se in questa Guida introduttiva viene usato Archivio BLOB di Azure, sono possibili altre origini dati di Azure. Per altre informazioni, vedere [Indicizzatori in Ricerca di Azure](search-indexer-overview.md).

Un altro concetto importante è che le competenze vengono eseguite tramite campi di input. Nel portale, è necessario scegliere un singolo campo di origine per tutte le competenze. Nel codice, gli input possono essere altri campi o l'output di una competenza upstream.

 Viene eseguito il mapping degli input di una competenza a un campo di output in un indice. Internamente, il portale configura [annotazioni](cognitive-search-concept-annotations-syntax.md) e definisce un [set di competenze](cognitive-search-defining-skillset.md), stabilendo l'ordine delle operazioni e il flusso generale. Questi passaggi sono nascosti nel portale, ma quando si avvia la scrittura di codice, questi concetti diventano importanti.

Infine, si è appreso che la visualizzazione dei risultati viene ottenuta eseguendo una query dell'indice. Infine, Ricerca di Azure offre un indice ricercabile, in cui è possibile eseguire una query utilizzando la [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) oppure [completamente estesa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Un indice che contiene campi arricchiti è come qualsiasi altro. Se si intende incorporare [analizzatori personalizzati](search-analyzers.md) o standard, [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [sinonimi](search-synonyms.md), [filtri con facet](search-filters-facets.md), ricerca geografica o qualsiasi altra funzionalità di Ricerca di Azure, è possibile farlo.

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

In base al modo in cui si è effettuato il provisioning della risorsa di Servizi cognitivi, è possibile usare sperimentare con l'indicizzazione e l'arricchimento rieseguendo la procedura guidata con diverse competenze e campi di origine dati. Per ripetere questa procedura, eliminare l'indice e l'indicizzatore, quindi ricreare l'indicizzatore con una nuova combinazione di selezioni.

+ In **Panoramica** > **Indici** selezionare l'indice creato e quindi fare clic su **Elimina**.

+ In **Panoramica** fare doppio clic nel riquadro **Indicizzatori**. Individuare l'indicizzatore creato ed eliminarlo.

In alternativa, riutilizzare i dati di esempio e i servizi creati per apprendere come eseguire stesse attività a livello di codice nella prossima esercitazione. 

> [!div class="nextstepaction"]
> [Esercitazione: Informazioni sulle API REST della ricerca cognitiva](cognitive-search-tutorial-blob.md)
