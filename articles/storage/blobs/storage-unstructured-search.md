---
title: Cercare dati non strutturati nell'archiviazione cloud di Azure
description: Ricerca di dati non strutturati tramite Ricerca di Azure.
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Cercare dati non strutturati nell'archiviazione cloud

In questa esercitazione viene illustrato come eseguire una ricerca di dati non strutturati tramite [Ricerca di Azure](../../search/search-what-is-azure-search.md) usando i dati archiviati nel BLOB di Azure. I dati non strutturati sono dati non organizzati in modo predefinito o che non dispongono di un modello di dati. Un esempio è rappresentato da un file con estensione TXT.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di risorse
> * Creare un account di archiviazione
> * Creare un contenitore
> * Caricare dati nel contenitore
> * Creare un servizio di ricerca tramite il portale
> * Usare il servizio di ricerca per cercare il contenitore

## <a name="download-the-sample"></a>Scaricare l'esempio

Un set di dati di esempio è stato preparato per l'utente. **Scaricare [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** e decomprimerlo nella relativa cartella.

L'esempio è costituito da file di testo ottenuti da [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). È possibile usarli come file di testo di esempio per la ricerca con Azure.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [Portale di Azure](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione offre uno spazio univoco per archiviare e accedere agli oggetti dati di Archiviazione di Azure.

Attualmente esistono due tipi di account di archiviazione, **BLOB** e **generico**. Per questa esercitazione creare un account di archiviazione **generico**.

Se non si ha familiarità con il processo di creazione dell'account di archiviazione generico, di seguito viene illustrato come crearne uno:

1. Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare **Aggiungi**.

2. Immettere un nome univoco per l'account di archiviazione. 

3. Selezionare **Gestione risorse** per il **Modello di distribuzione** e **Utilizzo generico** dall'elenco a discesa **Tipologia account**.

4. Nell'elenco a discesa **Replica** scegliere **Archiviazione con ridondanza locale**.

5. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome univoco.

6. Scegliere la sottoscrizione appropriata.

