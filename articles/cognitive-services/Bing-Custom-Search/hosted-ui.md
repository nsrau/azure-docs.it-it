---
title: Configurare un'interfaccia utente ospitata per Ricerca personalizzata Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare questo articolo per configurare e integrare un'interfaccia utente ospitata per Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: e30c36cbde3bf112b012526e6268dfc4414f64a9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555473"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurare l'esperienza dell'interfaccia utente ospitata

Ricerca personalizzata Bing offre un'interfaccia utente ospitata che è possibile integrare facilmente in applicazioni Web e pagine Web come frammento di codice JavaScript. Tramite il portale di Ricerca personalizzata Bing è possibile configurare layout, colore e opzioni di ricerca dell'interfaccia utente.



## <a name="configure-the-custom-hosted-ui"></a>Configurare l'interfaccia utente ospitata personalizzata

Per configurare un'interfaccia utente ospitata per le applicazioni Web, completare questi passaggi. Quando si apportano modifiche, nel riquadro a destra viene visualizzata un'anteprima dell'interfaccia utente. I risultati della ricerca visualizzati non sono effettivi risultati per l'istanza.

1. Accedere al [portale](https://customsearch.ai) di Ricerca personalizzata Bing.  
  
2. Selezionare l'istanza di Ricerca personalizzata Bing.

3. Fare clic sulla scheda **Hosted UI** (Interfaccia utente ospitata).  
  
4. Selezionare un layout.

    |  |  |
    |---------|---------|
    |Barra di ricerca e risultati (impostazione predefinita)    | Visualizza la casella di ricerca con i risultati della ricerca sotto essa.         |
    |Solo risultati     | Visualizza solo i risultati della ricerca senza la casella di ricerca. Quando si usa questo layout è necessario fornire la query di ricerca (`&q=<query string>`). Aggiungere il parametro di query all'URL della richiesta nel frammento di codice JavaScript oppure il collegamento all'endpoint HTML.        |
    |Finestra a comparsa     | Fornisce una casella di ricerca e visualizza i risultati della ricerca in una sovrapposizione scorrevole.        |
    
5. Selezionare un tema colori. È possibile personalizzare i colori per adattare l'applicazione facendo clic su **Customize theme** (Personalizza tema). Per modificare un colore, immettere il valore RGB esadecimale del colore, ad esempio `#366eb8`, o fare clic sull'anteprima colore.

  È possibile visualizzare in anteprima le modifiche sul lato destro del portale. Facendo clic su **Ripristina impostazioni predefinite** si ripristinano i colori predefiniti per il tema selezionato.

  > [!NOTE]
  > Nella scelta dei colori è necessario considerare l'accessibilità.

6. In **Additional Configurations** (Configurazioni aggiuntive) fornire i valori appropriati per l'app. Questa impostazioni sono facoltative. Per visualizzare l'effetto prodotto dalla selezione o deselezione di queste impostazioni, vedere il riquadro di anteprima a destra. Le opzioni di configurazione disponibili sono:  

7. Immettere la chiave di sottoscrizione della ricerca o sceglierne una nell'elenco a discesa. L'elenco a discesa viene popolato con le chiavi presenti nelle sottoscrizioni degli account Azure. Vedere [Account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Se sono stati abilitati i suggerimenti automatici, immettere la chiave di sottoscrizione dei suggerimenti automatici oppure sceglierne una nell'elenco a discesa. L'elenco a discesa viene popolato con le chiavi presenti nelle sottoscrizioni degli account Azure. I suggerimenti automatici personalizzati richiedono un livello di sottoscrizione specifico. Vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

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

## <a name="configuration-options"></a>Opzioni di configurazione

È possibile configurare il comportamento dell'interfaccia utente ospitata facendo clic su **Configurazioni aggiuntive** e fornendo i valori. Questa impostazioni sono facoltative. Per visualizzare l'effetto prodotto dalla selezione o deselezione di queste impostazioni, vedere il riquadro di anteprima a destra. 

### <a name="web-search-configurations"></a>Web search configurations (Configurazioni di ricerca Web)

|  |  |
|---------|---------|
|Web results enabled (Risultati Web abilitati)    | Determina se la ricerca Web è abilitata: sarà visualizzata una scheda Web nella parte superiore della pagina        |
|Enable autosuggest (Abilita suggerimenti automatici)     | Determina se sono abilitati i suggerimenti automatici personalizzati; per informazioni sugli eventuali costi aggiuntivi, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).        |
|Web results per page (Risultati Web per pagina)    | Numero di risultati della ricerca Web da visualizzare per volta: il numero massimo è 50 risultati per pagina.        |
|Image caption (Didascalia immagine)   | Determina se visualizzare le immagini con i risultati della ricerca.|


Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), vengono visualizzate le configurazioni seguenti:


