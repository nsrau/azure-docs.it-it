---
title: Come eseguire query su Ricerca cognitiva di Azure da Power AppsHow to query Azure Cognitive Search from Power Apps
titleSuffix: Azure Cognitive Search
description: Istruzioni dettagliate su come creare un connettore personalizzato per Ricerca cognitiva e su come visualizzarlo da un'app Power
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
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Come eseguire una query su un indice di Ricerca cognitiva da Power Apps

Questo documento illustra come creare un connettore personalizzato di Power Apps in modo da poter recuperare i risultati della ricerca da un indice di ricerca. Viene inoltre illustrato come eseguire una query di ricerca e visualizzare i risultati da un'app Power.It also shows how to issue a search query and visualize the results from a Power App. 

## <a name="prerequisites"></a>Prerequisiti
*    Accesso all'account di Power Apps con la possibilità di creare connettori personalizzati.
*    Si presuppone che sia già stato creato un indice di Ricerca di Azure.We assume you have already created an Azure Search Index.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Creare un connettore personalizzato per eseguire query su Ricerca di AzureCreate a custom connector to query Azure Search

Esistono due passaggi principali per avere una PowerApp che mostra i risultati di Ricerca cognitiva di Azure.There are two main steps to having a PowerApp that shows Azure Cognitive Search results. In primo luogo, creiamo un connettore in grado di eseguire query sull'indice di ricerca. Nella [sezione successiva](#visualize-results-from-the-custom-connector) verrà aggiornata l'applicazione Power Apps per visualizzare i risultati restituiti dal connettore.

