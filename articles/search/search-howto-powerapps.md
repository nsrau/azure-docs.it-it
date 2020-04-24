---
title: Come eseguire query ricerca cognitiva di Azure da Power Apps
titleSuffix: Azure Cognitive Search
description: Istruzioni dettagliate su come creare un connettore personalizzato per ricerca cognitiva e come visualizzarlo da un'app Power
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385113"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Come eseguire query su un indice di ricerca cognitiva da Power Apps

Questo documento illustra come creare un connettore personalizzato di Power Apps per poter recuperare i risultati della ricerca da un indice di ricerca. Viene anche illustrato come eseguire una query di ricerca e visualizzare i risultati da un'app Power. 

## <a name="prerequisites"></a>Prerequisiti
*    Accesso all'account Power Apps con la possibilità di creare connettori personalizzati.
*    Si presuppone che sia già stato creato un indice di ricerca di Azure.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Creare un connettore personalizzato per eseguire query in ricerca di Azure

Esistono due passaggi principali per avere un PowerApp che mostra i risultati di ricerca cognitiva di Azure. Prima di tutto, creiamo un connettore in grado di eseguire query sull'indice di ricerca. Nella [sezione successiva](#visualize-results-from-the-custom-connector) verrà aggiornata l'applicazione Power Apps per visualizzare i risultati restituiti dal connettore.