|  | |
|---------|---------|
|Highlight words (Evidenzia parole)     | Determina se i risultati vengono visualizzati con i termini di ricerca in grassetto.         |
|Link target (Destinazione collegamento)    |  Determina se la pagina Web viene aperta in una nuova scheda del browser (vuota) o nella stessa scheda (autonoma) del browser quando l'utente fa clic su un risultato della ricerca.        |

### <a name="image-search-configurations"></a>Image search configurations (Configurazioni di ricerca immagini)

| | |
|---------|---------|
|Image results enabled (Immagini abilitate nei risultati)     | Determina se la ricerca di immagini è abilitata: verrà visualizzata una scheda Immagini nella parte superiore della pagina.            |
|Image results per page (Immagini nei risultati per pagina)     | Numero di immagini nei risultati della ricerca da visualizzare per volta; il numero massimo è 150 risultati per pagina.          |

Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), viene visualizzata la configurazione seguente.  
  
| | |
|---------|---------|
| Enable filters (Abilita filtri)     | Aggiunge filtri che l'utente può usare per filtrare le immagini restituite da Bing. Ad esempio, l'utente può filtrare i risultati per ottenere solo immagini GIF animate.|

### <a name="video-search-configurations"></a>Configurazioni per la ricerca di video

|  | |
|---------|---------|
|Video results enabled (Video abilitati nei risultati)     | Determina se la ricerca di video è abilitata: verrà visualizzata una scheda Video nella parte superiore della pagina.           |
|Video results per page (Video nei risultati per pagina)   | Numero di video nei risultati della ricerca da visualizzare per volta; il numero massimo è 150 risultati per pagina.        |

Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), viene visualizzata la configurazione seguente.  
  
|  | |
|---------|---------|
|Enable filters (Abilita filtri)    | Aggiunge filtri che l'utente può usare per filtrare i video restituiti da Bing. Ad esempio, l'utente può filtrare i risultati per i video con una risoluzione specifica o i video individuati nelle ultime 24 ore.          |

### <a name="miscellaneous-configurations"></a>Miscellaneous configurations (Configurazioni varie)


| |  |
|---------|---------|
|Page title (Titolo della pagina)   | Testo visualizzato nell'area del titolo della pagina dei risultati della ricerca; non disponibile per il layout con risultati sovrapposti.        |
|Toolbar theme (Tema della barra degli strumenti)    | Determina il colore di sfondo dell'area del titolo della pagina dei risultati della ricerca. |

Se si fa clic su **Show advanced configurations** (Mostra configurazioni avanzate), vengono visualizzate le configurazioni seguenti.  

|Colonna1  |Colonna2  |
|---------|---------|
|Search box text placeholder (Segnaposto del testo della casella di ricerca)   | Testo visualizzato nella casella di ricerca prima dell'input.        |
|Title link URL (URL collegamento titolo)    |Destinazione per il collegamento del titolo.         |
|Logo URL (URL del logo)     | Immagine visualizzata accanto al titolo.         |
|Favicon (Icona preferita)    | Icona visualizzata nella barra del titolo del browser.          |

Le configurazioni seguenti si applicano solo se si usa l'interfaccia utente ospitata tramite l'endpoint HTML (non sono applicabili se si usa il frammento JavaScript).

- Page title (Titolo della pagina)
- Toolbar theme (Tema della barra degli strumenti)
- Title link URL (URL collegamento titolo)
- Logo URL (URL del logo)
- URL Favicon (URL icona preferita)  

## <a name="next-steps"></a>Passaggi successivi

- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
