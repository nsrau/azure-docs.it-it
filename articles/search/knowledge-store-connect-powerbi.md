---
title: Connettersi a un archivio conoscenze con Power BI - Ricerca di Azure
description: Creare un archivio conoscenze con la procedura guidata Importa dati nel portale di Azure e quindi connettersi con Power BI per l'analisi e l'esplorazione.
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 1c7f297092760f2a92f524347a3c1a5e353d0965
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635543"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Creare un archivio conoscenze di Ricerca di Azure e connettersi con Power BI

> [!Note]
> La funzionalità dell'archivio conoscenze è in anteprima e non deve essere usata in ambienti di produzione. È inclusa nell'[API REST di Ricerca di Azure versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.
>

L'archivio conoscenze è una funzionalità di Ricerca di Azure che rende persistente l'output di una pipeline con arricchimento di intelligenza artificiale per analisi successive o l'ulteriore elaborazione downstream. Una pipeline con arricchimento di intelligenza artificiale accetta file di immagine o file di testo non strutturati da un'origine dati supportata, li invia alla funzionalità di indicizzazione di Ricerca di Azure, applica arricchimenti di intelligenza artificiale di Servizi cognitivi (come l'analisi delle immagini e l'elaborazione del linguaggio naturale) e quindi salva i risultati in un archivio conoscenze in Archiviazione di Azure. Dall'archivio conoscenze è possibile collegare strumenti come Power BI o Storage Explorer per esplorare i risultati.

In questo articolo si creerà un archivio conoscenze nel portale e quindi si eseguiranno la connessione e l'esplorazione usando Power Query in Power BI Desktop. 

Questa procedura dettagliata usa un set di dati costituito da recensioni e valutazioni dei clienti, l'assegnazione di punteggi al sentiment in Servizi cognitivi e quindi Power Query per eseguire query sui documenti. I dati provengono dai dati di recensioni di hotel in Kaggle.com. 

## <a name="prerequisites"></a>Prerequisiti

+ [Scaricare il file CSV di recensioni di hotel (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Questo set di dati contiene record di commenti e suggerimenti dei clienti relativi a hotel.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Ricerca di Azure](search-create-service-portal.md). Per questa procedura dettagliata è possibile usare un servizio gratuito. 

+ [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Verificare che l'account sia "per utilizzo generico", ossia un *account di archiviazione per utilizzo generico V2*, che è l'impostazione predefinita, oppure *per utilizzo generico V1*. Scegliere la stessa area di Ricerca di Azure.
 
## <a name="prepare-data"></a>Preparazione dei dati 

Caricare il file con estensione csv nell'archivio BLOB di Azure in modo che sia accessibile da un indicizzatore di Ricerca di Azure.

1. [Accedere al portale di Azure](https://portal.azure.com), passare all'account di archiviazione di Azure, fare clic su **BLOB** e quindi su **+ Contenitore**.

1. [Creare un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) per i dati di esempio: 

   1. Assegnare al contenitore il nome `hotel-reviews`. 
   
   1. Impostare il livello di accesso pubblico su uno qualsiasi dei relativi valori validi. In questo caso è stato usato il valore predefinito.

1. Dopo aver creato il contenitore, aprirlo e selezionare **Carica** nella barra dei comandi.

1. Passare alla cartella contenente il file **HotelReviews-Free.csv**, selezionare il file e fare clic su **Carica**.

   ![Caricare il file CSV](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Caricare il file CSV")

1. Nell'archiviazione di Azure recuperare la stringa di connessione e il nome del contenitore.  Entrambe queste stringhe saranno necessarie durante la creazione di un oggetto origine dati:

   1. Nella pagina della panoramica fare clic su **Chiavi di accesso** e copiare una *stringa di connessione*. Tale stringa inizia con `DefaultEndpointsProtocol=https;` e termina con `EndpointSuffix=core.windows.net`. Nella parte centrale della stringa sono indicati il nome e la chiave dell'account. 

   1. Il nome del contenitore deve essere `hotel-reviews` o quello assegnato. 

## <a name="create-and-run-ai-enrichments"></a>Creare ed eseguire arricchimenti di intelligenza artificiale

Usare la procedura guidata Importa dati per creare l'archivio conoscenze. Si importerà il set di dati, si sceglieranno gli arricchimenti, si configureranno un archivio conoscenze e un indice e quindi si procederà con l'esecuzione.

1. [Trovare il servizio di ricerca](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nel portale di Azure.

1. Fare clic su **Importa dati** sulla barra dei comandi.

1. Creare l'oggetto origine dati nella prima pagina della procedura guidata.

   - Selezionare **Archivio BLOB di Azure**.

   - Assegnare un nome all'origine dati, ad esempio *hotel-reviews-ds*.

   - Poiché i dati sono in formato CSV, selezionare **Testo delimitato** come modalità di analisi e quindi **La prima riga contiene l'intestazione** e assicurarsi che il carattere di delimitazione sia una virgola.

   - La stringa di connessione all'account di archiviazione di Azure è disponibile nel portale, in **Chiavi di accesso**.

   - Anche il nome del contenitore è riportato nel portale nell'elenco dei BLOB del servizio di archiviazione di Azure.

      ![Creare un oggetto origine dati](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Creare un oggetto origine dati")

1. Aggiungere gli arricchimenti e configurare un archivio conoscenze nella seconda pagina della procedura guidata.

   - In **Collega Servizi cognitivi** è possibile usare la risorsa gratuita che consente fino a 20 transazioni al giorno. Il numero di record in HotelReviews-Free.csv è inferiore a 20.

   - In **Aggiungi arricchimenti** assegnare il nome *hotel-reviews-ss* al set di competenze, scegliere il campo *reviews_text* e il livello di granularità *Pagine (5000 blocchi di caratteri)* e quindi selezionare queste tre competenze cognitive: *Estrai frasi chiave*, *Rileva lingua* e *Rileva sentiment*.

      ![Creare un set di competenze](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Creare un set di competenze")

   - In **Salva arricchimenti in un archivio conoscenze** specificare la stringa di connessione all'account di archiviazione di Azure per utilizzo generico. Selezionando le opzioni di *Azure table projections* (Proiezioni in tabelle di Azure) in questa sezione, verrà creato un archivio conoscenze nell'archivio tabelle di Azure.

      ![Configurare l'archivio conoscenze](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Configurare l'archivio conoscenze")
   
   Fare clic su **Avanti: Personalizza indice di destinazione** per procedere al passaggio successivo.

1. Configurare un indice per query di ricerca full-text facoltative in Ricerca di Azure. Per quanto questa procedura dettagliata sia incentrata sulla connessione di Power BI all'archivio tabelle di Azure, la procedura guidata **Importa dati** consente anche di creare un indice usato per la ricerca full-text in Ricerca di Azure. 

   La procedura guidata esegue il campionamento dell'origine dati per dedurre campi e tipi di dati, pertanto per ottenere i comportamenti desiderati è sufficiente selezionare gli attributi necessari. Con *Recuperabile*, ad esempio, il contenuto del campo può essere recuperato dal servizio, mentre *Ricercabile* consente la ricerca full-text sui campi selezionati.

   - Assegnare un nome all'indice, ad esempio *hotel-reviews-idx*.
   - Impostare **Recuperabile** per tutti i campi.
   - Impostare **Ricercabile** per *city*, *name*, *reviews_text*, *language* e *keyphrases*.
   - Impostare **Filtrabile** e **Con facet** per *sentiment*, *language* e *keyphrases*. 
   
    L'indice dovrebbe essere simile all'immagine seguente.

     ![Configurare un indice](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Configurare un indice")

   Fare clic su **Avanti: Crea un indicizzatore** per procedere al passaggio successivo.

1. Configurare un indicizzatore assegnandogli un nome e una frequenza di esecuzione. Per questa procedura dettagliata, usare *hotel-reviews-idxr* come nome dell'indicizzatore e usare la pianificazione predefinita **Una sola volta** per eseguirlo immediatamente.

   L'esecuzione dell'indicizzatore attiva tutte le configurazioni precedenti. Tutte le operazioni di estrazione, elaborazione e inserimento vengono eseguite in questo passaggio.

1. Monitorare l'indicizzazione nella coda delle notifiche nel portale. Il completamento dell'esecuzione richiede alcuni minuti.

## <a name="connect-with-power-bi"></a>Connettersi con Power BI

1. Avviare Power BI Desktop e selezionare **Recupera dati**.

1. In Recupera dati selezionare **Azure** e quindi **Archiviazione tabelle di Azure**. Fare clic su **Connetti**.

1. In Nome dell'account o URL incollare il nome dell'account di archiviazione di Azure. L'URL completo viene risolto automaticamente.

1. Immettere la chiave dell'account.

1. Selezionare Document, KeyPhrases e Pages, che sono le tabelle create dalla procedura guidata Importa dati quando si selezionano gli elementi con lo stesso nome nella configurazione dell'archivio conoscenze. Fare clic su **Carica**.

1. Aprire Power Query facendo clic sul comando **Modifica query**.

   ![Aprire Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Aprire Power Query")

1. Per Document:

   - Rimuovere le colonne PartitionKey, RowKey e Timestamp create da Archiviazione tabelle di Azure. Le relazioni usate in questa analisi sono fornite dall'archivio conoscenze.

   - Espandere la colonna del contenuto.

     ![Modificare le tabelle](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Modificare le tabelle")

1. Selezionare tutti i campi e quindi deselezionare quelli che iniziano con "metadata".

1. Correggere il tipo di dati per le colonne seguenti usando l'icona ABC-123 in ogni colonna:

   - Le colonne delle date devono essere di tipo **Data/Ora**
   - *Latitude* deve essere di tipo **Numero decimale**
   - *Longitude* deve essere di tipo **Numero decimale**

1. Ripetere i passaggi precedenti per KeyPhrases, rimuovendo PartitionKey e le altre colonne, espandendo le colonne del contenuto e impostando *SentimentScore* su **Numero decimale**.

1. Ripetere di nuovo per Pages, rimuovendo PartitionKey e le altre colonne ed espandendo le colonne del contenuto. Per questa tabella non sono necessarie modifiche del tipo di dati.

1. Fare clic su **Chiudi e applica** all'estrema sinistra della barra dei comandi di Power Query.

1. Verificare che Power BI riconosca le relazioni create dall'archivio conoscenze all'interno dei dati. Fare clic sul riquadro delle relazioni nel riquadro di spostamento a sinistra. Tutte e tre le tabelle devono essere correlate. Modificare le relazioni e verificare che l'opzione "Direzione filtro incrociato" sia impostata su Entrambi affinché tutti gli oggetti visivi vengano aggiornati quando viene applicato un filtro.

   ![Verificare le relazioni](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Verificare le relazioni")

## <a name="try-with-larger-data-sets"></a>Provare con set di dati di maggiori dimensioni

È stato intenzionalmente usato un set di dati di dimensioni ridotte per evitare addebiti per una procedura dettagliata dimostrativa. Per un'esperienza più realistica, è possibile creare e quindi collegare una risorsa Servizi cognitivi fatturabile per supportare un numero maggiore di transazioni per le competenze di analisi del sentiment, estrazione di frasi chiave e rilevamento della lingua.

Creare nuovi contenitori nell'archivio BLOB di Azure e caricare ogni file CSV nel relativo contenitore. Specificare uno di questi contenitori nel passaggio della creazione dell'origine dati nella procedura guidata Importa dati.

| DESCRIZIONE | Collegamento |
|-------------|------|
| Livello gratuito   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Piccolo (500 record) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medio (6000 record)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Grande (35000 record dell'intero set di dati) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Tenere presente che l'elaborazione di set di dati di dimensioni molto grandi è costosa. Questo set di dati costa circa 1.000 dollari USA.|

Per usare set di dati di dimensioni maggiori, nel passaggio degli arricchimenti della procedura guidata collegare una risorsa [Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) fatturabile, creata al livello *S0* nella stessa area di Ricerca di Azure. 

  ![Creare una risorsa Servizi cognitivi](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Creare una risorsa Servizi cognitivi")

## <a name="next-steps"></a>Passaggi successivi

Se si vuole ripetere questo esercizio o eseguire un'altra procedura dettagliata per gli arricchimenti di intelligenza artificiale, eliminare l'indicizzatore *hotel-reviews-idx* appena creato. Eliminando l'indicizzatore, il contatore delle transazioni giornaliere gratuite viene azzerato. 

Per altre procedure dettagliate che illustrano l'archivio conoscenze, continuare con l'articolo successivo che descrive come creare un archivio conoscenze usando le API REST e Postman.

> [!div class="nextstepaction"]
> [Introduzione all'archivio conoscenze](knowledge-store-howto.md)