1. Vai a [make.powerapps.com](http://make.powerapps.com) e **Accedi**.

1. Ricerca di**connettori personalizzati** di **dati** > 
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu connettore personalizzato" border="true":::

1. Fare clic su **Nuovo connettore personalizzato** e quindi selezionare **Crea da vuoto**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Crea da menu vuoto" border="true":::

1. Assegnare un nome al connettore personalizzato. (ovvero *AzureSearchQuery*) e quindi fare clic su **Continua**. Verrà visualizzata una procedura guidata per creare il nuovo connettore.

1. Immettere le informazioni nella pagina Generale.

    - Colore di sfondo dell'icona (ad esempio, #007ee5)
    - Descrizione (ad esempio, "Un connettore a Ricerca cognitiva di Azure")
    - Nell'host, è necessario immettere l'URL del `<yourservicename>.search.windows.net`servizio di ricerca (ad esempio, )
    - Per URL di base, è sufficiente immettere "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialogo generale dell'informazione" border="true":::

1. Nella pagina Sicurezza impostare *Chiave API* come Tipo **di autenticazione**, impostare l'etichetta del parametro e i campi del nome del parametro come *chiave api*. Per **Posizione parametro**, selezionare *Intestazione* come illustrato di seguito.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opzione del tipo di autenticazione" border="true":::

1. Nella pagina Definizioni, selezionare **Nuova azione** per creare un'azione che eseguirà una query sull'indice. Immettere il valore "Query" per il riepilogo e il nome dell'ID operazione. Immettere una descrizione come *"Query sull'indice di ricerca".*
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nuove opzioni di azione" border="true":::


1. Per definire i parametri e le intestazioni, premete il pulsante **Importa da esempio.** Successivamente, si definirà la richiesta di query.  

    * Selezionare il verbo`GET`
    * Per l'URL immettere una query di esempio per l'indice di ricerca, ad esempio:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=Versione API &2019-05-06-Anteprima
    

    **Power Apps** utilizzerà la sintassi per estrarre i parametri dalla query. Si noti che il campo di ricerca è stato definito in modo esplicito. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importa da esempio" border="false":::

1.  Fare clic su **Importa** per precompilare automaticamente la finestra di dialogo Richiesta.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importa da finestra di dialogo campione" border="false":::


1. Completare l'impostazione dei metadati dei parametri facendo clic sul pulsante **...** accanto a ciascuno dei parametri.

    - Per *la* `*` ricerca : Impostare come **valore predefinito**, impostare **required** come *false* e impostare la **visibilità** su *none*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadati dei parametri di ricerca" border="true":::

    - Per *api-version* `2019-05-06-Preview` : impostare come **valore predefinito**, impostare la **visibilità** come interna e impostare **required** su *True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadati dei parametri di versione" border="true":::

    - Analogamente, per *api-key*, impostarlo come **richiesto**, con **visibilità** *interna* . Immettere la chiave API del servizio di ricerca come **valore predefinito.**
    
    Dopo aver apportato queste modifiche, passare alla visualizzazione **Editor Swagger.** Nella sezione dei parametri dovrebbe essere visualizzata la seguente configurazione:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Nella sezione Risposta fare clic su **"Aggiungi risposta predefinita".** Questo è fondamentale perché aiuterà **Power Apps** a comprendere lo schema della risposta. Incollare una risposta di esempio.

    > [!TIP] 
    > Esiste un limite di caratteri per la risposta JSON che è possibile immettere, pertanto è consigliabile semplificare il codice JSON in modo che venga incollato prima di incollarlo. Schema/formato dell'aspetto importante della risposta. I valori effettivi nella risposta di esempio sono meno importanti e possono essere semplificati per ridurre il numero di caratteri.
    

1.    Fare clic sul pulsante **Crea connettore** in alto a destra dello schermo prima di poterlo testare.

1.  Nella pagina Di prova fare clic sul pulsante **Nuova connessione**e immettere la chiave di query del servizio di ricerca come valore per *api-key*.

    Questo passaggio può passare alla procedura guidata fuori dalla procedura guidata e alla pagina Connessioni. È possibile tornare all'editor delle connessioni personalizzate per testare effettivamente la connessione. Vai a **Connettore personalizzato** > Selezionare il connettore appena creato > *...* > **Proprietà** > della vista**Modifica** > **4. Eseguire** il test per tornare alla pagina di test.

1.    Fare clic su Operazione di **test** per assicurarsi di ottenere risultati dall'indice. Se l'operazione ha esito positivo dovrebbe essere visualizzata una visualizzazione 200 e nel corpo della risposta dovrebbe essere visualizzato JSON che descrive i risultati della ricerca.




## <a name="visualize-results-from-the-custom-connector"></a>Visualizzare i risultati dal connettore personalizzato
L'obiettivo di questa esercitazione non è mostrare come creare esperienze utente di fantasia con le app per l'alimentazione, pertanto il layout dell'interfaccia utente sarà minimalista. Creiamo una PowerApp con una casella di ricerca, un pulsante di ricerca e visualizziamo i risultati in un controllo raccolta.  The PowerApp will connect to our recently created custom connector to get the data from Azure Search.

1. Creare una nuova Power App. Vai alla sezione **App,** fai clic su **"Nuova app"** e seleziona **Area di disegno.**

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Crea app canvas" border="true":::

1. Selezionare il tipo di applicazione desiderato. Per questa esercitazione creare **un'app vuota** con il **layout del telefono**. Verrà visualizzata **la finestra di power-app Studio.**

1. Una volta in studio, selezionare la scheda **Origini dati** e fare clic sul nuovo connettore appena creato. Nel nostro caso, si chiama *AzureSearchQuery*. Fare clic su **Aggiungi connessione**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="collegare il connettore" border="true":::

    Ora *AzureSearchQuery* è un'origine dati che è disponibile per essere usata dall'applicazione.
    
1. Passare alla **scheda Inserisci**per aggiungere alcuni controlli al modulo.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserire controlli" border="true":::

1.  Inserire i seguenti elementi:
    -   Un'etichetta di testo con il valore "Query:"
    -   Un elemento di input di testo (chiamarlo *txtQuery*, valore predefinito: "")
    -   Un pulsante con il testo "Cerca" 
    -   Una raccolta verticale chiamata (chiamata *galleryResults*)
    
    Il modulo dovrebbe essere simile al seguente:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layout dei controlli" border="true":::

1. Per fare in modo che il **pulsante Cerca** emetta una query, selezionare il pulsante e incollare l'azione seguente per eseguire **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Pulsante OnSelect" border="true":::
 
    Questa azione farà sì che il pulsante aggiorni una nuova raccolta denominata *azResult* con il risultato della query di ricerca, usando il testo nella casella di testo *txtQuery* come termine della query.
    
1.  Come passaggio successivo, collegheremo la galleria verticale creata alla raccolta *azResult.As* a next step, we will link the vertical gallery we created to the azResult collection. Selezionare il controllo raccolta ed eseguire le azioni seguenti nel riquadro delle proprietà.

    -  Impostare **DataSource** su *azResult*.
    
    -  Selezionare un **layout** adatto alle proprie attività in base al tipo di dati nell'indice. In questo caso, abbiamo usato il *layout del titolo,* del sottotitolo e del corpo.
    
    -  **Modificare i campi**e selezionare i campi che si desidera visualizzare.

    Poiché è stato fornito un risultato di esempio quando abbiamo definito il connettore, l'app è a conoscenza dei campi disponibili nell'indice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campi della raccolta" border="true":::   
 
1.  Premere **F5** per visualizzare l'anteprima dell'app.  

    Tenere presente che i campi possono essere impostati su valori calcolati.      
    Per l'esempio, l'impostazione utilizzando il layout *"Immagine, titolo e sottotitolo"* e specificando la funzione *Immagine* come `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`concatenazione del percorso radice per i dati e del nome del file (ad esempio, ) produrrà il risultato seguente.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="App finale" border="true":::        

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni e formazione online, vedere [Catalogo di formazione](https://docs.microsoft.com/powerapps/learning-catalog/get-started)di Power Apps .