1. Passare a [make.powerapps.com](http://make.powerapps.com) ed eseguire l' **accesso**.

1. Ricerca di**connettori personalizzati** per **i dati** > 
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu connettore personalizzato" border="true":::

1. Fare clic su **+ nuovo connettore personalizzato** , quindi selezionare **Crea da zero**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Crea dal menu vuoto" border="true":::

1. Assegnare un nome al connettore personalizzato. (ovvero, *AzureSearchQuery*), quindi fare clic su **continue (continua**). Verrà creata una procedura guidata per creare il nuovo connettore.

1. Immettere le informazioni nella pagina generale.

    - Colore di sfondo dell'icona (ad esempio, #007ee5)
    - Descrizione (ad esempio, "un connettore ad Azure ricerca cognitiva")
    - Nell'host sarà necessario immettere l'URL del servizio di ricerca (ad esempio, `<yourservicename>.search.windows.net`)
    - Per URL di base, è sufficiente immettere "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialogo informazioni generali" border="true":::

1. Nella pagina sicurezza impostare *chiave API* come **tipo di autenticazione**, impostare l'etichetta del parametro e i campi nome parametro come *API-Key*. In **percorso parametri**selezionare *intestazione* come illustrato di seguito.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opzione tipo di autenticazione" border="true":::

1. Nella pagina definizioni selezionare **+ nuova azione** per creare un'azione che eseguirà una query sull'indice. Immettere il valore "query" per il riepilogo e il nome dell'ID operazione. Immettere una descrizione, ad esempio *"query nell'indice di ricerca"*.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Opzioni nuova azione" border="true":::


1. Premere il pulsante **+ Importa da esempio** per definire i parametri e le intestazioni. Verrà quindi definita la richiesta di query.  

    * Selezionare il verbo`GET`
    * Per l'URL, immettere una query di esempio per l'indice di ricerca, ad esempio:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=* &API-Version = 2019-05-06-Preview
    

    **Power Apps** userà la sintassi per estrarre i parametri dalla query. Si noti che il campo di ricerca è stato definito in modo esplicito. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importa da esempio" border="false":::

1.  Fare clic su **Importa** per eseguire automaticamente il pre-riempimento della finestra di dialogo di richiesta.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Finestra di dialogo Importa da esempio" border="false":::


1. Per completare l'impostazione dei metadati del parametro, fare clic su **...** simbolo accanto a ognuno dei parametri.

    - Per *la ricerca*: `*` impostare come **valore predefinito**, impostare **required** su *false* e impostare la **visibilità** su *None*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Cerca metadati parametro" border="true":::

    - Per *API-Version*: impostare `2019-05-06-Preview` come **valore predefinito**, impostare la **visibilità** come Internal e impostare **required** su *true*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadati del parametro della versione" border="true":::

    - Analogamente, per la *chiave API*, impostarla come **obbligatoria**, con **visibilità** *interna* . Immettere la chiave API del servizio di ricerca come **valore predefinito**.
    
    Dopo avere apportato queste modifiche, impostare la visualizzazione dell' **editor di spavalderia** . Nella sezione Parameters dovrebbe essere visualizzata la seguente configurazione:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Nella sezione risposta fare clic su **"Aggiungi risposta predefinita"**. Questo è fondamentale perché consente alle **app Power** di comprendere lo schema della risposta. Incollare una risposta di esempio.

    > [!TIP] 
    > È presente un limite di caratteri per la risposta JSON che è possibile immettere, quindi è consigliabile semplificare il codice JSON in modo che prima di incollarlo. Schema/formato di aspetto importante della risposta. I valori effettivi nella risposta di esempio sono meno importanti e possono essere semplificati per ridurre il numero di caratteri.
    

1.    Fare clic sul pulsante **Crea connettore** nella parte superiore destra della schermata prima di poterlo testare.

1.  Nella pagina test fare clic su **+ nuova connessione**e immettere la chiave di query del servizio di ricerca come valore per *chiave API*.

    Questo passaggio consente di passare all'esterno della procedura guidata e alla pagina connessioni. È possibile tornare all'editor delle connessioni personalizzate per testare effettivamente la connessione. Passare a **connettore personalizzato** > selezionare il > connettore appena creato *...* > **Visualizzare le proprietà** > **modifica** > **4. **Eseguire un test per tornare alla pagina di test.

1.    Fare clic su **test operazione** per assicurarsi di ottenere risultati dall'indice. Se l'operazione ha esito positivo, verrà visualizzato lo stato 200 e nel corpo della risposta verrà visualizzato JSON che descrive i risultati della ricerca.




## <a name="visualize-results-from-the-custom-connector"></a>Visualizzare i risultati del connettore personalizzato
L'obiettivo di questa esercitazione non è mostrare come creare esperienze utente accattivanti con Power Apps, quindi il layout dell'interfaccia utente sarà minimalista. Creare un PowerApp con una casella di ricerca, un pulsante di ricerca e visualizzare i risultati in un controllo raccolta.  Il PowerApp si connetterà al connettore personalizzato creato di recente per ottenere i dati da ricerca di Azure.

1. Creare una nuova app Power app. Passare alla sezione **app** , fare clic su **+ nuova app**e selezionare **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Crea app Canvas" border="true":::

1. Selezionare il tipo di applicazione che si desidera. Per questa esercitazione creare un' **app vuota** con il **layout del telefono**. Verrà visualizzato **Power Apps Studio** .

1. In Studio selezionare la scheda **origini dati** e fare clic sul nuovo connettore appena creato. In questo caso, è denominato *AzureSearchQuery*. Fare clic su **Aggiungi connessione**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="Connetti connettore" border="true":::

    Ora *AzureSearchQuery* è un'origine dati disponibile per l'uso da un'applicazione.
    
1. Passare alla **scheda Inserisci**, in modo che sia possibile aggiungere alcuni controlli al modulo.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserire i controlli" border="true":::

1.  Inserire gli elementi seguenti:
    -   Etichetta di testo con il valore "query:"
    -   Un elemento input di testo (chiamato *txtQuery*, valore predefinito: "*")
    -   Un pulsante con il testo "Search" 
    -   Una raccolta verticale denominata (chiamata *galleryResults*)
    
    Il form dovrebbe avere un aspetto simile al seguente:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layout controlli" border="true":::

1. Per fare in modo che il **pulsante di ricerca** rilasciasse una query, selezionare il pulsante e incollare l'azione seguente per eseguire **onselect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Pulsante OnSelect" border="true":::
 
    Questa azione comporterà l'aggiornamento di una nuova raccolta denominata *azResult* con il risultato della query di ricerca, usando il testo nella casella di testo *txtQuery* come termine della query.
    
1.  Come passaggio successivo, si collegherà la raccolta verticale creata alla raccolta *azResult* . Selezionare il controllo raccolta ed eseguire le azioni seguenti nel riquadro proprietà.

    -  Impostare **DataSource** su *azResult*.
    
    -  Selezionare un **layout** che funzioni in base al tipo di dati nell'indice. In questo caso, è stato usato il *titolo, il sottotitolo e* il layout del corpo.
    
    -  **Modificare i campi**e selezionare i campi che si desidera visualizzare.

    Poiché è stato fornito un risultato di esempio quando è stato definito il connettore, l'app è in grado di riconoscere i campi disponibili nell'indice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campi della raccolta" border="true":::   
 
1.  Premere **F5** per visualizzare l'anteprima dell'app.  

    Tenere presente che i campi possono essere impostati su valori calcolati.      
    Per l'esempio, impostando il layout *"immagine, titolo e sottotitolo"* e specificando la funzione di *immagine* come concatenazione del percorso radice per i dati e il nome del file (ad `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`esempio,) produrrà il risultato riportato di seguito.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="App finale" border="true":::        

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni e corsi di formazione online, vedere catalogo di formazione per [Power Apps](https://docs.microsoft.com/powerapps/learning-catalog/get-started).

