---
title: Creare un archivio conoscenze (anteprima) nel portale di Azure
titleSuffix: Azure Cognitive Search
description: Usare l'Importazione guidata dati per creare un archivio conoscenze da usare per persistente il contenuto arricchito. Connettersi a un archivio conoscenze per l'analisi da altre app o inviare contenuto arricchito ai processi downstream. Questa funzionalità è attualmente in anteprima pubblica.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472367"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Guida introduttiva: Creare un archivio conoscenze di Ricerca cognitiva di Azure nel portale di Azure

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

L'archivio conoscenze è una funzionalità di Ricerca cognitiva di Azure che consente di rendere persistente l'output di una pipeline di competenze cognitive per analisi successive o per l'elaborazione downstream. 

Una pipeline accetta testo e immagini non strutturate come contenuto non elaborato, applica l'intelligenza artificiale tramite Servizi cognitivi (ad esempio OCR, analisi delle immagini e elaborazione del linguaggio naturale), estrae informazioni e restituisce nuove strutture e informazioni. Uno degli artefatti fisici creati da una pipeline è un [archivio conoscenze](knowledge-store-concept-intro.md), a cui è possibile accedere tramite strumenti per analizzare ed esplorare il contenuto.

In questa guida di avvio rapido verranno combinati servizi e dati nel cloud di Azure per creare un archivio conoscenze. Si userà quindi la procedura guidata **Importa dati** nel portale per riunire tutti i dati ottenuti. Il risultato finale include il contenuto di testo originale e quello generato dall'intelligenza artificiale che è possibile visualizzare nel portale ([Storage Explorer](knowledge-store-view-storage-explorer.md)).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-services-and-load-data"></a>Creare i servizi e caricare i dati

Questa guida di avvio rapido usa Ricerca cognitiva di Azure, Archiviazione BLOB di Azure e [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/) per l'intelligenza artificiale. 

Poiché il carico di lavoro è molto ridotto, Servizi cognitivi lavora dietro le quinte per offrire un'elaborazione gratuita per un massimo di 20 transazioni al giorno. Poiché il set di dati è molto ridotto, è possibile evitare di creare o collegare una risorsa di Servizi cognitivi.

