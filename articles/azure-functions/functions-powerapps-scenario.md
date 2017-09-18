---
title: Chiamare una funzione da PowerApps | Microsoft Docs
description: Creare un connettore personalizzato e quindi chiamare una funzione usando tale connettore.
services: functions
keywords: app cloud, servizi cloud, PowerApps, processi aziendali, applicazione aziendale
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 0fd127293edfaf7eb19a4561ab8d0a19f993bcad
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---

# <a name="call-a-function-from-powerapps"></a>Chiamare una funzione da PowerApps
La piattaforma [PowerApps](https://powerapps.microsoft.com) è progettata per consentire agli esperti aziendali di creare app senza il tradizionale codice delle applicazioni. Gli sviluppatori professionisti possono usare Funzioni di Azure per estendere le funzionalità di PowerApps, evitando ai creatori di app PowerApps i dettagli tecnici.

In questo argomento si crea un'app basata su uno scenario di manutenzione per le turbine eoliche. L'argomento mostra come chiamare la funzione definita in [Creare una definizione OpenAPI per una funzione](functions-openapi-definition.md). La funzione determina se una riparazione urgente di una turbina eolica è conveniente.

![App finita in PowerApps](media/functions-powerapps-scenario/finished-app.png)

In questo argomento si apprenderà come:

> [!div class="checklist"]
> * Preparare i dati di esempio in Excel.
> * Esportare una definizione API.
> * Aggiungere una connessione all'API.
> * Creare un'app e aggiungere origini dati.
> * Aggiungere controlli per visualizzare i dati nell'app.
> * Aggiungere controlli per chiamare la funzione e visualizzare i dati.
> * Eseguire l'app per determinare se una riparazione è conveniente.

## <a name="prerequisites"></a>Prerequisiti

+ Un [account PowerApps](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md) attivo con le stesse credenziali di accesso dell'account Azure. 
+ Excel, che verrà usato come origine dati per l'app.
+ Completare l'esercitazione [Creare una definizione OpenAPI per una funzione](functions-openapi-definition.md).


## <a name="prepare-sample-data-in-excel"></a>Preparare i dati di esempio in Excel
Prima di tutto, preparare i dati di esempio usati nell'app. Copiare la tabella seguente in Excel. 

| Titolo      | Latitudine  | Longtitude  | LastServiceDate | MaxOutput | ServiceRequired | EstimatedEffort | InspectionNotes                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| Turbine 1  | 47.438401 | -121.383767 | 2/23/2017       | 2850      | Sì             | 6               | This is the second issue this month.       |
| Turbine 4  | 47.433385 | -121.383767 | 5/8/2017        | 5400      | Sì             | 6               |                                            |
| Turbine 33 | 47.428229 | -121.404641 | 6/20/2017       | 2800      |                 |                 |                                            |
| Turbine 34 | 47.463637 | -121.358824 | 2/19/2017       | 2800      | Sì             | 7               |                                            |
| Turbine 46 | 47.471993 | -121.298949 | 3/2/2017        | 1200      |                 |                 |                                            |
| Turbine 47 | 47.484059 | -121.311171 | 8/2/2016        | 3350      |                 |                 |                                            |
| Turbine 55 | 47.438403 | -121.383767 | 10/2/2016       | 2400      | Sì             | 40               | We have some parts coming in for this one. |

1. In Excel selezionare i dati e nella scheda **Home** fare clic su **Formatta come tabella**.

    ![Formatta come tabella](media/functions-powerapps-scenario/format-table.png)

1. Selezionare uno stile e fare clic su **OK**.

1. Con la tabella selezionata, nella scheda **Progettazione** immettere `Turbines` per **Nome tabella**.

    ![Nome tabella](media/functions-powerapps-scenario/table-name.png)

1. Salvare la cartella di lavoro di Excel.

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Aggiungere una connessione all'API
L'API personalizzata (nota anche come connettore personalizzato) è disponibile in PowerApps, ma è prima necessario stabilire una connessione all'API per poterla usare in un'app.

1. In [web.powerapps.com](https://web.powerapps.com) fare clic su **Connessioni**.

    ![Connessioni di PowerApps](media/functions-powerapps-scenario/powerapps-connections.png)

1. Fare clic su **Nuova connessione**, scorrere verso il basso fino al connettore **Turbine Repair** e quindi fare clic su di esso.

    ![Nuova connessione](media/functions-powerapps-scenario/new-connection.png)

1. Immettere la chiave API e fare clic su **Crea**.

    ![Creare la connessione](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Se si condivide l'app con altri utenti, ogni persona che lavora all'app o la usa deve immettere anche la chiave API per la connessione all'API. Questo comportamento potrebbe cambiare in futuro e questo argomento verrà aggiornato di conseguenza.

## <a name="create-an-app-and-add-data-sources"></a>Creare un'app e aggiungere origini dati
A questo punto si è pronti per creare l'app in PowerApps e aggiungere i dati di Excel e l'API personalizzata come origini dati per l'app.

1. In [web.powerapps.com](https://web.powerapps.com), nel riquadro sinistro, fare clic su **Nuova app**.

1. In **App vuota** fare clic su **Layout del telefono**.

    ![Creare un'app per tablet](media/functions-powerapps-scenario/create-phone-app.png)

    L'app viene aperta in PowerApps Studio per il Web. La figura seguente mostra le diverse parti di PowerApps Studio. Questa immagine si riferisce all'app finita. All'inizio, nel riquadro centrale verrà visualizzata una schermata vuota.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) Barra di spostamento sinistra**, in cui viene mostrata una visualizzazione gerarchica di tutti i controlli in ogni schermata

    **(2) Riquadro centrale**, che mostra la schermata in cui si sta lavorando

    **(3) Riquadro destro**, dove è possibile impostare opzioni come il layout e le origini dati

    **(4) Proprietà**, elenco a discesa in cui si selezionano le proprietà a cui si applicano le formule

    **(5) Barra della formula**, dove è possibile aggiungere formule (come in Excel) che definiscono il comportamento dell'app
    
    **(6) Barra multifunzione**, dove è possibile aggiungere i controlli e personalizzare gli elementi di progettazione

1. Aggiungere il file di Excel come origine dati.

    1. Nel riquadro destro, nella scheda **Dati**, fare clic su **Aggiungi origine dati**.

        ![Aggiungere l'origine dati](media/functions-powerapps-scenario/add-data-source.png)

    1. Fare clic su **Aggiunge dati statici all'app**.

        ![Aggiungere l'origine dati](media/functions-powerapps-scenario/add-static-data.png)

        In genere si leggono e si scrivono i dati da un'origine esterna, ma si stanno aggiungendo i dati di Excel come dati statici in quanto si tratta di un esempio.

    1. Passare al file di Excel salvato, selezionare la tabella **Turbines** e fare clic su **Connetti**.

        ![Aggiungere l'origine dati](media/functions-powerapps-scenario/choose-table.png)

1. Aggiungere l'API personalizzata come origine dati.

    1. Nella scheda **Dati** fare clic su **Aggiungi origine dati**.

    1. Fare clic su **Turbine Repair**.

        ![Connettore Turbine Repair](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Aggiungere controlli per visualizzare i dati nell'app
Ora che le origini dati sono disponibili nell'app, aggiungere una schermata all'app in modo da poter visualizzare i dati della turbina.

1. Nella scheda **Home** fare clic su **Nuova schermata** > **Schermata Elenco**.

    ![Schermata Elenco](media/functions-powerapps-scenario/list-screen.png)

    PowerApps aggiunge una schermata che contiene una *raccolta* per visualizzare gli elementi e altri controlli che consentono la ricerca, l'ordinamento e il filtro.

1. Modificare la barra del titolo in `Turbine Repair` e ridimensionare la raccolta in modo che ci sia spazio per altri controlli.

    ![Modificare il titolo e ridimensionare la raccolta](media/functions-powerapps-scenario/gallery-title.png)

1. Con la raccolta selezionata, nella scheda **Dati** nel riquadro destro modificare l'origine dati da **CustomGallerySample** a **Turbines**.

    ![Modificare l'origine dati](media/functions-powerapps-scenario/change-data-source.png)

    Il set di dati non contiene un'immagine, quindi si modifica il layout per adattarlo meglio ai dati. 

1. Sempre nel riquadro destro modificare **Layout** in **Titolo, sottotitolo e corpo**.

    ![Modificare il layout della raccolta](media/functions-powerapps-scenario/change-layout.png)

1. Come ultimo passaggio nel riquadro destro modificare i campi visualizzati nella raccolta.

    ![Modificare i campi della raccolta](media/functions-powerapps-scenario/change-fields.png)
    
    + **Corpo1** = LastServiceDate
    + **Sottotitolo2** = ServiceRequired
    + **Titolo2** = Title 

1. Con la raccolta selezionata, impostare la proprietà **TemplateFill** sulla formula seguente: `If(ThisItem.IsSelected, Orange, White)`.

    ![Formula di riempimento modello](media/functions-powerapps-scenario/formula-fill.png)

    A questo punto, è più facile vedere quale elemento della raccolta è selezionato.

    ![Elemento selezionato](media/functions-powerapps-scenario/selected-item.png)

1. Non è necessaria la schermata originale nell'app. Nel riquadro sinistro passare il puntatore su **Schermata1** e fare clic su **. . .** e quindi su **Elimina**.

    ![Eliminare la schermata](media/functions-powerapps-scenario/delete-screen.png)

In un'app di produzione ci sarebbero molte altre opzioni di formattazione da impostare, ma al momento si passerà alla parte importante di questo scenario, la chiamata della funzione.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Aggiungere controlli per chiamare la funzione e visualizzare i dati
Si ha un'app che mostra i dati di riepilogo per ogni turbina, quindi è possibile aggiungere i controlli per chiamare la funzione creata e visualizzare i dati restituiti. Si accede alla funzione in base al nome scelto nella definizione OpenAPI. In questo caso, si usa `TurbineRepair.CalculateCosts()`.

1. Sulla barra multifunzione, nella scheda **Inserisci**, fare clic su **Pulsante**. Nella stessa scheda fare quindi clic su **Etichetta**

    ![Inserire un pulsante e un'etichetta](media/functions-powerapps-scenario/insert-controls.png)

1. Trascinare il pulsante e l'etichetta sotto la raccolta e ridimensionare l'etichetta. 

1. Selezionare il testo del pulsante e modificarlo in `Calculate costs`. L'app dovrebbe essere simile all'immagine seguente.

    ![App con pulsante](media/functions-powerapps-scenario/move-button-label.png)

1. Selezionare il pulsante e immettere la formula seguente per la proprietà **OnSelect** del pulsante.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Questa formula viene eseguita quando si fa clic sul pulsante ed esegue le operazioni seguenti, se il valore **ServiceRequired** dell'elemento della raccolta è `Yes`:

    + Cancella la *raccolta* `DetermineRepair` per rimuovere i dati delle chiamate precedenti. Una raccolta è una variabile tabulare.

    + Assegna alla raccolta di dati restituiti chiamando la funzione `TurbineRepair.CalculateCosts()`. 
    
        I valori passati alla funzione provengono dai campi **EstimatedEffort** e **MaxOutput** per l'elemento selezionato nella raccolta. Questi campi non vengono visualizzati nella raccolta, ma sono comunque disponibili per l'uso nelle formule.

1. Selezionare l'etichetta e immettere la formula seguente per la proprietà **Text** dell'etichetta.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Questa formula usa la funzione `First()` per accedere alla prima (e unica) riga della raccolta `DetermineRepair`. Visualizza quindi i tre valori restituiti dalla funzione: `message`, `costToFix` e `revenueOpportunity`. Questi valori sono vuoti prima della prima esecuzione dell'app.

    L'app completata dovrebbe essere simile all'immagine seguente.

    ![App completata prima dell'esecuzione](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Esecuzione dell'app
L'app è stata completata. A questo punto è possibile eseguirla e vedere le chiamate della funzione in azione.

1. Nell'angolo superiore destro di PowerApps Studio fare clic sul pulsante di esecuzione: ![Pulsante di esecuzione dell'app](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Selezionare una turbina con un valore di `Yes` per **ServiceRequired** e quindi fare clic sul pulsante **Calculate costs**. Verrà visualizzato un risultato simile all'immagine seguente.

    ![App finita in PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Provare con le altre turbine per vedere cosa viene restituito dalla funzione ogni volta.

## <a name="next-steps"></a>Passaggi successivi
In questo argomento si è appreso come:

> [!div class="checklist"]
> * Preparare i dati di esempio in Excel.
> * Esportare una definizione API.
> * Aggiungere una connessione all'API.
> * Creare un'app e aggiungere origini dati.
> * Aggiungere controlli per visualizzare i dati nell'app.
> * Aggiungere controlli per chiamare la funzione e visualizzare i dati
> * Eseguire l'app per determinare se una riparazione è conveniente.

Per altre informazioni su PowerApps, vedere [Introduzione a PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Per informazioni su un altro scenario interessante che usa Funzioni di Azure, vedere [Creare una funzione che si integra con le app per la logica di Azure](functions-twitter-email.md).
