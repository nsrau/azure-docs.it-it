---
title: Configurare l'esperienza di API Ricerca personalizzata Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Descrive come creare un sito e servizi di ricerca verticale
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: e4799ca099d608c3b8ecd16612b790f5654df7dd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390426"
---
# <a name="configure-your-bing-custom-search-experience"></a>Configurare un'esperienza API Ricerca personalizzata Bing

Un'istanza di Ricerca personalizzata consente di personalizzare l'esperienza di ricerca in modo da includere il contenuto solo dai siti Web a cui sono interessati gli utenti. Anziché eseguire una ricerca in tutto il Web, Bing esegue la ricerca solo nelle sezioni Web desiderate. Per creare una visualizzazione personalizzata del Web, usare il [portale](https://customsearch.ai) di Ricerca personalizzata Bing.

Il portale consente di creare un'istanza di ricerca che specifica le sezioni Web: domini, pagine secondarie e pagine Web in cui si vuole eseguire la ricerca con Bing e le sezioni in cui non si vuole eseguire la ricerca. Il portale è inoltre in grado di suggerire il contenuto che l'utente potrebbe volere includere.

Quando si definiscono le sezioni Web, usare il comando seguente:

| Nome della sezione | Descrizione                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | La sezione di un dominio include tutto il contenuto presente all'interno di un dominio Internet. Ad esempio: `www.microsoft.com`. Se si omette `www.`, Bing eseguirà la ricerca anche all'interno dei sottodomini del dominio. Ad esempio, se si specifica `microsoft.com`, Bing restituisce anche i risultati dal `support.microsoft.com` o `technet.microsoft.com`. |
| Pagina secondaria    | La sezione di una pagina secondaria include tutto il contenuto disponibile nella pagina secondaria e i relativi percorsi. È possibile specificare un massimo di due pagine secondarie nel percorso. Ad esempio: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Pagina Web    | La sezione di una pagina Web può includere solo la pagina Web in una ricerca personalizzata. Facoltativamente, è possibile specificare se includere pagine secondarie.                                                                                                                                                                                  |

> [!IMPORTANT]
> Tutti i domini, le pagine secondarie e le pagine Web specificati devono essere pubblici e indicizzati da Bing. Se si è proprietari di un sito pubblico che si vuole includere nella ricerca e non è stato indicizzato da Bing, vedere la [documentazione per webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bing per informazioni dettagliate sull'indicizzazione di Bing. Inoltre, la documentazione per webmaster contiene informazioni dettagliate su come aggiornare un sito sottoposto a ricerca per indicizzazione, se l'indice non è aggiornato.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Aggiungere le sezioni del Web all'istanza di ricerca personalizzata

Quando si crea l'istanza di ricerca personalizzata, è possibile specificare le sezioni del Web che si desidera includere o bloccare dai risultati della ricerca: domini, pagine secondarie e pagine Web. 

Se si conoscono le sezioni da includere nell'istanza, aggiungerle all'elenco **Active** (Elementi attivi) dell'istanza. 

Se non si è certi di quali sezioni includere, è possibile inviare le query di ricerca di Bing nel riquadro **Anteprima** e selezionare gli intervalli desiderati. A tale scopo, effettuare l'operazione seguente: 

1. selezionare "Bing" nell'elenco a discesa nel riquadro di anteprima e inserire una query di ricerca

2. Fare clic su **Add site** (Aggiungi sito) accanto al risultato da includere. Fare quindi clic su OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Personalizzare l'esperienza di ricerca con gli elenchi Active (Elementi attivi) e Blocked (Elementi bloccati) 

È possibile accedere all'elenco delle sezioni attive e bloccate facendo clic sulle schede **Active** (Attivo) e **Blocked** (Bloccato) nell'istanza di ricerca personalizzata. Le sezioni aggiunte all'elenco active (elementi attivi) verranno incluse nella ricerca personalizzata. Non verrà effettuata la ricerca all'interno delle sezioni bloccate e non saranno visualizzate nei risultati della ricerca.

Per specificare le sezioni del Web in cui si desidera effettuare la ricerca con Bing, fare clic sulla scheda **Active** (Attivo) e aggiungere uno o più URL. Per modificare o eliminare URL, usare le opzioni nella colonna **Controls** (Controlli). 

Quando si aggiungono URL all'elenco **Active**(Elementi attivi), è possibile aggiungere singoli URL o più URL in una sola volta, caricando un file di testo usando l'icona di caricamento.

![Informazioni sulla scheda Active (Attivo) di Ricerca personalizzata Bing](media/file-upload-icon.png)

Per caricare un file, creare un file di testo e specificare un solo dominio, una sola pagina secondaria o una sola pagina Web per ogni riga. Il file verrà rifiutato se non è formattato correttamente.

> [!NOTE]
> * È possibile caricare solo un file nell'elenco **Active** (Elementi attivi). Non è possibile usarlo per aggiungere sezioni all'elenco **Blocked** (Elementi bloccati).  
> * Se l'elenco **Blocked** (Elementi bloccati) contiene un dominio, una pagina secondaria o una pagina Web specificata nel file di caricamento, il servizio lo rimuove dall'elenco **Blocked** (Elementi bloccati) e lo aggiunge all'elenco **Active** (Elementi attivi).
> * Le voci duplicate nel file di caricamento verranno ignorate da Ricerca personalizzata Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Ottieni suggerimenti di siti Web per l'esperienza di ricerca

Dopo l'aggiunta di sezioni Web agli elenchi **Active** (Elementi attivi), il portale di API Ricerca personalizzata Bing genererà suggerimenti relativi a pagine secondarie e siti Web nella parte inferiore della scheda. Si tratta di sezioni che Ricerca personalizzata Bing ritiene che potrebbe essere opportuno includere. Fare clic su **Aggiorna** per ottenere suggerimenti dopo l'aggiornamento delle impostazioni dell'istanza di ricerca personalizzata. Questa sezione è visibile solo se i suggerimenti sono disponibili.

## <a name="search-for-images-and-videos"></a>Eseguire la ricerca di immagini e video

È possibile cercare immagini e video in modo analogo al contenuto Web usando l'[API Ricerca immagini personalizzata Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) o l'[API Ricerca video personalizzata Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). È possibile visualizzare questi risultati con [Interfaccia utente ospitata](hosted-ui.md), o con le API. 

Queste API sono simili alle API non-personalizzate [Ricerca immagini Bing](../Bing-Image-Search/overview.md) e [Ricerca video Bing](../Bing-Video-Search/search-the-web.md), ma effettuano la ricerca nell'intero Web e non richiedono il parametro di query `customConfig`. Consultare questi set di documentazione per altre informazioni sull'uso di immagini e video. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testare l'istanza di ricerca del riquadro di anteprima

È possibile testare l'istanza di ricerca usando il riquadro di anteprima sul lato destro del portale per inviare le query di ricerca e visualizzare i risultati. 

1. Sotto la casella di ricerca, selezionare **My Instance**(Istanza personale). È possibile confrontare i risultati della propria esperienza di ricerca con Bing, selezionando **Bing**. 
2. Selezionare un filtro di ricerca sicura e il mercato in cui eseguire la ricerca. Vedere [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) (Parametri di query).
3. Immettere una query e premere INVIO o fare clic sull'icona di ricerca per visualizzare i risultati dalla configurazione corrente. È possibile modificare il tipo di ricerca da eseguire facendo clic su **Web**, **Image**(Immagine), o **Video** per ottenere i risultati corrispondenti. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Modificare la classificazione dei risultati della ricerca specifici

Il portale consente di modificare la classificazione di ricerca di contenuto da domini specifici, pagine secondarie e pagine Web. Dopo l'invio di una query di ricerca nel riquadro di anteprima, ogni risultato di ricerca conterrà un elenco di modifiche che è possibile apportare per:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blocco      | Sposta il dominio, la pagina secondaria o la pagina Web nell'elenco Blocked (Elementi bloccati). Bing esclude il contenuto dal sito selezionato dalla visualizzazione nei risultati di ricerca.                    |
| Aumento priorità      | Aumenta la priorità del contenuto del dominio o della pagina secondaria nei risultati della ricerca.                                                                                        |
| Abbassamento di livello     | Abbassa di livello il contenuto del dominio o della pagina secondaria nei risultati della ricerca. Selezionare se si vuole abbassare di livello il contenuto dal dominio o dalla pagina secondaria a cui appartiene la pagina Web. |
| Aggiungere in alto | Sposta il dominio, la pagina secondaria o la pagina Web nell'elenco **Pinned** (Elementi aggiunti in alto). Questo fa sì che la pagina Web compaia come miglior risultato di ricerca per una data query di ricerca.                   |

La modifica della classificazione non è disponibile per le ricerche di immagini o video.

### <a name="boosting-and-demoting-search-results"></a>Aumento della priorità e abbassamento di livello per i risultati della ricerca

È possibile assegnare la posizione più alta a un dominio o a una pagina secondaria, aumentarne la priorità o abbassarne il livello nell'elenco **Active** (Elementi attivi). Per impostazione predefinita, tutte le sezioni vengono aggiunte con la stessa priorità. Le sezioni Web contrassegnate con Super boosted (Posizione più alta) o Boosted (Priorità aumentata) vengono inserite in una posizione più alta nei risultati della ricerca (con la posizione più alta per gli elementi a cui è stata assegnata la posizione più alta rispetto a quelli per cui è stata aumentata la priorità). Gli elementi di cui viene abbassato il livello vengono inseriti in una posizione più bassa nei risultati della ricerca.

È possibile assegnare la posizione più alta agli elementi, aumentarne la priorità o abbassarne il livello usando i controlli di **Ranking Adjust** (Modifica della classificazione) nell'elenco **Active** (Elementi attivi) o usando i controlli Boost (Aumentare la priorità) e Demote (Abbassare di livello) nel riquadro di anteprima. Il servizio aggiunge la sezione all'elenco Active (Elementi attivi) e ne modifica di conseguenza la classificazione.

> [!NOTE] 
> Aumento della priorità e abbassamento di livello è uno dei molti metodi usati da Ricerca personalizzata Bing per determinare l'ordine dei risultati della ricerca. A causa di altri fattori che influenzano la classificazione di diversi contenuti Web, gli effetti di modifica della classificazione possono variare. Usare il riquadro di anteprima per verificare gli effetti di modifica della classificazione dei risultati della ricerca. 

Le operazioni di assegnazione della posizione più alta, di aumento della priorità e di abbassamento di livello non sono disponibili per Ricerca immagini e video.

## <a name="pin-slices-to-the-top-of-search-results"></a>Aggiungere le sezioni in cima ai risultati di ricerca

Il portale consente inoltre di aggiungere gli URL in cima ai risultati della ricerca per specifici termini di ricerca, usando la scheda **Pinned**(Elementi aggiunti in alto). Immettere un URL e una query per specificare la pagina Web che apparirà come risultato principale. Nota: è possibile aggiungere al massimo una pagina Web per ogni query di ricerca e solo le pagine Web indicizzate saranno visualizzate nelle ricerche. L'aggiunta di risultati non è disponibile per le ricerche di immagini o video.

È possibile aggiungere una pagina Web in cima in due modi:

* Nel **Pinned** , immettere l'URL della pagina Web per aggiungere alla parte superiore e la relativa query corrispondente.

* Nel riquadro **Preview** (Anteprima) immettere un termine di query e fare clic sull'opzione di ricerca. Trovare la pagina Web si desidera aggiungere per la query, fare clic su **Pin verso l'alto**. La pagina Web e la query verranno aggiunte all'elenco **Pinned** (Elementi aggiunti in alto).

### <a name="specify-the-pins-match-condition"></a>Indicare la condizione di corrispondenza dell'elemento aggiunto in alto

Per impostazione predefinita, le pagine Web vengono aggiunte in cima ai risultati della ricerca solo quando la stringa di query dell'utente corrisponde esattamente a una delle pagine elencate in **Pinned** (Elementi aggiunti in alto). È possibile modificare il comportamento predefinito specificando una delle condizioni di corrispondenza seguenti:

> [!NOTE]
> tutti i confronti tra query di ricerca dell'utente e query di ricerca dell'elemento da aggiungere sono maiuscole/minuscole.

| Value | Descrizione                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Starts with (Inizia con) | Affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'utente deve iniziare con la stringa di query dell'elemento aggiunto in alto |
| Ends with (Finisce con)   | Affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'utente deve finire con la stringa di query dell'elemento aggiunto in alto.  |
| Contiene    | Affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'utente deve contenere la stringa di query dell'elemento aggiunto in alto.   |


Per modificare la condizione di corrispondenza dell'elemento aggiunto in alto, fare clic sull'icona di modifica dell'elemento. Nella colonna **Query match condition** (Condizione di corrispondenza query) fare clic sull'elenco a discesa e selezionare la nuova condizione da usare. Fare quindi clic sull'icona di salvataggio per salvare la modifica.

### <a name="change-the-order-of-your-pinned-sites"></a>Modificare l'ordine dei siti aggiunti

Per modificare l'ordine degli elementi aggiunti, è possibile effettuare il trascinamento della selezione o modificare il numero d'ordine facendo clic sull'icona "Modifica" nella colonna dei **Controlli** dell'elenco **Pinned** (Elementi aggiunti in alto).

Se più elementi aggiunti in alto soddisfano una condizione di corrispondenza, Ricerca personalizzata Bing userà il più alto dell'elenco.

## <a name="view-statistics"></a>Visualizzare le statistiche

Se si è sottoscritto il livello appropriato di Ricerca personalizzata (vedere le [pagine sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), viene aggiunta la scheda **Statistics** (Statistiche) per le istanze di produzione. La scheda Statistics (Statistiche) mostra i dettagli sul modo in cui vengono usati gli endpoint di Ricerca personalizzata, inclusi volume delle chiamate, query principali, distribuzione geografica, codici di risposta e ricerca sicura. È possibile filtrare i dettagli con i controlli forniti.

## <a name="usage-guidelines"></a>Linee guida per l'uso

- Per ogni istanza di ricerca personalizzata, il numero massimo di modifiche di classificazione che è possibile apportare alle sezioni **Active** (Elementi attivi) e **Blocked** (Elementi bloccati) è limitato a 400.
- L'aggiunta di una sezione alle schede Active (Elementi attivi) e Blocked (Elementi bloccati) viene considerata come una modifica di classificazione.
- Le operazioni di aumento della priorità e abbassamento di livello valgono come due modifiche di classificazione.
- Per ogni istanza di ricerca personalizzata, il numero massimo di elementi aggiunti in alto consentito è limitato a 200.

## <a name="next-steps"></a>Passaggi successivi

- [Chiamare la ricerca personalizzata](./search-your-custom-view.md)
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