7. Scegliere un percorso e selezionare **Crea.**

  ![Ricerca non strutturata](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Creare un contenitore

I contenitori sono simili alle cartelle e vengono usati per archiviare BLOB.

Per questa esercitazione usare un solo contenitore per archiviare i file di testo ottenuti da clinicaltrials.gov.

1. Passare all'account di archiviazione nel portale di Azure.

2. Selezionare **Esplora BLOB** in **Servizio BLOB**.

3. Aggiungere un nuovo contenitore.

4. Denominare il contenitore "dati" e selezionare **Contenitore** per il livello di accesso pubblico.

5. Fare clic su **OK** per creare il contenitore. 

  ![Ricerca non strutturata](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Caricare i dati di esempio

Dopo aver creato il contenitore, è possibile caricarvi i dati di esempio.

1. Selezionare il contenitore e **Carica**.

2. Selezionare l'icona della cartella blu accanto al campo File e passare alla cartella locale in cui sono stati estratti i dati di esempio.

3. Selezionare tutti i file estratti e scegliere **Apri**

4. Selezionare **Carica** per avviare il processo di caricamento.

  ![Ricerca non strutturata](media/storage-unstructured-search/upload.png)

Il processo di caricamento potrebbe richiedere qualche istante.

Al termine tornare al contenitore dati per verificare i file di testo caricati.

  ![Ricerca non strutturata](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Creare un servizio di ricerca

Ricerca di Azure è una soluzione cloud di ricerca distribuita come servizio che offre agli sviluppatori le API e gli strumenti per ottenere un'esperienza di ricerca avanzata tra i dati nelle applicazioni Web, per dispositivi mobili ed enterprise.

Se non si ha familiarità con il processo di creazione del servizio di ricerca, di seguito viene illustrato come crearne uno:

1. Passare all'account di archiviazione nel portale di Azure.

2. Scorrere verso il basso e fare clic su **Aggiungi Ricerca di Azure** in **SERVIZIO BLOB**.

3. In **Importa dati** scegliere **Selezionare il servizio**.

4. Selezionare **Fare clic qui per creare un nuovo servizio di ricerca**.

5. All'interno di **Nuovo servizio di ricerca** immettere un nome univoco per il servizio di ricerca nel campo **URL**.

6. In **Gruppo di risorse** selezionare **Usa esistente** e scegliere il gruppo di risorse creato in precedenza.

7. Per **Piano tariffario** selezionare il livello **Gratuito** e fare clic su **Seleziona**.

8. Selezionare **Crea** per creare il servizio di ricerca.

  ![Ricerca non strutturata](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Collegare il servizio di ricerca al contenitore

Dopo aver creato il servizio di ricerca, è possibile collegarlo nell'archiviazione BLOB. Questa sezione illustra il processo di scelta di un'origine dati, la creazione di un indice e di un indicizzatore.

1. Passare all'account di archiviazione.

2. Selezionare **Aggiungi Ricerca di Azure** in **SERVIZIO BLOB**.

3. Selezionare **Servizio di ricerca** all'interno di **Importa dati** e quindi fare clic sul servizio di ricerca creato nella sezione precedente. Verrà visualizzato **Nuova origine dati**.

### <a name="new-data-source"></a>Nuova origine dati

  Un'origine dati specifica i dati da indicizzare e come accedervi. Questa può essere usata più volte nello stesso servizio di ricerca.

1. Immettere un nome per l'origine dati. In **Dati da estrarre** selezionare **Contenuto e metadati**. L'origine dati specifica quali parti del BLOB vengono indicizzate.
    
    a. In altri scenari è anche possibile selezionare **Solo metadati archiviazione**. Eseguire questa selezione se si desidera limitare i dati che vengono indicizzati alle proprietà BLOB standard o alle proprietà definite dall'utente.
    
    b. È anche possibile scegliere **All metadata** (Tutti i metadati) per ottenere proprietà BLOB standard e *tutti* i metadati specifici per il tipo di contenuto. 

2. Poiché i BLOB in uso sono file di testo, impostare **Modalità di analisi** su **Testo**.
    
    a. In altri scenari potrebbe essere necessario selezionare [altre modalità di analisi](../../search/search-howto-indexing-azure-blob-storage.md) in base al contenuto dei BLOB.

  ![Ricerca non strutturata](media/storage-unstructured-search/datasources.png)

3. Selezionare **Contenitore di archiviazione** per elencare gli account di archiviazione disponibili.

4. Selezionare l'account di archiviazione, quindi scegliere il contenitore creato in precedenza.

5. Fare clic su **Seleziona** per tornare a **Nuova origine dati** e selezionare **OK** per continuare.

  ![Ricerca non strutturata](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Configurare l'indice

  Un indice è una raccolta di campi dell'origine dati in cui è possibile eseguire ricerche. L'indice indica le modalità note al servizio di ricerca per eseguire le ricerche dei dati.

1. In **Importa dati** selezionare **Personalizza indice di destinazione**.
 
2. Immettere un nome per l'indice nel campo **Nome indice**.

3. Selezionare la casella di controllo dell'attributo **Recuperabile** in **metadata_storage_name**.

  ![Ricerca non strutturata](media/storage-unstructured-search/valuestoselect.png)

4. Fare clic su **OK**; viene visualizzato **Crea un indicizzatore**.

I parametri dell'indice e gli attributi assegnati a tali parametri sono importanti. I parametri specificano *quali* dati archiviare, mentre gli attributi specificano *come* archiviarli.

La colonna **NOME CAMPO** contiene i parametri. La tabella seguente contiene un elenco degli attributi disponibili e delle relative descrizioni.

### <a name="field-attributes"></a>Attributi dei campi
| Attributo | Descrizione |
| --- | --- |
| *Chiave* |Stringa che fornisce l'ID univoco di ogni documento, usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.String. |
| *Recuperabile* |Specifica se il campo può essere restituito nel risultato di una ricerca. |
| *Filtrabile* |Consente di usare il campo nelle query di filtro. |
| *Ordinabile* |Consente a una query ordinare i risultati della ricerca usando questo campo. |
| *Con facet* |Consente di usare un campo in una struttura di esplorazione in base a facet per i filtri autoindirizzati. In genere, i campi che contengono valori ricorrenti che è possibile usare per raggruppare più documenti, ad esempio, più documenti che rientrano in una categoria di servizi o una singola marca, funzionano meglio come facet. |
| *Ricercabile* |Contrassegna il campo come disponibile per la ricerca full-text. |


### <a name="create-an-indexer"></a>Crea un indicizzatore
    
  Un indicizzatore connette un'origine dati con un indice di ricerca e consente di pianificare una nuova indicizzazione dei dati.

1. Immettere un nome per il campo **Nome** e selezionare **OK**.

  ![Ricerca non strutturata](media/storage-unstructured-search/exindexer.png)

2. Verrà visualizzato nuovamente **Importa dati**, selezionare **OK** per completare il processo di connessione.

A questo punto il BLOB è stato collegato correttamente al servizio di ricerca. Sono necessari alcuni minuti affinché il portale mostri che l'indice è stato compilato. Tuttavia, il servizio di ricerca inizia l'indicizzazione subito per consentire all'utente di iniziare la ricerca immediatamente.

## <a name="search-your-text-files"></a>Cercare i file di testo

Per cercare i file, aprire Esplora ricerche nell'indice del servizio di ricerca appena creato.

I passaggi seguenti illustrano dove trovare Esplora ricerche e indicano alcuni esempi di query:

1. Passare a tutte le risorse e individuare il servizio di ricerca appena creato.

  ![Ricerca non strutturata](media/storage-unstructured-search/exampleurl.png)

3. Selezionare l'indice per aprirlo. 

  ![Ricerca non strutturata](media/storage-unstructured-search/overview.png)

4. Selezionare **Esplora ricerche** per aprire Esplora ricerche e poter effettuare query in tempo reale sui dati.

  ![Ricerca non strutturata](media/storage-unstructured-search/indexespane.png)

5. Selezionare **Cerca** quando il campo della stringa di query è vuoto. Una query vuota restituisce *tutti* i dati dei BLOB.

  ![Ricerca non strutturata](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Ricerca full-text 

Immettere "Myopia" nel campo **Stringa di query** e selezionare **Cerca**. Viene avviata una ricerca dei contenuti dei file che ne restituisce un sottoinsieme contenente la parola "Myopia".

  ![Ricerca non strutturata](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Ricerca delle proprietà di sistema

È anche possibile creare query che effettuano la ricerca per proprietà di sistema usando il parametro `$select`. Se si immette `$select=metadata_storage_name` nella stringa di query e si preme Invio, viene restituito solo quel campo specifico.
    
La stringa di query modifica direttamente l'URL, pertanto non sono consentiti spazi. Per eseguire una ricerca con più campi usare la virgola, ad esempio: `$select=metadata_storage_name,metadata_storage_path`
    
Il parametro `$select` può essere usato solo con i campi contrassegnati come recuperabili durante la definizione dell'indice.

  ![Ricerca non strutturata](media/storage-unstructured-search/metadatasearchunstructured.png) 

Questa esercitazione è terminata e ha consentito all'utente di avere un set di dati non strutturati ricercabile.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come eseguire una ricerca di dati non strutturati tramite Ricerca di Azure, ad esempio come:

> [!div class="checklist"]
> * Creare un gruppo di risorse
> * Creare un account di archiviazione
> * Creare un contenitore
> * Caricare dati nel contenitore
> * Creare un servizio di ricerca
> * Usare il servizio di ricerca per cercare il contenitore

Fare clic sul collegamento per altre informazioni sull'indicizzazione di documenti mediante Ricerca di Azure.

> [!div class="nextstepaction"]
> [Indicizzazione di documenti in Archiviazione BLOB di Azure con Ricerca di Azure](../../search/search-howto-indexing-azure-blob-storage.md)