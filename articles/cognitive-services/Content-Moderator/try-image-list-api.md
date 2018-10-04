---
title: Moderare le immagini con elenchi personalizzati con la console dell'API - Content Moderator
titlesuffix: Azure Content Moderator
description: Eseguire il test drive di elenchi personalizzati di immagini nella console dell'API di Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 5e573262879b6a16e3cd530757900b86228ae299
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219958"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderare le immagini con elenchi personalizzati nella console dell'API

È possibile usare l'[API di gestione degli elenchi](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) di Azure Content Moderator per creare elenchi personalizzati di immagini da usare con l'API di moderazione delle immagini. L'operazione di moderazione consiste nella valutazione di un'immagine. Se si creano elenchi personalizzati, l'immagine viene anche confrontata con le immagini incluse in tali elenchi. È così possibile decidere se bloccarla o consentirne la visualizzazione.

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di immagini** e ogni elenco **non può includere più di 10.000 immagini**.
>

L'API di gestione degli elenchi consente di eseguire queste attività:

- Crea un elenco
- Aggiungere immagini a un elenco.
- Filtrare le immagini rispetto a quelle incluse in un elenco.
- Eliminare le immagini da un elenco.
- Eliminare un elenco.
- Modificare le informazioni di un elenco.
- Aggiornare l'indice in modo da includere le modifiche apportate all'elenco in una nuova analisi.

## <a name="use-the-api-console"></a>Usare la console dell'API
Prima di poter eseguire il test drive dell'API nella console online, è necessario avere la chiave di sottoscrizione. Questa si trova nella casella **Ocp-Apim-Subscription-Key** della scheda **Settings** (Impostazioni). Per altre informazioni, vedere la [panoramica](overview.md).

## <a name="refresh-search-index"></a>Aggiornare l'indice di ricerca

Dopo avere modificato un elenco di immagini, è necessario aggiornarne l'indice in modo da includere le modifiche nelle analisi successive. Questo passaggio è paragonabile al modo in cui un motore di ricerca sul desktop (se abilitato) o un motore di ricerca Web aggiorna continuamente il proprio indice per includere nuovi file o pagine.