1. [Download HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Questi dati corrispondono ai dati delle recensioni di hotel salvati in un file CSV (generato da Kaggle.com) e contengono 19 commenti dei clienti su un unico hotel. 

1. [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) o [trovare un account esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) nella sottoscrizione corrente. Si userà l'archiviazione di Azure sia per il contenuto non elaborato da importare che per l'archivio conoscenze che rappresenta il risultato finale.

   Scegliere il tipo di account **Archiviazione V2 (utilizzo generico V2)** .

1. Aprire le pagine dei servizi BLOB e creare un contenitore denominato *hotel-reviews*.

1. Fare clic su **Carica**.

    ![Caricare i dati](media/knowledge-store-create-portal/upload-command-bar.png "Caricare le recensioni di hotel")

1. Selezionare il file **HotelReviews-Free.csv** scaricato nel primo passaggio.

    ![Creazione del contenitore BLOB di Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Creazione del contenitore BLOB di Azure")

1. Questa risorsa è quasi terminata, ma prima di chiudere le pagine, fare clic sul collegamento nel riquadro di spostamento sinistro per aprire la pagina **Chiavi di accesso**. Ottenere una stringa di connessione per recuperare i dati dall'archiviazione BLOB. Una stringa di connessione è simile all'esempio seguente: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Sempre nel portale, passare a Ricerca cognitiva di Azure. [Creare un nuovo servizio](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). È possibile usare un servizio gratuito per questo avvio rapido.

A questo punto è possibile procedere con la procedura guidata Importa dati.

## <a name="run-the-import-data-wizard"></a>Eseguire la procedura guidata Importa dati

Nella pagina Panoramica del servizio di ricerca fare clic su **Importa dati** sulla barra dei comandi per creare un archivio conoscenze in quattro passaggi.

  ![Comando Importa dati](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

1. In **Definisci la connessione ai dati** scegliere **Archiviazione BLOB di Azure** e selezionare l'account e il contenitore creati. 
1. In **Nome** immettere `hotel-reviews-ds`.
1. In **Modalità di analisi** selezionare **Testo delimitato** e quindi selezionare la casella di controllo **La prima riga contiene l'intestazione**. Verificare che in **Carattere di delimitazione**  sia impostata la virgola (,).
1. In **Stringa di connessione** incollare la stringa di connessione copiata dalla pagina **Chiavi di accesso** in Archiviazione di Azure.
1. In **Contenitori** immettere il nome del contenitore BLOB in cui si trovano i dati.

    La pagina sarà simile alla schermata seguente.

    ![Creazione di un oggetto origine dati](media/knowledge-store-create-portal/hotel-reviews-ds.png "Creazione di un oggetto origine dati")

1. Passare alla pagina successiva.

### <a name="step-2-add-cognitive-skills"></a>Passaggio 2: Aggiungere competenze cognitive

In questo passaggio della procedura guidata si creerà un set di competenze con arricchimenti delle competenze cognitive. I dati di origine sono costituiti dalle recensioni dei clienti in diverse lingue. Le competenze pertinenti per questo set di dati includono l'estrazione di frasi chiave, il rilevamento del sentiment e la traduzione del testo. In un passaggio successivo questi arricchimenti verranno "proiettati" in un archivio conoscenze come tabelle di Azure.

1. Espandere **Collega Servizi cognitivi**. **Gratuito (miglioramenti limitati)** è l'impostazione predefinita. È possibile usare questa risorsa perché il numero di record in HotelReviews-Free.csv è 19 e questa risorsa gratuita consente fino a 20 transazioni al giorno.
1. Espandere **Aggiungi arricchimenti**.
1. In **Nome set di competenze** immettere `hotel-reviews-ss`.
1. In **Campo dei dati di origine** selezionare **reviews_text**.
1. In **Livello di granularità dell'arricchimento** selezionare **Pagine (5000 blocchi di caratteri)** .
1. Selezionare le competenze cognitive seguenti:
    + **Estrarre le espressioni chiave**
    + **Tradurre un testo**
    + **Rileva sentiment**

      ![Creare un set di competenze](media/knowledge-store-create-portal/hotel-reviews-ss.png "Creare un set di competenze")

1. Espandere **Salva arricchimenti in un archivio conoscenze (anteprima)** .
1. Selezionare le **proiezioni di tabelle di Azure** seguenti:
    + **Documents** (Documenti)
    + **Pagine**
    + **Frasi chiave**
1. Immettere la **stringa di connessione dell'account di archiviazione** salvata in un passaggio precedente.

    ![Configurazione dell'archivio conoscenze](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurazione dell'archivio conoscenze")

1. Facoltativamente, scaricare un modello di Power BI. Quando si accede al modello dalla procedura guidata, il file con estensione pbit locale viene adattato alla forma dei dati.

1. Passare alla pagina successiva.

### <a name="step-3-configure-the-index"></a>Passaggio 3: Configurare l'indice

In questo passaggio della procedura guidata si configura un indice per query di ricerca full-text facoltative. La procedura guidata campiona l'origine dati per dedurre i campi e i tipi di dati. Non occorre fare altro che selezionare gli attributi per il comportamento desiderato. Ad esempio, l'attributo **Recuperabile** consentirà al servizio di ricerca di restituire un valore di campo, mentre l'attributo **Ricercabile** abiliterà la ricerca full-text nel campo.

1. In **Nome indice** immettere `hotel-reviews-idx`.
1. Per gli attributi, accettare le selezioni predefinite: **Recuperabile** e **Ricercabile** per i nuovi campi creati dalla pipeline.

    L'indice dovrebbe essere simile all'immagine seguente. Poiché l'elenco è lungo, non tutti i campi sono visibili nell'immagine.

    ![Configurazione di un indice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurazione di un indice")

1. Passare alla pagina successiva.

### <a name="step-4-configure-the-indexer"></a>Passaggio 4: Configurare l'indicizzatore

In questo passaggio della procedura guidata si configurerà un indicizzatore che riunisce l'origine dati, il set di competenze e l'indice definiti nei passaggi precedenti della procedura guidata.

1. Per **Nome** immettere `hotel-reviews-idxr`.
1. In **Pianificazione** mantenere l'impostazione predefinita **Una sola volta**.
1. Fare clic su **Invia** per eseguire l'indicizzatore. Le operazioni di estrazione dei dati, indicizzazione e applicazione delle competenze cognitive vengono eseguite tutte in questo passaggio.

## <a name="monitor-status"></a>Monitorare lo stato

Il completamento dell'indicizzazione delle competenze cognitive richiede più tempo rispetto all'indicizzazione tipica basata su testo. La procedura guidata dovrebbe aprire l'elenco degli indicizzatori nella pagina di panoramica in modo che sia possibile monitorare lo stato di avanzamento. In caso contrario, passare alla pagina Panoramica e fare clic su **Indicizzatori**.

Nel portale di Azure è anche possibile monitorare il log attività Notifiche per rilevare la presenza di un collegamento di stato **Notifica di Ricerca cognitiva di Azure** selezionabile. Il completamento dell'esecuzione può richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Ora che i dati sono stati arricchiti mediante Servizi cognitivi e i risultati sono stati proiettati in un archivio conoscenze, è possibile usare Storage Explorer o Power BI per esplorare il set di dati arricchito.

È possibile visualizzare il contenuto in Storage Explorer o eseguire un'ulteriore operazione con Power BI per ottenere informazioni dettagliate tramite la visualizzazione.

> [!div class="nextstepaction"]
> [Visualizzare con Storage Explorer](knowledge-store-view-storage-explorer.md)
> [Connettersi con Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Se si vuole ripetere questo esercizio o provare un'altra procedura dettagliata per gli arricchimenti di intelligenza artificiale, eliminare l'indicizzatore *hotel-reviews-idxr*. Eliminando l'indicizzatore, il contatore delle transazioni giornaliere gratuite viene azzerato per l'elaborazione di Servizi cognitivi.
