---
title: Eseguire la moderazione del testo con elenchi personalizzati di termini - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare l'API di gestione degli elenchi per creare elenchi personalizzati di termini da usare con l'API di moderazione del testo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 733aae0fe7bd11dfb5c41b7c3d15838a76ab5834
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870200"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderare il testo con elenchi personalizzati nella console dell'API

L'elenco globale di termini predefinito in Azure Content Moderator è sufficiente per quasi tutte le esigenze di moderazione del contenuto. Può tuttavia essere necessario filtrare termini specifici per la propria organizzazione. Può ad esempio essere opportuno contrassegnare con tag i nomi delle società concorrenti per una successiva analisi. 

Usare l'[API di gestione degli elenchi](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) per creare elenchi personalizzati di termini da usare con l'API di moderazione del testo. L'operazione **Text - Screen** (Testo - Filtra) analizza il testo per rilevare eventuale contenuto volgare e lo confronta con blacklist personalizzate e condivise.

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di termini** e ogni elenco **non può includere più di 10.000 immagini**.
>

L'API di gestione degli elenchi consente di eseguire queste attività:
- Crea un elenco
- Aggiungere termini a un elenco.
- Filtrare i termini rispetto a quelli inclusi in un elenco.
- Eliminare i termini da un elenco.
- Eliminare un elenco.
- Modificare le informazioni di un elenco.
- Aggiornare l'indice in modo da includere le modifiche apportate all'elenco in una nuova analisi.

## <a name="use-the-api-console"></a>Usare la console dell'API

Prima di poter eseguire il test drive dell'API nella console online, è necessario disporre della chiave di sottoscrizione. Questa si trova nella casella **Ocp-Apim-Subscription-Key** della scheda **Settings** (Impostazioni). Per altre informazioni, vedere la [panoramica](overview.md).

## <a name="refresh-search-index"></a>Aggiornare l'indice di ricerca

Dopo avere modificato un elenco di termini, è necessario aggiornarne l'indice in modo da includere le modifiche nelle analisi successive. Questo passaggio è paragonabile al modo in cui un motore di ricerca sul desktop (se abilitato) o un motore di ricerca Web aggiorna continuamente il proprio indice per includere nuovi file o pagine.

