---
title: 'Ricerca personalizzata Bing: Creare una pagina Web di Ricerca personalizzata | Microsoft Docs'
description: Descrive come configurare un'istanza di Ricerca personalizzata e integrarla in una pagina Web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374756"
---
# <a name="build-a-custom-search-web-page"></a>Compilare una pagina Web di Ricerca personalizzata
Ricerca personalizzata Bing consente di creare esperienze di ricerca personalizzata per gli argomenti a cui si è interessati. Ad esempio, se si è proprietari di un sito Web di arti marziali che offre un'esperienza di ricerca, è possibile specificare i domini, i siti secondari e le pagine Web cercati da Bing. Gli utenti visualizzano risultati di ricerca specifici per il contenuto a cui sono interessati anziché scorrere risultati generali che possono includere contenuto irrilevante. 

Questa esercitazione dimostra come configurare un'istanza di Ricerca personalizzata e integrarla in una nuova pagina Web.

Sono descritte queste attività:

> [!div class="checklist"]
> - Creare un'istanza di Ricerca personalizzata
> - Aggiungere voci attive
> - Aggiungere voci bloccate
> - Aggiungere voci fissate in alto
> - Integrare la ricerca personalizzata in una pagina Web

## <a name="prerequisites"></a>prerequisiti
- Per proseguire con l'esercitazione è necessaria una chiave di sottoscrizione per l'API Ricerca personalizzata Bing.  Per ottenere una chiave, vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Creare un'istanza di Ricerca personalizzata
Per creare un'istanza di Ricerca personalizzata Bing:

1.  Aprire un browser Internet.
2.  Passare al [portale](https://customsearch.ai) di Ricerca personalizzata.
3.  Accedere al portale usando un account Microsoft (account del servizio gestito). Se non si ha un account Microsoft, fare clic su **Crea un account Microsoft**. Se è la prima volta che si usa il portale, verranno chieste le autorizzazioni necessarie per accedere ai dati. Fare clic su **Sì**.
4.  Dopo aver eseguito l'accesso, fare clic su **New custom search** (Nuova ricerca personalizzata). Nella finestra **Create a new custom search instance** (Crea una nuova istanza di Ricerca personalizzata) immettere un nome significativo che descriva il tipo di contenuto restituito dalla ricerca. È possibile modificare il nome in qualsiasi momento.
 
    ![Screenshot della finestra di creazione nuova istanza di Ricerca personalizzata](../media/newCustomSrch.png)

5.  Fare clic su OK e specificare un URL e se includere le pagine secondarie della pagina di base:

    ![Screenshot della pagina di definizione URL](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Aggiungere voci attive
Per includere i risultati da siti o URL specifici, aggiungerli alla scheda **Active** (Elementi attivi).

1.  Nell'**editor delle definizioni** fare clic sulla scheda **Active** (Elementi attivi) e immettere l'URL di uno o più siti da includere nella ricerca.

    ![Screenshot della scheda Active (Elementi attivi) nell'editor delle definizioni](../media/customSrchEditor.png)

2.  Per verificare che l'istanza restituisca risultati, immettere una query nel riquadro di anteprima a destra. Bing restituisce risultati solo per i siti pubblici che ha indicizzato.

## <a name="add-blocked-entries"></a>Aggiungere voci bloccate
Per escludere i risultati da siti o URL specifici, aggiungerli alla scheda **Blocked** (Elementi bloccati).

1. Nell'**editor delle definizioni** fare clic sulla scheda **Blocked** (Elementi bloccati) e immettere l'URL di uno o più siti da escludere dalla ricerca.

    ![Screenshot della scheda Blocked (Elementi bloccati) nell'editor delle definizioni](../media/blockedCustomSrch.png)


2. Per verificare che l'istanza non restituisca risultati dai siti bloccati, immettere una query nel riquadro di anteprima a destra. 

## <a name="add-pinned-entries"></a>Aggiungere voci fissate in alto
Per aggiungere una specifica pagina Web in cima ai risultati della ricerca, aggiungere la pagina Web e il termine di query alla scheda **Pinned** (Elementi aggiunti in alto).  La scheda **Pinned** (Elementi aggiunti in alto) contiene un elenco di coppie costituite da termine di query e pagina Web che specificano la pagina Web che viene visualizzata come primo risultato per una query specifica. Il termine di query dell'utente deve corrispondere esattamente al termine di query che indica la pagina Web da aggiungere in alto.

1. Nell'**editor delle definizioni**fare clic sulla scheda**Pinned** (Elementi aggiunti in alto) e immettere la pagina Web e il termine di query della pagina Web da restituire come primo risultato.

    ![Screenshot della scheda Pinned (Elementi aggiunti in alto) nell'editor delle definizioni](../media/pinnedCustomSrch.png)

2. Per verificare che l'istanza restituisca la pagina Web specificata come primo risultato, immettere il termine di query aggiunto nel riquadro di anteprima a destra.

## <a name="configure-hosted-ui"></a>Configurare l'interfaccia utente ospitata
Ricerca personalizzata fornisce un'interfaccia utente ospitata per eseguire il rendering della risposta JSON dell'istanza di Ricerca personalizzata.  Per definire l'esperienza dell'interfaccia utente:

1. Fare clic sulla scheda **Hosted UI** (Interfaccia utente ospitata).
2. Selezionare un layout.

    ![Screenshot del passaggio di selezione layout per l'interfaccia utente ospitata](./media/custom-search-hosted-ui-select-layout.png)

3. Selezionare un tema colori.

    ![Screenshot del passaggio di selezione layout per l'interfaccia utente ospitata](./media/custom-search-hosted-ui-select-color-theme.png)

4. Specificare opzioni di configurazione aggiuntive.

    ![Screenshot del passaggio di configurazioni aggiuntive dell'interfaccia utente ospitata](./media/custom-search-hosted-ui-additional-configurations.png)

5. Incollare la **chiave di sottoscrizione**. Vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Screenshot del passaggio di configurazioni aggiuntive dell'interfaccia utente ospitata](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Uso dell'interfaccia utente ospitata
L'interfaccia utente ospitata può essere usata in due modi.  

- Opzione 1: integrare il frammento di codice JavaScript fornito nell'applicazione.
- Opzione 2: usare l'endpoint HTML fornito.

La parte restante di questa esercitazione illustra l'**opzione 1: frammento di codice Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Configurare la soluzione di Visual Studio
1. Aprire **Visual Studio** nel computer.
2. Scegliere **Nuovo** dal menu **File** e quindi selezionare **Progetto**.
3. Nella finestra **Nuovo progetto** selezionare **Visual C# / Web / Applicazione Web ASP.NET Core**, assegnare un nome al progetto e quindi fare clic su **OK**.
   
    ![Screenshot della finestra Nuovo progetto](./media/custom-search-new-project.png)

4. Nella finestra **Nuova applicazione Web ASP.NET Core** selezionare **Applicazione Web** e fare clic su **OK**.
    
    ![Screenshot della finestra Nuovo progetto](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Modificare index.cshtml
1. In **Esplora soluzioni** espandere **Pagine** e fare doppio clic su **index.cshtml** per aprire il file.

    ![Screenshot di Esplora soluzioni con pagine espanse e index.cshtml selezionato](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. In index.cshtml eliminare tutto a partire dalla riga 7.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Aggiungere un elemento di interruzione di riga e un elemento div che funga da contenitore.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Dalla scheda **Hosted UI** (Interfaccia utente ospitata) scorrere fino alla sezione intitolata **Consuming the UI** (Uso interfaccia utente). Copiare il frammento di codice JavaScript.

    ![Screenshot del pulsante di salvataggio per l'interfaccia utente ospitata](./media/custom-search-hosted-ui-consuming-ui.png)

5. Incollare l'elemento script nel contenitore aggiunto.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **wwwroot** e scegliere **Visualizza nel browser**.

    ![Screenshot di Esplora soluzioni con selezione di Visualizza nel browser dal menu di scelta rapida wwwroot](./media/custom-search-webapp-view-in-browser.png)

La nuova pagina Web di Ricerca personalizzata dovrebbe essere simile alla seguente:

![Screenshot della pagina Web di Ricerca personalizzata](./media/custom-search-webapp-browse-index.png)

L'esecuzione di una ricerca produce risultati simili ai seguenti.

![Screenshot dei risultati della Ricerca personalizzata](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> - Creazione di un'istanza di Ricerca personalizzata
> - Aggiunta di voci attive
> - Aggiunta di voci bloccate
> - Aggiunta di voci fissate in alto
> - Integrazione della ricerca personalizzata in una pagina Web

Ora si può procedere all'argomento sulla chiamata degli endpoint di Ricerca personalizzata Bing a livello di codice.

> [!div class="nextstepaction"]
> [Chiamare l'endpoint di Ricerca personalizzata Bing (C#)](../call-endpoint-csharp.md)