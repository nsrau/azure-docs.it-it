---
title: Ricerca del sito, usare Ricerca personalizzata Bing dell'interfaccia utente in hosting
titlesuffix: Azure Cognitive Services
description: Descrive come configurare l'interfaccia utente in hosting di Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: c71597cf540cca67b9558ce28d20ce1d21ae0243
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424987"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurare l'esperienza dell'interfaccia utente ospitata

Dopo aver configurato l'istanza di ricerca personalizzata, è possibile chiamare l'API Ricerca personalizzata per ottenere i risultati della ricerca e visualizzarli nell'app. In alternativa, se l'app è un'app Web, è possibile usare l'interfaccia utente ospitata fornita da Ricerca personalizzata.   

## <a name="configure-custom-hosted-ui"></a>Configurare l'interfaccia utente ospitata personalizzata

Per configurare un'interfaccia utente ospitata per l'app Web, completare questi passaggi:

1. Accedere al portale di [Ricerca personalizzata](https://customsearch.ai).  
  
2. Fare clic su un'istanza di Ricerca personalizzata. Per creare un'istanza, vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).  

3. Fare clic sulla scheda **Hosted UI** (Interfaccia utente ospitata).  
  
4. Selezionare un layout.
  
  - Search bar and results (default) (Barra di ricerca e risultati - Impostazione predefinita) &mdash; Questo layout è la pagina di ricerca tradizionale con la casella di ricerca e i risultati della ricerca.
  - Results only (Solo risultati) &mdash; Questo layout visualizza solo i risultati della ricerca. Non viene visualizzata una casella di ricerca. È necessario fornire la query di ricerca aggiungendo il parametro di query (&q=\<stringa di query>) all'URL della richiesta nel frammento di codice JavaScript o nel collegamento all'endpoint HTML.
  - Pop-over (Risultati sovrapposti) &mdash; Questo layout fornisce una casella di ricerca e visualizza i risultati della ricerca in una sovrapposizione scorrevole.
      
