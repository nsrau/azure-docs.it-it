---
title: 'Ricerca personalizzata Bing: ricerca siti | Microsoft Docs'
description: Descrive come configurare l'interfaccia utente ospitata
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374732"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurare l'esperienza dell'interfaccia utente ospitata
Dopo aver configurato l'istanza di ricerca personalizzata, è possibile chiamare l'API Ricerca personalizzata per ottenere i risultati della ricerca e visualizzarli nell'app. In alternativa, se l'app è un'app Web, è possibile usare l'interfaccia utente ospitata fornita da Ricerca personalizzata.   

## <a name="configure-custom-hosted-ui"></a>Configurare l'interfaccia utente ospitata personalizzata
Usare le istruzioni seguenti per configurare un'interfaccia utente ospitata da includere nell'app Web.
1.  Accedere al portale di [Ricerca personalizzata](https://customsearch.ai).
2.  Fare clic su un'istanza di Ricerca personalizzata. Per creare un'istanza, vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
3.  Fare clic sulla scheda **Hosted UI** (Interfaccia utente ospitata).
4.  Selezionare un layout.
    - Barra di ricerca e risultati (impostazione predefinita) &mdash; Visualizza una casella di ricerca e i risultati della ricerca
    - Solo risultati &mdash; Non visualizza una casella di ricerca e mostra solo i risultati
    - Finestra a comparsa &mdash; Non visualizza una casella di ricerca e mostra solo i risultati in una finestra sovrapposta scorrevole
    
   > [!IMPORTANT]
   > Assicurarsi di includere il parametro di query customConfig quando si seleziona il layout **Solo risultati**. Vedere i [parametri di query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  In **Additional Configurations** (Configurazioni aggiuntive) fornire i valori appropriati per l'app. Questa impostazioni sono facoltative. Per visualizzare l'effetto prodotto dalla selezione o deselezione di queste impostazioni, vedere il riquadro di anteprima a destra.  Le opzioni di configurazione disponibili sono:
    - Web search configurations (Configurazioni di ricerca Web):
        - Web results enabled (Risultati Web abilitati) &mdash; Determina se vengono restituiti i risultati della ricerca Web
        - Enable autosuggest (Abilita suggerimenti automatici) &mdash; Determina se sono abilitati i suggerimenti automatici personalizzati
        - Web results per page (Risultati Web per pagina) &mdash; Numero di risultati della ricerca Web da visualizzare alla volta
        - Image caption (Didascalia immagine) &mdash; Determina se vengono visualizzate le immagini con i risultati della ricerca
        - Highlight words (Evidenzia parole) &mdash; Determina se i risultati vengono visualizzati con i termini di ricerca in grassetto
    - Image search configurations (Configurazioni di ricerca immagini):
        - Image results enabled (Risultati immagini abilitati) &mdash; Determina se vengono restituiti i risultati della ricerca immagini
    - Miscellaneous configurations (Configurazioni varie):
        - Page title (Titolo pagina) &mdash; Testo visualizzato nell'area del titolo pagina
        - Toolbar theme (Tema della barra degli strumenti) &mdash; Determina il colore di sfondo dell'area del titolo pagina
        - Search box text placeholder (Segnaposto del testo della casella di ricerca) &mdash; Testo visualizzato nella casella di ricerca prima dell'input
        - Title link url (URL collegamento titolo) &mdash; Destinazione per il collegamento del titolo
        - Logo url (URL del logo) &mdash; Immagine visualizzata accanto al titolo 
        - Favicon url (URL icona preferita) &mdash; Icona visualizzata nella barra del titolo del browser

   > [!IMPORTANT]
   > È necessario che sia abilitata la ricerca immagini o la ricerca Web.

6.  Immettere la chiave di sottoscrizione della ricerca o sceglierne una dall'elenco a discesa. L'elenco a discesa viene popolato con le chiavi presenti nelle sottoscrizioni di Azure dell'account usato. Vedere [Account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Se sono abilitati i suggerimenti automatici, immettere la chiave di sottoscrizione dei suggerimenti automatici o sceglierne uno dall'elenco a discesa. L'elenco a discesa viene popolato con le chiavi presenti nelle sottoscrizioni di Azure dell'account usato. I suggerimenti automatici personalizzati richiedono un livello di sottoscrizione specifico. Vedere le [pagine relative ai prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Quando si apportano modifiche alla configurazione dell'interfaccia utente ospitata personalizzata, il riquadro a destra fornisce un riferimento visivo per le modifiche apportate. I risultati della ricerca visualizzati non sono risultati effettivi per l'istanza

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Utilizzare l'interfaccia utente personalizzata
Per utilizzare l'interfaccia utente ospitata, eseguire una delle due operazioni seguenti: 

- Includere lo script nella pagina Web
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- Usare l'URL fornito `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > La pagina non può visualizzare l'informativa sulla privacy o altri avvisi e condizioni. L'idoneità all'uso può variare.  

Per altre informazioni, tra cui l'ID di configurazione personalizzato, vedere **Endpoint** (Endpoint) nella scheda **Production** (Produzione).

## <a name="next-steps"></a>Passaggi successivi
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)