1.  Nella pagina di [riferimento dell'API di gestione degli elenchi di termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) selezionare **Term Lists** (Elenchi di termini) nel menu a sinistra e quindi **Refresh Search Index** (Aggiorna indice di ricerca). 

  Viene visualizzata la pagina **Term Lists - Refresh Search Index** (Elenchi di termini - Aggiorna indice di ricerca).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Term Lists - Refresh Search Index (Elenchi di termini - Aggiorna indice di ricerca)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Term Lists - Refresh Search Index** (Elenchi di termini - Aggiorna indice di ricerca).

3.  Nella casella **listId** immettere l'ID dell'elenco. Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia).

  ![Casella del contenuto della risposta nella console dell'API Term Lists - Refresh Search Index (Elenchi di termini - Aggiorna indice di ricerca)](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Creare un elenco di termini
1.  Passare alla pagina di [riferimento dell'API di gestione degli elenchi di termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  Viene visualizzata la pagina **Term Lists - Create** (Elenchi di termini - Crea).

2.  Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Term Lists - Create (Elenchi di termini - Crea)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Term Lists - Create** (Elenchi di termini - Crea).
 
3.  Nella casella **Ocp-Apim-Subscription-Key** immettere la chiave di sottoscrizione.

4.  Nella casella **Request body** (Corpo della richiesta) immettere i valori per **Name** (Nome), ad esempio MyList, e **Description** (Descrizione).

  ![Nome e descrizione del corpo della richiesta nella console Term Lists - Create (Elenchi di termini - Crea)](images/try-terms-list-create-1.png)

5.  Usare i segnaposto della coppia chiave-valore per assegnare metadati più descrittivi all'elenco.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Aggiungere i metadati dell'elenco come coppie chiave-valore e non i termini effettivi.
 
6.  Selezionare **Send** (Invia). L'elenco è stato creato. Si noti il valore di **ID** associato al nuovo elenco. Questo ID è necessario per altre funzioni di gestione degli elenchi di termini.

  ![ID dell'elenco visualizzato nella casella del contenuto della risposta nella console Term Lists - Create (Elenchi di termini - Crea)](images/try-terms-list-create-2.png)
 
7.  Aggiungere termini a MyList. Nel menu a sinistra, sotto **Term** (Termine), selezionare **Add Term** (Aggiungi termine). 

  Viene visualizzata la pagina **Term - Add Term** (Termine - Aggiungi termine). 

8.  Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Term - Add Term (Termine - Aggiungi termine)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Term - Add Term** (Termine - Aggiungi termine).
 
9.  Nella casella **listId** box, immettere l'ID elenco che è stato generato e selezionare un valore per **language**. Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia).

  ![Parametri di query della console Term - Add Term (Termine - Aggiungi termine)](images/try-terms-list-create-3.png)
 
10. Per verificare che il termine sia stato aggiunto all'elenco, nel menu a sinistra selezionare **Term** (Termine) e quindi **Get All Terms** (Ottieni tutti i termini). 

  Viene visualizzata la console dell'API **Term - Get All Terms** (Termine - Ottieni tutti i termini).

11. Nella casella **listId** immettere l'ID dell'elenco e quindi la chiave di sottoscrizione. Selezionare **Send** (Invia).

12. Nella casella **Response content** (Contenuto della risposta) verificare i termini specificati.

  ![Elenco dei termini specificati nella casella del contenuto della risposta nella console Term - Get All Terms (Termine - Ottieni tutti i termini)](images/try-terms-list-create-4.png)
 
13. Aggiungere altri termini. Ora che è stato creato un elenco personalizzato di termini, provare ad [analizzare del testo](try-text-api.md) usando questo elenco. 

## <a name="delete-terms-and-lists"></a>Eliminare termini ed elenchi

L'eliminazione di un termine o di un elenco è un'operazione semplice. È possibile usare l'API per eseguire queste attività:

- Eliminare un termine: **Term - Delete** (Termine - Elimina)
- Eliminare tutti termini inclusi in un elenco senza eliminare l'elenco: **Term - Delete All Terms** (Termine - Elimina tutti i termini)
- Eliminare un elenco e tutto il contenuto: **Term Lists - Delete** (Elenchi di termini - Elimina)

Questo esempio elimina un singolo termine.

1.  Nella pagina di [riferimento dell'API di gestione degli elenchi di termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) selezionare **Term** (Termine) nel menu a sinistra e quindi **Delete** (Elimina). 

  Viene visualizzata la pagina **Term - Delete** (Termine - Elimina).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Term - Delete (Termine - Elimina)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Term - Delete** (Termine - Elimina).
  
3.  Nella casella **listId** immettere l'ID dell'elenco da cui si vuole eliminare un termine. Questo ID corrisponde al numero (**122** nell'esempio) che viene restituito nella console **Term Lists - Get Details** (Elenchi di termini - Ottieni dettagli) per MyList. Immettere il termine e selezionare una lingua.
 
  ![Parametri di query della console Term - Delete (Termine - Elimina)](images/try-terms-list-delete-1.png)

4.  Immettere la chiave di sottoscrizione e quindi selezionare **Send** (Invia).

5.  Per verificare che il termine sia stato eliminato, usare la console **Term Lists - Get All** (Elenchi di termini - Ottieni tutti).

  ![La casella del contenuto della risposta nella console Term Lists - Get All (Elenchi di termini - Ottieni tutti) mostra che il termine è stato eliminato](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Modificare le informazioni di un elenco

È possibile modificare il nome e la descrizione di un elenco e aggiungere elementi di metadati.

1.  Nella pagina di [riferimento dell'API di gestione degli elenchi di termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) selezionare **Term Lists** (Elenchi di termini) nel menu a sinistra e quindi **Refresh Search Index** (Aggiorna indice di ricerca). 

  Viene visualizzata la pagina **Term Lists - Update Details** (Elenchi di termini - Aggiorna dettagli).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Term Lists - Update Details (Elenchi di termini - Aggiorna dettagli)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Term Lists - Update Details** (Elenchi di termini - Aggiorna dettagli).

3.  Nella casella **listId** immettere l'ID dell'elenco e quindi la chiave di sottoscrizione.

4.  Nella casella **Request body** (Corpo della richiesta) apportare le modifiche e quindi selezionare **Send** (Invia).

  ![Modifiche del corpo della richiesta nella console Term Lists - Update Details (Elenchi di termini - Aggiorna dettagli)](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la [guida rapida agli elenchi di termini per .NET](term-lists-quickstart-dotnet.md) per procedere all'integrazione con la propria applicazione.