1.  Nella pagina di [riferimento dell'API di gestione degli elenchi di immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) selezionare **Image Lists** (Elenchi di immagini) nel menu a sinistra e quindi **Refresh Search Index** (Aggiorna indice di ricerca).

  Viene visualizzata la pagina **Image Lists - Refresh Search Index** (Elenchi di immagini - Aggiorna indice di ricerca).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 
 
    ![Selezione dell'area nella pagina Image Lists - Refresh Search Index (Elenchi di immagini - Aggiorna indice di ricerca)](images/test-drive-region.png)

    Viene visualizzata la console dell'API **Image Lists - Refresh Search Index** (Elenchi di immagini - Aggiorna indice di ricerca).

3.  Nella casella **listId** immettere l'ID dell'elenco. Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia).

  ![Casella del contenuto della risposta nella console Image Lists - Refresh Search Index (Elenchi di immagini - Aggiorna indice di ricerca)](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Creare un elenco di immagini

1.  Passare alla pagina di [riferimento dell'API di gestione degli elenchi di immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  Viene visualizzata la pagina **Image Lists - Create** (Elenchi di immagini - Crea). 

3.  Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.

    ![Selezione dell'area nella pagina Image Lists - Create (Elenchi di immagini - Crea)](images/test-drive-region.png)

    Viene visualizzata la console dell'API **Image Lists - Create** (Elenchi di immagini - Crea).
 
4.  Nella casella **Ocp-Apim-Subscription-Key** immettere la chiave di sottoscrizione.

5.  Nella casella **Request body** (Corpo della richiesta) immettere i valori per **Name** (Nome), ad esempio MyList, e **Description** (Descrizione).

  ![Nome e descrizione del corpo della richiesta nella console Image Lists - Create (Elenchi di immagini - Crea)](images/try-terms-list-create-1.png)

6.  Usare i segnaposto della coppia chiave-valore per assegnare metadati più descrittivi all'elenco.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Aggiungere i metadati dell'elenco come coppie chiave-valore e non le immagini effettive.
 
7.  Selezionare **Send** (Invia). L'elenco è stato creato. Si noti il valore di **ID** associato al nuovo elenco. Questo ID è necessario per altre funzioni di gestione degli elenchi di immagini.

  ![ID dell'elenco visualizzato nella casella del contenuto della risposta nella console Image Lists - Create (Elenchi di immagini - Crea)](images/try-terms-list-create-2.png)
 
8.  Aggiungere quindi le immagini a MyList. Nel menu a sinistra selezionare **Image** (Immagine) e quindi **Add Image** (Aggiungi immagine).

  Viene visualizzata la pagina **Image - Add Image** (Immagine - Aggiungi immagine). 

9. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.

  ![Selezione dell'area nella pagina Image - Add Image (Immagine - Aggiungi immagine)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Image - Add Image** (Immagine - Aggiungi immagine).
 
10. Nella casella **listId** immettere l'ID elenco che è stato generato e quindi specificare l'URL dell'immagine che si vuole aggiungere. Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia).

11. Per verificare che l'immagine sia stata aggiunta all'elenco, nel menu a sinistra selezionare **Image** (Immagine) e quindi **Get All Image Ids** (Ottieni tutti gli ID immagine).

  Viene visualizzata la console dell'API **Image - Get All Image Ids** (Immagine - Ottieni tutti gli ID immagine).
  
12. Nella casella **listId** immettere l'ID dell'elenco e quindi la chiave di sottoscrizione. Selezionare **Send** (Invia).

  ![Elenco delle immagini specificate nella casella del contenuto della risposta nella console Image - Get All Image Ids (Immagine - Ottieni tutti gli ID immagine)](images/try-image-list-create-11.png)
 
10. Aggiungere altre immagini. Ora che è stato creato un elenco personalizzato di immagini, provare a [valutare le immagini](try-image-api.md) usando questo elenco. 

## <a name="delete-images-and-lists"></a>Eliminare immagini ed elenchi

L'eliminazione di un'immagine o di un elenco è un'operazione semplice. È possibile usare l'API per eseguire queste attività:

- Eliminare un'immagine. **Image - Delete** (Immagine - Elimina)
- Eliminare tutte le immagini incluse in un elenco senza eliminare l'elenco: **Image - Delete All Images** (Immagine - Elimina tutte le immagini)
- Eliminare un elenco e tutto il contenuto: **Image Lists - Delete** (Elenchi di immagini - Elimina)

Questo esempio elimina una singola immagine:

1. Nella pagina di [riferimento dell'API di gestione degli elenchi di immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) selezionare **Image** (Immagine) nel menu a sinistra e quindi **Delete** (Elimina). 

  Viene visualizzata la pagina **Image - Delete** (Immagine - Elimina).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Image - Delete (Immagine - Elimina)](images/test-drive-region.png)
 
  Viene visualizzata la console dell'API **Image - Delete** (Immagine - Elimina).
 
3.  Nella casella **listId** immettere l'ID dell'elenco da cui si vuole eliminare un'immagine.  Questo ID corrisponde al numero che viene restituito nella console **Image - Get All Image Ids** (Immagine - Ottieni tutti gli ID immagine) per MyList. Immettere quindi il valore di **Image Id** dell'immagine da eliminare. 

In questo esempio l'ID dell'elenco è **58953**, ovvero il valore per **ContentSource**. L'ID dell'immagine è **59021**, ovvero il valore per **ContentIds**.

4.  Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia).

5.  Per verificare che l'immagine sia stata eliminata, usare la console **Image - Get All Image Ids** (Immagine - Ottieni tutti gli ID immagine).
 
## <a name="change-list-information"></a>Modificare le informazioni di un elenco

È possibile modificare il nome e la descrizione di un elenco e aggiungere elementi di metadati.

1.  Nella pagina di [riferimento dell'API di gestione degli elenchi di immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) selezionare **Image Lists** (Elenchi di immagini) nel menu a sinistra e quindi **Update Details** (Aggiorna dettagli). 

  Viene visualizzata la pagina **Image Lists - Update Details** (Elenchi di immagini - Aggiorna dettagli).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.  

    ![Selezione dell'area nella pagina Image Lists - Update Details (Elenchi di immagini - Aggiorna dettagli)](images/test-drive-region.png)

    Viene visualizzata la console dell'API **Image Lists - Update Details** (Elenchi di immagini - Aggiorna dettagli).
 
3.  Nella casella **listId** immettere l'ID dell'elenco e quindi la chiave di sottoscrizione.

4.  Nella casella **Request body** (Corpo della richiesta) apportare le modifiche e quindi selezionare il pulsante **Send** (Invia) nella pagina.

  ![Modifiche del corpo della richiesta nella console Image Lists - Update Details (Elenchi di immagini - Aggiorna dettagli)](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la [guida rapida agli elenchi di immagini per .NET](image-lists-quickstart-dotnet.md) per procedere all'integrazione con la propria applicazione.
