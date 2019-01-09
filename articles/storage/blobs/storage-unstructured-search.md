---
title: "Esercitazione: Cercare dati non strutturati nell'archiviazione BLOB di Azure"
description: "Esercitazione: Ricerca di dati non strutturati nell'archiviazione BLOB tramite Ricerca di Azure."
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 42c67d73ee776488fbe932676f61cb7166c2984b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599841"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Esercitazione: Cercare dati non strutturati nell'archiviazione cloud

In questa esercitazione viene illustrato come eseguire una ricerca di dati non strutturati tramite [Ricerca di Azure](../../search/search-what-is-azure-search.md) usando i dati archiviati nell'archiviazione BLOB di Azure. I dati non strutturati sono dati non organizzati in modo predefinito o che non dispongono di un modello di dati. Un esempio è rappresentato da un file TXT.

Per completare questa esercitazione, è necessario avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di risorse
> * Creare un account di archiviazione
> * Creare un contenitore
> * Caricare dati nel contenitore
> * Creare un servizio di ricerca tramite il portale
> * Connettere un servizio di ricerca a un account di archiviazione
> * Creare un'origine dati
> * Configurare l'indice
> * Creare un indicizzatore
> * Usare il servizio di ricerca per cercare il contenitore

## <a name="prerequisites"></a>Prerequisiti

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. In questa esercitazione viene creato un nuovo gruppo di risorse.

Accedere al [portale di Azure](http://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

È stato preparato un set di dati di esempio per consentirne l'uso durante l'esercitazione. Scaricare [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) e decomprimere il file nella cartella.

L'esempio è costituito da file di testo ottenuti da [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). In questa esercitazione i file vengono usati come file di testo di esempio in cui vengono eseguite le ricerche tramite servizi di ricerca di Azure.

## <a name="create-a-container"></a>Creare un contenitore

I contenitori sono simili alle cartelle e vengono usati per archiviare BLOB.

Per questa esercitazione usare un solo contenitore per archiviare i file di testo ottenuti da clinicaltrials.gov.

1. Passare al proprio account di archiviazione nel portale di Azure.

2. Selezionare **Esplora BLOB** in **Servizio BLOB**.

3. Aggiungere un nuovo contenitore.

4. Assegnare al contenitore il nome **data** e selezionare **Contenitore** per il livello di accesso pubblico.

5. Fare clic su **OK** per creare il contenitore.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Caricare i dati di esempio

Dopo aver creato il contenitore, è possibile caricarvi i dati di esempio.

1. Selezionare il contenitore e **Carica**.

2. Selezionare l'icona di cartella blu accanto al campo **File** e passare alla cartella locale in cui sono stati estratti i dati di esempio.

3. Selezionare tutti i file estratti e scegliere **Apri**.

4. Selezionare **Carica** per avviare il processo di caricamento.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/upload.png)

Il processo di caricamento può richiedere alcuni istanti.

Al termine, tornare al contenitore dati per verificare i file di testo caricati.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Creare un servizio di ricerca

Ricerca di Azure è una soluzione cloud di ricerca distribuita come servizio che offre agli sviluppatori le API e gli strumenti necessari per aggiungere un'esperienza di ricerca sui dati.

Per questa esercitazione, usare un servizio di ricerca per cercare nei file di testo ottenuti da clinicaltrials.gov.

1. Passare al proprio account di archiviazione nel portale di Azure.

2. Scorrere verso il basso e selezionare **Aggiungi Ricerca di Azure** in **SERVIZIO BLOB**.

3. In **Importa dati** scegliere **Selezionare il servizio**.

4. Selezionare **Fare clic qui per creare un nuovo servizio di ricerca**.

5. All'interno di **Nuovo servizio di ricerca** immettere un nome univoco per il servizio di ricerca nel campo **URL**.

6. In **Gruppo di risorse** selezionare **Usa esistente** e scegliere il gruppo di risorse creato in precedenza.

7. Per **Piano tariffario** selezionare il livello **Gratuito** e fare clic su **Seleziona**.

8. Selezionare **Crea** per creare il servizio di ricerca.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Collegare il servizio di ricerca al contenitore

Dopo aver creato il servizio di ricerca, è possibile collegarlo nell'archiviazione BLOB. Questa sezione illustra il processo di scelta di un'origine dati, la creazione di un indice e di un indicizzatore.

1. Passare all'account di archiviazione.

2. Selezionare **Aggiungi Ricerca di Azure** in **SERVIZIO BLOB**.

3. Selezionare **Servizio di ricerca** all'interno di **Importa dati** e quindi fare clic sul servizio di ricerca creato nella sezione precedente. Viene visualizzato **Nuova origine dati**.

### <a name="create-a-data-source"></a>Creare un'origine dati

  Un'origine dati specifica i dati da indicizzare e come accedervi. Questa può essere usata più volte nello stesso servizio di ricerca.

1. Immettere un nome per l'origine dati. In **Dati da estrarre** selezionare **Contenuto e metadati**. L'origine dati specifica quali parti del BLOB vengono indicizzate.

2. Poiché i BLOB usati sono file di testo, impostare **Modalità di analisi** su **Testo**.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/datasources.png)

3. Selezionare **Contenitore di archiviazione** per elencare gli account di archiviazione disponibili.

4. Selezionare l'account di archiviazione e quindi il contenitore creato in precedenza.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/datacontainer.png)

