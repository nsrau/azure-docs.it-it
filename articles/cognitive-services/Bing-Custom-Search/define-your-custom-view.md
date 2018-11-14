---
title: Definire una vista personalizzata - Ricerca personalizzata Bing
titlesuffix: Azure Cognitive Services
description: Descrive come creare un sito e servizi di ricerca verticale
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 5e168608202f81f698221b52315de83e9713feab
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282924"
---
# <a name="configure-your-custom-search-experience"></a>Configurare un'esperienza di ricerca personalizzata

Un'istanza di Ricerca personalizzata consente di personalizzare l'esperienza di ricerca in modo da includere il contenuto solo dai siti Web a cui sono interessati gli utenti. Anziché eseguire una ricerca in tutto il Web, Bing esegue la ricerca solo nella parte di Web desiderata. Per creare una visualizzazione personalizzata del Web, usare il [portale](https://customsearch.ai) di Ricerca personalizzata Bing. Per informazioni sull'accesso al portale, vedere [Creare la prima istanza di Ricerca personalizzata Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). 

Il portale consente di creare un'istanza di ricerca che specifica i domini, le pagine secondarie e le pagine Web in cui si vuole eseguire la ricerca con Bing e quelli in cui non si vuole eseguire la ricerca. Oltre a specificare gli URL del contenuto che si conosce, è anche possibile chiedere al portale di suggerire contenuto da potere aggiungere alla visualizzazione. 

Di seguito sono indicati i modi in cui è possibile definire una parte del Web: 

1.  Dominio. La sezione di un dominio include tutto il contenuto presente all'interno di un dominio Internet. Ad esempio, www.microsoft.com. Se si omette 'www', Bing eseguirà la ricerca anche nei sottodomini del dominio. Ad esempio, se si specifica microsoft.com, Bing restituirà anche i risultati provenienti da support.microsoft.com o da technet.microsoft.com.  
  
2.  Pagina secondaria. La sezione di una pagina secondaria include tutto il contenuto disponibile nella pagina secondaria e i relativi percorsi. È possibile specificare un massimo di due pagine secondarie nel percorso. Ad esempio, www.microsoft.com/en-us/windows/  
  
3.  Pagina Web. La sezione di una pagina Web può includere solo la pagina Web in una ricerca personalizzata. Facoltativamente, è possibile specificare se includere pagine secondarie.

Tutti i domini, le pagine secondarie e le pagine Web specificati devono essere pubblici e indicizzati da Bing. Se si è proprietari di un sito pubblico che si vuole includere nella ricerca e non è stato indicizzato da Bing, vedere la [documentazione per webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bing per informazioni dettagliate sull'indicizzazione di Bing. Inoltre, la documentazione per webmaster contiene informazioni dettagliate su come aggiornare un sito sottoposto a ricerca per indicizzazione, se l'indice non è aggiornato.

## <a name="adding-slices-to-your-custom-search"></a>Aggiunta di sezioni alla ricerca personalizzata

Quando si definisce un'istanza di ricerca personalizzata, specificare i domini, le pagine secondarie e le pagine Web attivi e bloccati in cui eseguire la ricerca o quelli in cui non si vuole eseguire la ricerca.  

- Elementi attivi: elenco di domini, pagine secondarie o pagine Web da includere nella ricerca.  
  
- Elementi bloccati: elenco di domini, pagine secondarie o pagine Web da escludere dalla ricerca. Gli elementi bloccati devono far parte del contenuto presente nei domini e nelle pagine secondarie elencati nell'elenco Active (Elementi attivi).

Per accedere a ogni elenco, fare clic sulle schede Active (Elementi attivi) e Blocked (Elementi bloccati) nell'istanza di ricerca personalizzata. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Elenchi Active (Elementi attivi) e Blocked (Elementi bloccati) 

Per specificare una sezione del Web in cui si vuole eseguire la ricerca con Bing, fare clic sulla scheda **Active** (Elementi attivi) ed elencare i domini, le pagine secondarie e le pagine Web in cui eseguire la ricerca. È possibile aggiungere una sezione direttamente all'elenco o aggiungere più di un'unica sezione caricando un file di testo con l'icona di caricamento.

Dettagli per il caricamento di file: 

- Il caricamento di file è disponibile solo per l'aggiunta di sezioni all'elenco Active (Elementi attivi). Non è possibile usarlo per aggiungere sezioni all'elenco Blocked (Elementi bloccati).  
  
- Creare un file di testo e specificare un solo dominio, una sola pagina secondaria o una sola pagina Web per ogni riga. Se si verifica un errore, verrà rifiutato l'intero caricamento.  
  
- Se l'elenco Blocked (Elementi bloccati) contiene il dominio, la pagina secondaria o la pagina Web specificata nel file di caricamento, il servizio lo rimuove dall'elenco Blocked (Elementi bloccati) e lo aggiunge all'elenco Active (Elementi attivi).  
  
- Il servizio ignora gli elementi duplicati nel file di caricamento.

Per modificare o eliminare le sezioni, usare le opzioni nella colonna **Controls** (Controlli). 

Analogamente, è possibile aggiungere sezioni all'elenco Blocked (Elementi bloccati), ad eccezione del fatto che non è possibile usare un file di caricamento per specificare le sezioni.

## <a name="pinned-list"></a>Elenco Pinned (Elementi aggiunti in alto)

Il portale consente anche di aggiungere una pagina Web specifica in alto nei risultati della ricerca, se l'utente immette un termine di ricerca specifico. La scheda **Pinned** (Elementi aggiunti in alto) contiene un elenco di coppie costituite da termine di query e pagina Web che specificano la pagina Web che viene visualizzata come primo risultato per una query specifica. Inoltre, nelle ricerche verranno visualizzate solo le pagine Web indicizzate. Per informazioni sull'aggiunta di risultati, vedere [Modificare la classificazione](#adjustrank). 

L'aggiunta in alto dei risultati non è disponibile per le esperienze di ricerca di immagini e video.

## <a name="website-suggestions"></a>Suggerimenti di siti Web

Dopo l'aggiunta delle sezioni all'elenco Active (Elementi attivi), il servizio genera i suggerimenti di siti Web e pagine secondarie che è possibile aggiungere alla ricerca. La sezione **You might want to add** (È possibile aggiungere) contiene i suggerimenti. La pagina delle impostazioni dell'istanza include questa sezione solo se sono disponibili suggerimenti. 

Per aggiungere suggerimenti all'elenco Active (Elementi attivi), fare clic sull'icona +.  Poiché il servizio genera i suggerimenti in base alle impostazioni specificate, assicurarsi di fare clic su **Refresh**  (Aggiorna) dopo aver aggiunto i suggerimenti. 

## <a name="preview-pane"></a>Riquadro di anteprima

È possibile testare l'istanza di ricerca usando il riquadro di anteprima a destra per inviare le query di ricerca e visualizzare i risultati. 

1. Selezionare **My Instance** (Istanza personale).
2. Selezionare un filtro di ricerca sicura e il mercato in cui eseguire la ricerca. Vedere [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) (Parametri di query).
3. Immettere una query e premere INVIO o fare clic sull'icona di ricerca per visualizzare i risultati dalla configurazione corrente. 

Per selezionare il tipo di ricerca da eseguire, fare clic su **Web** per ottenere i risultati Web, su **Image** (Immagine) per ottenere i risultati relativi alle immagini o su **Video** per ottenere i risultati relativi ai video. 

Usando il riquadro di anteprima, è anche possibile rivedere i risultati di Bing selezionando **Bing** anziché **My Instance** (Istanza personale). Ciò può essere utile per confrontare i risultati dell'esperienza di ricerca con i risultati restituiti da Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Modificare la classificazione

Il portale consente di modificare la classificazione per controllare i risultati restituiti da Bing. Nel riquadro di anteprima immettere un termine di ricerca ed eseguire la query. Il riquadro di anteprima elenco risultati della ricerca per la query. A destra di ogni risultato è presente l'elenco di modifiche che è possibile apportare. 

- Disporre di blocchi. Sposta il dominio, la pagina secondaria o la pagina Web nell'elenco Blocked (Elementi bloccati). È possibile selezionare il livello di blocco. Bing esclude il contenuto dal sito selezionato nei risultati della ricerca.  
  
- Aumentare la priorità. Aumenta la priorità del contenuto del dominio o della pagina secondaria nei risultati della ricerca. Scegliere se aumentare la priorità dei contenuti dal dominio o dalla pagina secondaria a cui appartiene la pagina Web. [Altre informazioni](#boosting-and-demoting)  
  
- Abbassare di livello. Abbassa di livello il contenuto del dominio o della pagina secondaria nei risultati della ricerca. Selezionare se si vuole abbassare di livello il contenuto dal dominio o dalla pagina secondaria a cui appartiene la pagina Web. [Altre informazioni](#boosting-and-demoting).  
  
- Aggiungere in alto. Definire la pagina Web visualizzata in cima ai risultati se la stringa di query dell'utente corrisponde esattamente alla stringa di query degli elementi aggiunti in alto in base alla condizione di corrispondenza dell'elemento aggiunto in alto. L'elenco Active (Elementi attivi) non deve contenere la pagina Web da aggiungere. [Altre informazioni](#pin-to-top).

L'aggiunta della classificazione non è disponibile per le esperienze di ricerca di immagini e video.

## <a name="boosting-and-demoting"></a>Aumento della priorità e abbassamento di livello

È possibile assegnare la posizione più alta a un dominio o a una pagina secondaria, aumentarne la priorità o abbassarne il livello nell'elenco Active (Elementi attivi). Per impostazione predefinita, tutte le sezioni vengono aggiunte con la stessa priorità. Gli elementi contrassegnati con Super boosted (Posizione più alta) o Boosted (Priorità aumentata) vengono inseriti in una posizione più alta nei risultati della ricerca (con la posizione più alta per gli elementi a cui è stata assegnata la posizione più alta rispetto a quelli per cui è stata aumentata la priorità). Gli elementi di cui viene abbassato il livello vengono inseriti in una posizione più bassa nei risultati della ricerca.

È importante notare che le operazioni di assegnazione della posizione più alta, di aumento della priorità e di abbassamento di livello attribuiscono rispettivamente priorità diverse ai domini o alle pagine secondarie. Questo è solo uno dei tanti modi usati dallo strumento di classificazione per determinare l'ordine dei risultati. Ciò significa che il risultato di una query specifica non è garantito perché molti altri fattori potrebbero influenzare la classificazione generale dei risultati Web.  Per determinare il possibile effetto prodotto dall'aumento della priorità o dall'abbassamento di livello sullo strumento di classificazione, provare l'esperienza di ricerca usando il riquadro di anteprima.

È possibile assegnare la posizione più alta agli elementi, aumentarne la priorità o abbassarne il livello usando i controlli di Ranking Adjust (Modifica della classificazione) nell'elenco Active (Elementi attivi) o usando i controlli Boost (Aumentare la priorità) e Demote (Abbassare di livello) nel riquadro di anteprima. Il servizio aggiunge la sezione all'elenco Active (Elementi attivi) e ne modifica di conseguenza la classificazione.

Le operazioni di assegnazione della posizione più alta, aumento della priorità e abbassamento di livello non sono disponibili per le esperienze di ricerca di immagini e video.

## <a name="pin-to-top"></a>Aggiungere in alto

Per aggiungere una pagina Web in cima ai risultati della ricerca per una query specifica, scegliere una delle opzioni seguenti:

1.  Nella scheda  **Pinned**  (Elementi aggiunti in alto) immettere l'URL della pagina Web da aggiungere in cima ai risultati e la query che attiva l'aggiunta in alto.  
  
2.  Nel riquadro **Preview** (Anteprima) immettere un termine di query e fare clic sull'opzione di ricerca. Identificare quindi la pagina Web nei risultati che si vuole aggiungere in cima ai risultati, se l'utente immette la stessa query. Fare quindi clic su  **Pin to top** (Aggiungi in alto). Il servizio aggiunge la pagina Web e la query all'elenco **Pinned** (Elementi aggiunti in alto). 

È possibile tenere traccia degli elementi aggiunti in alto nella scheda  **Pinned**  (Elementi aggiunti in alto). Gli elementi aggiunti in alto vengono visualizzati come coppie '\<query\>, \<pagina Web\>'. 

Per una query specifica, è possibile aggiungere in alto un massimo di una pagina Web in cima ai risultati.

Gli elementi aggiunti in alto non sono disponibili per le esperienze di ricerca di immagini e video.

### <a name="specifying-the-pins-match-condition"></a>Indicazione della condizione di corrispondenza dell'elemento aggiunto in alto

La pagina Web viene aggiunta in cima ai risultati solo se la stringa di query dell'utente corrisponde alla stringa di query dell'elemento aggiunto in alto in base alla condizione di corrispondenza dell'elemento aggiunto in alto. Per impostazione predefinita, affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'elemento aggiunto in alto e quella dell'utente devono corrispondere esattamente. È possibile modificare il comportamento predefinito specificando una delle condizioni di corrispondenza seguenti.

- Starts with (Inizia con): affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'utente deve iniziare con la stringa di query dell'elemento aggiunto in alto.
- Ends with (Finisce con): affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'utente deve finire con la stringa di query dell'elemento aggiunto in alto.
- Contains (Contiene): affinché vi sia una corrispondenza per l'elemento aggiunto in alto, la stringa di query dell'utente deve contenere la stringa di query dell'elemento aggiunto in alto.

Per modificare la condizione di corrispondenza dell'elemento aggiunto in alto, fare clic sull'icona di modifica dell'elemento, a forma di matita. Nella colonna **Query match condition** (Condizione di corrispondenza query) fare clic sull'elenco a discesa e selezionare la nuova condizione da usare. Fare quindi clic sull'icona di salvataggio per salvare la modifica.

Tutte le operazioni di confronto non fanno distinzione tra maiuscole e minuscole.

### <a name="changing-the-order-of-the-pins"></a>Modifica dell'ordine degli elementi aggiunti in alto

Per modificare l'ordine degli elementi aggiunti in alto, è possibile trascinare e rilasciare l'elemento oppure modificarlo e cambiare il relativo numero di posizione. Per trascinare e rilasciare un elemento aggiunto in alto, fare clic sull'elemento da spostare nell'elenco, tenere premuto il pulsante del mouse e trascinare l'elemento sopra a quello che si vuole sostituire, quindi rilasciare il pulsante del mouse. Si noti che il numero di posizione dell'elemento aggiunto in alto cambia di conseguenza.

È anche possibile modificare il numero di posizione dell'elemento aggiunto in alto. Nella colonna **Controls** (Controlli) fare clic sull'icona di modifica dell'elemento, a forma di matita. Immettere il numero di posizione con cui si vuole che venga visualizzato l'elemento aggiunto in alto nell'elenco e premere INVIO o fare clic sull'icona di salvataggio. Se, ad esempio, l'elemento aggiunto in alto si trova attualmente nella sesta posizione nell'elenco e lo si vuole spostare in seconda posizione, modificare il numero di posizione su due (2).

Se più elementi aggiunti in alto soddisfano la condizione di corrispondenza, il loro ordine determina gli elementi usati da Bing. Se, ad esempio, gli elementi due e tre soddisfano la condizione di corrispondenza, Bing usa l'elemento due.

## <a name="use-bing-to-specify-slices"></a>Usare Bing per specificare le sezioni

Esistono due modi per specificare le sezioni del Web che costituiscono la ricerca personalizzata. Se si conoscono le sezioni da includere nell'istanza, aggiungerle all'elenco **Active** (Elementi attivi) dell'istanza. Per informazioni sull'aggiunta di elementi all'elenco **Active** (Elementi attivi), vedere [Elenchi Active (Elementi attivi) e Blocked (Elementi bloccati)](#active-and-blocked-lists).

Se invece non si è certi di quali sezioni includere, è possibile eseguire le query di Bing nel riquadro **Preview** (Anteprima) e vedere i risultati restituiti da Bing. È quindi possibile selezionare le sezioni da includere nella ricerca personalizzata. È probabile che sia necessario eseguire più termini di query per assicurarsi di identificare tutte le sezioni desiderate per l'istanza. 

Seguire questi passaggi per usare Bing per aggiungere sezioni all'istanza di Ricerca personalizzata. 

1.  Accedere al [portale](https://customsearch.ai) di Ricerca personalizzata Bing.
2.  Creare un'istanza o selezionare un'istanza da aggiornare.
3.  Nel riquadro di anteprima a destra selezionare Bing nell'elenco a discesa.
4.  Nella casella di ricerca immettere un termine di query che è pertinente per l'istanza.
5.  Fare clic su **Add site** (Aggiungi sito) accanto al risultato da includere.
6.  Fare clic sul pulsante **OK** .

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Visualizzare le statistiche

Se si è sottoscritto il livello appropriato di Ricerca personalizzata (vedere le [pagine sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), viene aggiunta la scheda **Statistics** (Statistiche) per le istanze di produzione. La scheda Statistics (Statistiche) mostra i dettagli sul modo in cui vengono usati gli endpoint di Ricerca personalizzata, inclusi volume delle chiamate, query principali, distribuzione geografica, codici di risposta e ricerca sicura. È possibile filtrare i dettagli con i controlli forniti.

## <a name="understanding-quota"></a>Informazioni sulle quote

- Per ogni istanza di ricerca personalizzata, il numero massimo di modifiche di classificazione che è possibile apportare alle sezioni **Active** (Elementi attivi) e **Blocked** (Elementi bloccati) è limitato a 400.
- L'aggiunta di una sezione alle schede Active (Elementi attivi) e Blocked (Elementi bloccati) viene considerata come una modifica di classificazione.
- Le operazioni di aumento della priorità e abbassamento di livello valgono come due modifiche di classificazione.
- Per ogni istanza di ricerca personalizzata, il numero massimo di elementi aggiunti in alto consentito è limitato a 200.

## <a name="next-steps"></a>Passaggi successivi

- [Chiamare la ricerca personalizzata](./search-your-custom-view.md)
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)