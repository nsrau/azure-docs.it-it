---
title: 'Esercitazione: Eseguire query da Power Apps'
titleSuffix: Azure Cognitive Search
description: Istruzioni dettagliate su come creare un'app Power Apps che si connette a un indice di Ricerca cognitiva di Azure, invia query e visualizza i risultati.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: fd74bfca73323209012dfd1fda61bbaada84092f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530693"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Esercitazione: Eseguire query su un indice di Ricerca cognitiva da Power Apps

Sfruttare l'ambiente di sviluppo rapido di applicazioni di Power Apps per creare un'app personalizzata per il contenuto ricercabile in Ricerca cognitiva di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Connettersi a Ricerca cognitiva di Azure
> * Configurare una richiesta di query
> * Visualizzare i risultati in un'app canvas

Se non si ha una sottoscrizione di Azure, aprire un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* [Account di Power Apps](https://make.powerapps.com)

* [Indice Hotels-sample](search-get-started-portal.md)

* [Chiave API di query](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - Creare un connettore personalizzato

Un connettore in Power Apps è una connessione all'origine dati. In questo passaggio verrà creato un connettore personalizzato per connettersi a un indice di ricerca nel cloud.

1. [Accedere](https://make.powerapps.com) a Power Apps.

1. A sinistra espandere **Dati** > **Connettori personalizzati**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Selezionare **+ Nuovo connettore personalizzato** e quindi **Crea da zero**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Assegnare un nome al connettore personalizzato, ad esempio *AzureSearchQuery*, quindi fare clic su **Continua**.

1. Immettere le informazioni nella pagina Generale:

   * Colore di sfondo dell'icona (ad esempio #007ee5)
   * Descrizione (ad esempio "Connettore a Ricerca cognitiva di Azure")
   * In Host sarà necessario immettere l'URL del servizio di ricerca (ad esempio `<yourservicename>.search.windows.net`)
   * Per URL di base, immettere semplicemente "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Nella pagina Sicurezza impostare *Chiave API* per **Tipo di autenticazione**, quindi impostare sia l'etichetta che il nome del parametro su *api-key*. Per **Posizione parametro** selezionare *Intestazione* come illustrato di seguito.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Nella pagina Definizioni selezionare **+ Nuova azione** per creare un'azione che eseguirà una query sull'indice. Immettere il valore "Query" per il riepilogo e per il nome dell'ID operazione. Immettere una descrizione, ad esempio *"query sull'indice di ricerca"* .

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Scorrere verso il basso. In Richieste selezionare il pulsante **+ Importa da esempio** per configurare una richiesta di query per il servizio di ricerca:

   * Selezionare il verbo `GET`

   * Per URL immettere una query di esempio per l'indice di ricerca (`search=*` restituisce tutti i documenti, `$select=` consente di scegliere i campi). Il campo Versione API è obbligatorio. Un URL completamente specificato sarà simile al seguente: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Per Intestazioni, digitare `Content-Type`. 

     **Power Apps** userà la sintassi per estrarre i parametri dalla query. Si noti che il campo di ricerca è stato definito in modo esplicito. 

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Fare clic su **Importa** per compilare automaticamente la richiesta. Completare l'impostazione dei metadati dei parametri facendo clic sul simbolo **...** accanto a ognuno dei parametri. Fare clic su **Indietro** per tornare nella pagina Richiesta dopo l'aggiornamento di ogni parametro.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Per *search*: impostare `*` come **valore predefinito**, impostare **Obbligatorio?** su *no* e impostare **Visibilità** su *nessuna*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Per *select*: impostare `HotelName,Description,Address/City` come **valore predefinito**, impostare **Obbligatorio?** su *no* e impostare **Visibilità** su *nessuna*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Per *api-version*: impostare `2020-06-30` come **valore predefinito**, impostare **Obbligatorio?** su *sì* e impostare **Visibilità** su *interna*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Per *Content-Type*: Impostare su `application/json`.

1. Dopo aver apportato queste modifiche, passare alla visualizzazione **Editor Swagger**. Nella sezione parameters dovrebbe essere visualizzata la configurazione seguente:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Tornare al passaggio **3. Richiesta** e scorrere verso il basso fino alla sezione Risposta. Fare clic su **"Aggiungi risposta predefinita"** . Questa scelta è fondamentale perché consente a Power Apps di riconoscere lo schema della risposta. 

1. Incollare una risposta di esempio. Per acquisire facilmente una risposta di esempio, è possibile usare Esplora ricerche nel portale di Azure. In Esplora ricerche è necessario immettere la stessa query specificata per la richiesta, ma aggiungendo **$top = 2** per vincolare i risultati a due soli documenti: `search=*&$select=HotelName,Description,Address/City&$top=2`. 

   Power Apps richiede solo pochi risultati per rilevare lo schema.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Esiste un limite di caratteri per la risposta JSON che è possibile immettere, quindi è consigliabile semplificare il codice JSON prima di incollarlo. Lo schema e il formato della risposta sono più importanti dei valori stessi. Il campo Descrizione, ad esempio, può essere semplificato lasciando solo per la prima frase.

1. Fare clic su **Crea connettore** in alto a destra.

## <a name="2---test-the-connection"></a>2 - Testare la connessione

La prima volta che viene creato il connettore, è necessario riaprirlo dall'elenco di connettori personalizzati per poterlo testare. In seguito, se si apportano altri aggiornamenti, è possibile testarlo dalla procedura guidata.

Per questa attività sarà necessaria una [chiave API di query](search-security-api-keys.md#find-existing-keys). Ogni volta che viene creata una connessione, sia per un'esecuzione di test o per l'inclusione in un'app, il connettore richiede la chiave API di query usata per la connessione a Ricerca cognitiva di Azure.

1. All'estrema sinistra fare clic su **Connettori personalizzati**.

1. Cercare il connettore in base al nome, che in questa esercitazione è "AzureSearchQuery".

1. Selezionare il connettore, espandere l'elenco di azioni e selezionare **Visualizza proprietà**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Selezionare **Modifica** in alto a destra.

1. Selezionare **4. Test** per aprire la pagina di test.

1. In Verifica operazione fare clic su **+ Nuova connessione**.

1. Immettere una chiave API di query. Si tratta di una query di Ricerca cognitiva di Azure per l'accesso in sola lettura a un indice. La [chiave è reperibile](search-security-api-keys.md#find-existing-keys) nel portale di Azure. 

1. In Operazioni fare clic sul pulsante **Verifica operazione**. Se l'operazione riesce, verrà visualizzato lo stato 200 e nel corpo della risposta verrà visualizzato il codice JSON che descrive i risultati della ricerca.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="Menu Connettori personalizzati" border="true":::

## <a name="3---visualize-results"></a>3 - Visualizzare i risultati

In questo passaggio creare un'app Power Apps con una casella di ricerca, un pulsante di ricerca e un'area per la visualizzazione dei risultati. Power Apps si connetterà al connettore personalizzato creato di recente per ottenere i dati da Ricerca di Azure.

1. A sinistra espandere **App** >  **+ Nuova app** > **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Selezionare il tipo di applicazione. Per questa esercitazione, scegliere **App vuota** con **Layout telefono**. Verrà visualizzato **Power Apps Studio**.

1. In Studio selezionare la scheda **Origini dati** e fare clic sul nuovo connettore appena creato. In questo caso, si chiama *AzureSearchQuery*. Fare clic su **Aggiungi connessione**.

   Immettere la chiave API di query.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="Menu Connettori personalizzati" border="true":::

    A questo punto *AzureSearchQuery* è un'origine dati disponibile per l'applicazione.

1. Nella scheda **Inserisci** aggiungere alcuni controlli al canvas.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Inserire gli elementi seguenti:

   * Un'etichetta di testo con il valore "Query":
   * Un elemento input di testo (assegnare il nome *txtQuery*, valore predefinito: "*")
   * Un pulsante con il testo "Search" 
   * Una raccolta verticale denominata (assegnare il nome *galleryResults*)

    Il canvas sarà simile al seguente:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Menu Connettori personalizzati" border="true":::

1. Per fare in modo che il **pulsante di ricerca** esegua una query, incollare l'azione seguente in **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   Lo screenshot seguente mostra la barra della formula per l'azione **OnSelect**.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Menu Connettori personalizzati" border="true":::

   Con questa azione, il pulsante aggiornerà una nuova raccolta denominata *azResult* con il risultato della query di ricerca, usando il testo della casella di testo *txtQuery* come termine della query.

   > [!NOTE]
   > Verificare se si ottiene un errore di sintassi della formula, con un messaggio analogo a "La funzione 'ClearCollect' include alcune funzioni non valide":
   > 
   > * Assicurarsi prima di tutto che il riferimento al connettore sia corretto. Cancellare il nome del connettore e iniziare a digitarne il nome. IntelliSense dovrebbe suggerire il connettore e il verbo corretti.
   > 
   > * Se l'errore persiste, eliminare il connettore e ricrearlo. Se sono presenti più istanze di un connettore, è possibile che l'app usi quella sbagliata.
   > 

1. Collegare il controllo raccolta verticale alla raccolta *azResult* creata nel passaggio precedente. 

   Selezionare il controllo raccolta ed eseguire le azioni seguenti nel riquadro delle proprietà.

   * Impostare **DataSource** su *azResult*.
   * Selezionare un **Layout** appropriato in base al tipo di dati dell'indice. In questo caso, è stato usato il layout *Titolo, sottotitolo e corpo*.
   * Scegliere **Modifica campi** e selezionare i campi da visualizzare.

    Poiché quando è stato definito il connettore è stato fornito un risultato di esempio, l'app è in grado di riconoscere i campi disponibili nell'indice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Menu Connettori personalizzati" border="true":::   
 
1. Premere **F5** per visualizzare l'anteprima dell'app.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Menu Connettori personalizzati" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Menu Connettori personalizzati" border="true":::         -->

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Power Apps consente lo sviluppo rapido di app personalizzate. Ora che si è appreso come connettersi a un indice di ricerca, vedere altre informazioni su come creare un'esperienza di visualizzazione arricchita in un'app personalizzata di Power Apps.

> [!div class="nextstepaction"]
> [Catalogo di risorse di apprendimento per Power Apps](/powerapps/learning-catalog/get-started)