5. Fare clic su **Seleziona** per tornare a **Nuova origine dati** e selezionare **OK** per continuare.

### <a name="configure-the-index"></a>Configurare l'indice

  Un indice è una raccolta di campi dell'origine dati in cui è possibile eseguire ricerche. È necessario impostare e configurare parametri in questi campi per indicare al servizio di ricerca i modi in cui cercare i dati.

1. In **Importa dati** selezionare **Personalizza indice di destinazione**.

2. Immettere un nome per l'indice nel campo **Nome indice**.

3. Selezionare la casella di controllo dell'attributo **Recuperabile** in **metadata_storage_name**.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/valuestoselect.png)

4. Selezionare **OK**. Verrà visualizzato **Crea un indicizzatore**.

I parametri dell'indice e gli attributi assegnati a tali parametri sono importanti. I parametri specificano *quali* dati archiviare, mentre gli attributi specificano *come* archiviarli.

La colonna **NOME CAMPO** contiene i parametri. La tabella seguente contiene un elenco degli attributi disponibili e delle relative descrizioni.

#### <a name="field-attributes"></a>Attributi dei campi

| Attributo | Descrizione |
| --- | --- |
| *Chiave* |Stringa che fornisce l'ID univoco di ogni documento, usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.String. |
| *Recuperabile* |Specifica se il campo può essere restituito nel risultato di una ricerca. |
| *Filtrabile* |Consente di usare il campo nelle query di filtro. |
| *Ordinabile* |Consente a una query ordinare i risultati della ricerca usando questo campo. |
| *Con facet* |Consente di usare un campo in una struttura di esplorazione in base a facet per i filtri autoindirizzati. In genere, i campi che contengono valori ricorrenti che è possibile usare per raggruppare più documenti, ad esempio più documenti che rientrano in una categoria di servizi o un singolo marchio, funzionano meglio come facet. |
| *Ricercabile* |Contrassegna il campo come disponibile per la ricerca full-text. |

### <a name="create-an-indexer"></a>Creare un indicizzatore

  Un indicizzatore connette un'origine dati con un indice di ricerca e consente di pianificare una nuova indicizzazione dei dati.

1. Immettere un nome per il campo **Nome** e selezionare **OK**.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/exindexer.png)

2. Verrà visualizzato di nuovo **Importa dati**. Selezionare **OK** per completare il processo di connessione.

A questo punto il BLOB è stato collegato correttamente al servizio di ricerca. Sono necessari alcuni minuti affinché il portale mostri che l'indice è stato compilato. Tuttavia, il servizio di ricerca inizia l'indicizzazione subito per consentire all'utente di iniziare la ricerca immediatamente.

## <a name="search-your-text-files"></a>Cercare i file di testo

Per cercare i file, aprire Esplora ricerche nell'indice del servizio di ricerca appena creato.

I passaggi seguenti illustrano dove trovare Esplora ricerche e indicano alcuni esempi di query:

1. Passare a tutte le risorse e individuare il servizio di ricerca appena creato.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/exampleurl.png)

2. Selezionare l'indice per aprirlo.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/overview.png)

3. Selezionare **Esplora ricerche** per aprire Esplora ricerche, in cui è possibile eseguire query in tempo reale sui dati.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/indexespane.png)

4. Selezionare **Cerca** quando il campo della stringa di query è vuoto. Una query vuota restituisce *tutti* i dati dei BLOB.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Eseguire una ricerca full-text

Immettere **Myopia** nel campo **Stringa di query** e selezionare **Cerca**. Viene avviata una ricerca nel contenuto dei file che ne restituisce il sottoinsieme contenente la parola "Myopia".

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Eseguire una ricerca delle proprietà di sistema

Oltre a una ricerca full-text, è possibile creare query per la ricerca in base a proprietà di sistema tramite il parametro `$select`.

Immettere `$select=metadata_storage_name` nella stringa di query e premere **INVIO**. In questo modo, viene restituito solo il campo specifico.

La stringa di query modifica direttamente l'URL, pertanto non sono consentiti spazi. Per eseguire una ricerca con più campi usare la virgola, ad esempio: `$select=metadata_storage_name,metadata_storage_path`

Il parametro `$select` può essere usato solo con i campi contrassegnati come recuperabili durante la definizione dell'indice.

  ![Ricerca su dati non strutturati](media/storage-unstructured-search/metadatasearchunstructured.png)

Questa esercitazione è terminata e ha consentito all'utente di avere un set di dati non strutturati ricercabile.

## <a name="clean-up-resources"></a>Pulire le risorse

Il metodo più semplice per rimuovere le risorse create consiste nell'eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le risorse che contiene. Nell'esempio seguente la rimozione del gruppo di risorse rimuove l'account di archiviazione e il gruppo di risorse stesso.

1. Nel portale di Azure passare all'elenco dei gruppi di risorse nella sottoscrizione.
2. Scegliere il gruppo di risorse che si vuole eliminare.
3. Selezionare il pulsante **Elimina gruppo di risorse** e immettere il nome del gruppo di risorse nel campo di eliminazione.
4. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Fare clic su questo collegamento per altre informazioni sull'indicizzazione di documenti con Ricerca di Azure:

> [!div class="nextstepaction"]
> [Indicizzazione di documenti in Archiviazione BLOB di Azure con Ricerca di Azure](../../search/search-howto-indexing-azure-blob-storage.md)