5. Selezionare un tema colori. I temi possibili sono i seguenti: 
  
  - Classico
  - Dark (Scuro)
  - Skyline Blue (Blu orizzonte)

  Fare clic su ognuno dei temi per scegliere quello più appropriato per l'app Web. Se è necessario ottimizzare la combinazione di colori per una migliore integrazione con l'app Web, fare clic su **Customize theme** (Personalizza tema). Non tutte le configurazioni di colori si applicano a tutti i temi del layout. Per modificare un colore, immettere il valore RGB esadecimale del colore (ad esempio, #366eb8) nella casella di testo corrispondente. In alternativa, fare clic sul pulsante del colore e quindi sulla sfumatura appropriata. 
  
  Dopo aver modificato un colore, verificare l'impatto della modifica sull'esempio di anteprima a destra. È sempre possibile fare clic su **Ripristina impostazioni predefinite** per tornare ai colori predefiniti per il tema selezionato.

  > [!NOTE]
  > Quando si modifica il tema colori, tenere conto dell'accessibilità nella scelta dei colori.

5. In **Additional Configurations** (Configurazioni aggiuntive) fornire i valori appropriati per l'app. Questa impostazioni sono facoltative. Per visualizzare l'effetto prodotto dalla selezione o deselezione di queste impostazioni, vedere il riquadro di anteprima a destra. Le opzioni di configurazione disponibili sono:  
  
  - Web search configurations (Configurazioni di ricerca Web):
    - Web results enabled (Risultati Web abilitati) &mdash; Determina se la ricerca Web è abilitata (verrà visualizzata una scheda Web nella parte superiore della pagina).
    - Enable autosuggest (Abilita suggerimenti automatici) &mdash; Determina se sono abilitati i suggerimenti automatici personalizzati (vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) per informazioni sugli eventuali costi aggiuntivi).
    - Web results per page (Risultati Web per pagina) &mdash; Numero di risultati della ricerca Web da visualizzare per volta (il numero massimo è 50 risultati per pagina).
    - Image caption (Didascalia immagine) &mdash; Determina se visualizzare le immagini con i risultati della ricerca.
  
    Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), vengono visualizzate le configurazioni seguenti.  
  
    - Highlight words (Evidenzia parole) &mdash; Determina se i risultati vengono visualizzati con i termini di ricerca in grassetto. 
    - Link target (Destinazione collegamento) &mdash; Determina se la pagina Web viene aperta in una nuova scheda del browser (vuota) o nella stessa scheda (autonoma) del browser quando l'utente fa clic su un risultato della ricerca. 

  - Image search configurations (Configurazioni di ricerca immagini):
    - Web results enabled (Risultati Web abilitati) &mdash; Determina se la ricerca di immagini è abilitata (verrà visualizzata una scheda Immagini nella parte superiore della pagina).   
    - Image results per page (Risultati immagine per pagina) &mdash; Numero di risultati della ricerca Web da visualizzare per volta (il numero massimo è 150 risultati per pagina).  
  
    Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), viene visualizzata la configurazione seguente.  
  
    - Enable filters (Abilita filtri) &mdash; Aggiunge filtri che l'utente può usare per filtrare le immagini restituite da Bing. Ad esempio, l'utente può filtrare i risultati per ottenere solo immagini GIF animate.

  - Configurazioni per la ricerca video:
    - Web results enabled (Risultati Web abilitati) &mdash; Determina se la ricerca Web è abilitata (verrà visualizzata una scheda Video nella parte superiore della pagina).  
    - Image results per page (Risultati immagine per pagina) &mdash; Numero di risultati della ricerca video da visualizzare per volta (il numero massimo è 150 risultati per pagina).
  
    Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), viene visualizzata la configurazione seguente.  
  
    - Enable filters (Abilita filtri) &mdash; Aggiunge filtri che l'utente può usare per filtrare i video restituiti da Bing. Ad esempio, l'utente può filtrare i risultati per i video con una risoluzione specifica o i video individuati nelle ultime 24 ore.

  - Miscellaneous configurations (Configurazioni varie):
    - Page title (Titolo della pagina) &mdash; Testo visualizzato nell'area del titolo della pagina dei risultati della ricerca (non per il layout con risultati sovrapposti).
    - Toolbar theme (Tema della barra degli strumenti) &mdash; Determina il colore di sfondo dell'area del titolo della pagina dei risultati della ricerca.  
  
    Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), vengono visualizzate le configurazioni seguenti.  
  
    - Search box text placeholder (Segnaposto del testo della casella di ricerca) &mdash; Testo visualizzato nella casella di ricerca prima dell'input.
    - Title link url (URL collegamento titolo) &mdash; Destinazione per il collegamento del titolo.
    - Logo url (URL del logo) &mdash; Immagine visualizzata accanto al titolo. 
    - Favicon url (URL icona preferita) &mdash; Icona visualizzata nella barra del titolo del browser.  

    Le configurazioni seguenti si applicano solo se si usa l'interfaccia utente ospitata tramite l'endpoint HTML (non sono applicabili se si usa il frammento JavaScript).
    
    - Page title (Titolo della pagina)
    - Toolbar theme (Tema della barra degli strumenti)
    - Title link URL (URL collegamento titolo)
    - Logo URL (URL del logo)
    - URL Favicon (URL icona preferita)  
  
6. Immettere la chiave di sottoscrizione della ricerca o sceglierne una nell'elenco a discesa. L'elenco a discesa viene popolato con le chiavi presenti nelle sottoscrizioni degli account Azure. Vedere [Account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Se sono stati abilitati i suggerimenti automatici, immettere la chiave di sottoscrizione dei suggerimenti automatici oppure sceglierne una nell'elenco a discesa. L'elenco a discesa viene popolato con le chiavi presenti nelle sottoscrizioni degli account Azure. I suggerimenti automatici personalizzati richiedono un livello di sottoscrizione specifico. Vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Quando si apportano modifiche alla configurazione dell'interfaccia utente ospitata personalizzata, il riquadro a destra fornisce un riferimento visivo per le modifiche apportate. I risultati della ricerca visualizzati non sono effettivi risultati per l'istanza.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Utilizzare l'interfaccia utente personalizzata

Per utilizzare l'interfaccia utente ospitata, eseguire una delle due operazioni seguenti: 

- Includere lo script nella pagina Web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- In alternativa, usare l'URL seguente in un Web browser.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Aggiungere i parametri di query seguenti all'URL nel modo necessario. Per altre informazioni su questi parametri, vedere [API Ricerca personalizzata Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > La pagina non può visualizzare l'informativa sulla privacy o altri avvisi e condizioni. L'idoneità all'uso può variare.  

Per altre informazioni, tra cui l'ID di configurazione personalizzato, vedere **Endpoint** (Endpoint) nella scheda **Production** (Produzione).

## <a name="next-steps"></a>Passaggi successivi

- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
