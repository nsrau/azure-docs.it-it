---
title: Creare un archivio conoscenze nel portale di Azure - Ricerca di Azure
description: Creare un archivio conoscenze di Ricerca di Azure per salvare in modo permanente gli arricchimenti dalla pipeline di ricerca cognitiva usando la procedura guidata Importa dati nel portale di Azure.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 14996d0ac9ee4e086a5dccd9275ef694adca06ca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963188"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Creare un archivio conoscenze di Ricerca di Azure nel portale di Azure

> [!Note]
> La funzionalità dell'archivio conoscenze è in anteprima e non deve essere usata in ambienti di produzione. È inclusa nell'[API REST di Ricerca di Azure versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.
>

L'archivio conoscenze è una funzionalità di Ricerca di Azure che rende persistente l'output di una pipeline con arricchimento di intelligenza artificiale per analisi successive o l'ulteriore elaborazione downstream. Una pipeline con arricchimento di intelligenza artificiale accetta file di immagine o file di testo non strutturati, li indicizza usando Ricerca di Azure, applica gli arricchimenti di intelligenza artificiale di Servizi cognitivi (come l'analisi delle immagini e l'elaborazione del linguaggio naturale) e quindi salva i risultati in un archivio conoscenze in Archiviazione di Azure. È quindi possibile usare strumenti come Power BI o Storage Explorer per esplorare l'archivio conoscenze.

In questo articolo si userà la procedura guidata Importa dati nel portale di Azure per inserire, indicizzare e applicare gli arricchimenti di intelligenza artificiale a un set di recensioni di hotel. Le recensioni degli hotel vengono importate in Archiviazione BLOB di Azure e i risultati vengono salvati come archivio conoscenze in Archiviazione tabelle di Azure.

Dopo aver creato l'archivio conoscenze, è possibile imparare ad accedervi usando Storage Explorer o Power BI.

## <a name="prerequisites"></a>Prerequisiti

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questa esercitazione.

+ [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) per l'archiviazione dei dati di esempio e dell'archivio conoscenze. L'account di archiviazione deve usare la stessa località (ad esempio Stati Uniti occidentali ) del servizio Ricerca di Azure e il *tipo di account* deve essere *Archiviazione V2 (utilizzo generico v2)* (impostazione predefinita) o *Archiviazione (utilizzo generico V1)* .

## <a name="load-the-data"></a>Caricare i dati

Caricare il file CSV delle recensioni di hotel nell'archivio BLOB di Azure in modo che sia accessibile da un indicizzatore di Ricerca di Azure e alimentato attraverso la pipeline di arricchimento di intelligenza artificiale.

### <a name="create-an-azure-blob-container-with-the-data"></a>Creare un contenitore BLOB di Azure con i dati

1. [Scaricare i dati sulle recensioni di hotel in un file CSV (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Questi dati provengono da Kaggle.com e contengono il feedback dei clienti sugli hotel.
1. [Accedere al portale di Azure](https://portal.azure.com) e passare all'account di archiviazione di Azure.
1. [Creare un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). A questo scopo, nella barra di spostamento a sinistra per l'account di archiviazione fare clic su **BLOB** e quindi fare clic su **+ Contenitore** sulla barra dei comandi.
1. In **Nome** immettere `hotel-reviews` come nome del nuovo contenitore.
1. Selezionare l'opzione desiderata in **Livello di accesso pubblico**. In questo caso è stato usato il valore predefinito.
1. Fare clic su **OK** per creare il contenitore BLOB di Azure.
1. Aprire il nuovo contenitore `hotels-review`, fare clic su **Carica** e selezionare il file **HotelReviews-Free.csv** scaricato nel primo passaggio.

    ![Caricare i dati](media/knowledge-store-create-portal/upload-command-bar.png "Caricare le recensioni di hotel")

1. Fare clic su **Carica** per importare il file CSV in Archiviazione BLOB di Azure. Verrà visualizzato il nuovo contenitore.

    ![Creare il contenitore BLOB di Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Creare il contenitore BLOB di Azure")

### <a name="get-the-azure-storage-account-connection-string"></a>Ottenere la stringa di connessione all'account di archiviazione di Azure

1. Nel portale passare all'account di archiviazione di Azure.
1. Nella riquadro di spostamento a sinistra per il servizio fare clic su **Chiavi di accesso**.
1. In **Chiave 1** copiare e salvare la *stringa di connessione*. La stringa inizia con `DefaultEndpointsProtocol=https`. Il nome e la chiave dell'account di archiviazione sono incorporati nella stringa. Tenere questa stringa a portata di mano. Sarà necessaria nei passaggi successivi.

## <a name="create-and-run-ai-enrichments"></a>Creare ed eseguire arricchimenti di intelligenza artificiale

Usare la procedura guidata Importa dati per creare l'archivio conoscenze. Si creerà un'origine dati, si sceglieranno gli arricchimenti, si configureranno un archivio conoscenze e un indice e quindi si procederà con l'esecuzione.

### <a name="start-the-import-data-wizard"></a>Avviare la procedura guidata Importa dati

1. Nel portale di Azure [trovare il servizio di ricerca](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Sulla barra dei comandi fare clic su **Importa dati** per avviare l'importazione guidata.

### <a name="connect-to-your-data-import-data-wizard"></a>Connettersi ai dati (procedura guidata Importa dati)

In questo passaggio della procedura guidata si creerà un'origine dati dal BLOB di Azure con i dati degli hotel.

1. Nell'elenco **Origine dati** selezionare **Archiviazione BLOB di Azure**.
1. In **Nome** immettere `hotel-reviews-ds`.
1. In **Modalità di analisi** selezionare **Testo delimitato** e quindi selezionare la casella di controllo **La prima riga contiene l'intestazione**. Verificare che in **Carattere di delimitazione**  sia impostata la virgola (,).
1. Immettere la **stringa di connessione** del servizio di archiviazione salvata in un passaggio precedente.
1. In **Nome contenitore** immettere `hotel-reviews`.
1. Fare clic su **Avanti: Aggiungi ricerca cognitiva (facoltativo)** .

      ![Creare un oggetto origine dati](media/knowledge-store-create-portal/hotel-reviews-ds.png "Creare un oggetto origine dati")

## <a name="add-cognitive-search-import-data-wizard"></a>Aggiungi ricerca cognitiva (procedura guidata Importa dati)

In questo passaggio della procedura guidata si creerà un set di competenze con arricchimenti delle competenze cognitive. Le competenze usate in questo esempio estraggono le frasi chiave e rilevano il linguaggio e il sentiment. Questi arricchimenti verranno "proiettati" in un archivio conoscenze come tabelle di Azure.

1. Espandere **Collega Servizi cognitivi**. **Gratuito (miglioramenti limitati)** è l'impostazione predefinita. È possibile usare questa risorsa perché il numero di record in HotelReviews-Free.csv è 19 e questa risorsa gratuita consente fino a 20 transazioni al giorno.
1. Espandere **Aggiungi arricchimenti**.
1. In **Nome set di competenze** immettere `hotel-reviews-ss`.
1. In **Campo dei dati di origine** selezionare **reviews_text*.
1. In **Livello di granularità dell'arricchimento** selezionare **Pagine (5000 blocchi di caratteri)** .
1. Selezionare le competenze cognitive seguenti:
    + **Estrarre le espressioni chiave**
    + **Rilevare la lingua**
    + **Rileva sentiment**

      ![Creare un set di competenze](media/knowledge-store-create-portal/hotel-reviews-ss.png "Creare un set di competenze")

1. Espandere **Salva arricchimenti in un archivio conoscenze (anteprima)** .
1. Immettere la **stringa di connessione dell'account di archiviazione** salvata in un passaggio precedente.
1. Selezionare le **proiezioni di tabelle di Azure** seguenti:
    + **Documents** (Documenti)
    + **Pagine**
    + **Frasi chiave**

    ![Configurare l'archivio conoscenze](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurare l'archivio conoscenze")

1. Fare clic su **Avanti: Personalizza indice di destinazione**.

### <a name="import-data-import-data-wizard"></a>Importa dati (procedura guidata Importa dati)

In questo passaggio della procedura guidata si configura un indice per query di ricerca full-text facoltative. La procedura guidata campiona l'origine dati per dedurre i campi e i tipi di dati. Non occorre fare altro che selezionare gli attributi per il comportamento desiderato. Ad esempio, l'attributo **Recuperabile** consentirà al servizio di ricerca di restituire un valore di campo, mentre l'attributo **Ricercabile** abiliterà la ricerca full-text nel campo.

1. In **Nome indice** immettere `hotel-reviews-idx`.
1. Per gli attributi, effettuare le selezioni seguenti:
    + Selezionare **Recuperabile** per tutti i campi.
    + Selezionare **Filtrabile** e **Con facet** per questi campi: *Sentiment*, *Language*, *Keyphrases*
    + Selezionare **Ricercabile** per questi campi: *city*, *name*, *reviews_text*, *language*, *Keyphrases*

    L'indice dovrebbe essere simile all'immagine seguente. Poiché l'elenco è lungo, non tutti i campi sono visibili nell'immagine.

    ![Configurare un indice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurare un indice")

1. Fare clic su **Avanti: Crea un indicizzatore**.

### <a name="create-an-indexer"></a>Creare un indicizzatore

In questo passaggio della procedura guidata si configurerà un indicizzatore che riunisce l'origine dati, il set di competenze e l'indice definiti nei passaggi precedenti della procedura guidata.

1. Per **Nome** immettere `hotel-reviews-idxr`.
1. In **Pianificazione** mantenere l'impostazione predefinita **Una sola volta**.
1. Fare clic su **Invia** per eseguire l'indicizzatore. Le operazioni di estrazione dei dati, indicizzazione e applicazione delle competenze cognitive vengono eseguite tutte in questo passaggio.

### <a name="monitor-the-notifications-queue-for-status"></a>Monitorare la coda delle notifiche per lo stato

1. Nel portale di Azure monitorare il log attività Notifiche per rilevare la presenza di un collegamento di stato **Notifica di Ricerca di Azure** selezionabile. Il completamento dell'esecuzione può richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Ora che i dati sono stati arricchiti mediante i servizi cognitivi e i risultati sono stati proiettati in un archivio conoscenze, è possibile usare Storage Explorer o Power BI per esplorare il set di dati arricchito.

Per informazioni su come esplorare questo archivio conoscenze usando Storage Explorer, vedere la procedura dettagliata seguente.

> [!div class="nextstepaction"]
> [Visualizzare con Storage Explorer](knowledge-store-view-storage-explorer.md)

Per informazioni su come connettere questo archivio conoscenze a Power BI, vedere la procedura dettagliata seguente.

> [!div class="nextstepaction"]
> [Stabilire la connessione con Power BI](knowledge-store-connect-power-bi.md)

Se si vuole ripetere questo esercizio o provare un'altra procedura dettagliata per gli arricchimenti di intelligenza artificiale, eliminare l'indicizzatore *hotel-reviews-idxr*. Eliminando l'indicizzatore, il contatore delle transazioni giornaliere gratuite viene azzerato.
