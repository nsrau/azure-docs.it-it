---
title: 'Ricerca personalizzata Bing: Definire una visualizzazione personalizzata | Microsoft Docs'
description: Descrive come creare un sito e servizi di ricerca verticale
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ef8aabc7363db88317a6428301512b0a0d4c055
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158773"
---
# <a name="configure-your-custom-search-experience"></a>Configurare un'esperienza di ricerca personalizzata
Un'istanza di Ricerca personalizzata consente di personalizzare l'esperienza di ricerca in modo da includere il contenuto solo dai siti Web a cui sono interessati gli utenti. Anziché eseguire una ricerca in tutto il Web, Bing esegue la ricerca solo nella parte di Web desiderata.
Per creare una visualizzazione personalizzata del Web, usare il [portale](https://customsearch.ai) di Ricerca personalizzata Bing. Per informazioni sull'accesso al portale, vedere [Creare la prima istanza di Ricerca personalizzata Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). Il portale consente di creare un'istanza di ricerca che specifica i domini, le pagine secondarie e le pagine Web in cui si vuole eseguire la ricerca con Bing e quelli in cui non si vuole eseguire la ricerca. Oltre a specificare gli URL del contenuto che si conosce, è anche possibile chiedere al portale di suggerire contenuto da potere aggiungere alla visualizzazione. Di seguito sono indicati i modi in cui è possibile definire una parte del Web: 

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

Per modificare o eliminare le sezioni, usare le opzioni nella colonna Controls (Controlli). 

Analogamente, è possibile aggiungere sezioni all'elenco Blocked (Elementi bloccati), ad eccezione del fatto che non è possibile usare un file di caricamento per specificare le sezioni.

## <a name="pinned-list"></a>Elenco Pinned (Elementi aggiunti in alto)
Il portale consente anche di aggiungere una pagina Web specifica in alto nei risultati della ricerca, se l'utente immette un termine di ricerca specifico. La scheda **Pinned** (Elementi aggiunti in alto) contiene un elenco di coppie costituite da termine di query e pagina Web che specificano la pagina Web che viene visualizzata come primo risultato per una query specifica. Il termine di query dell'utente deve corrispondere esattamente al termine di query dell'elemento aggiunto in alto.
Per informazioni sull'aggiunta di risultati, vedere [Modificare la classificazione](#adjustrank).

L'aggiunta di risultati non è disponibile per l'esperienza di Ricerca immagini.

## <a name="site-suggestions"></a>Suggerimenti di siti
Dopo l'aggiunta delle sezioni all'elenco Active (Elementi attivi), il servizio genera i suggerimenti di siti e pagine secondarie che è possibile aggiungere alla ricerca. La sezione **You might want to add** (È possibile aggiungere) contiene i suggerimenti. La pagina delle impostazioni dell'istanza include questa sezione solo se sono disponibili suggerimenti. 

Per aggiungere suggerimenti all'elenco Active (Elementi attivi), fare clic sull'icona +.  Poiché il servizio genera i suggerimenti in base alle impostazioni specificate, assicurarsi di fare clic su **Refresh**  (Aggiorna) dopo aver aggiunto i suggerimenti. 

## <a name="preview-pane"></a>Riquadro di anteprima
È possibile testare l'istanza di ricerca usando il riquadro di anteprima a destra per inviare le query di ricerca e visualizzare i risultati. Selezionare **My Instance** (Istanza personale), selezionare un filtro di ricerca sicura e il mercato in cui eseguire la ricerca (vedere i [parametri della query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Immettere una query e premere INVIO o fare clic sull'icona di ricerca per visualizzare i risultati dalla configurazione corrente. Per visualizzare i risultati Web, fare clic su **Web**; per visualizzare i risultati immagini, fare clic su **Image** (Immagine). 

 Usando il riquadro di anteprima, è anche possibile rivedere i risultati di Bing selezionando **Bing** anziché **My Instance** (Istanza personale). Ciò può essere utile per confrontare i risultati dell'esperienza di ricerca con i risultati restituiti da Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Modificare la classificazione
Il portale consente di modificare la classificazione per controllare i risultati restituiti da Bing. Nel riquadro di anteprima immettere un termine di ricerca ed eseguire la query. Il riquadro di anteprima elenco risultati della ricerca per la query. A destra di ogni risultato è presente l'elenco di modifiche che è possibile apportare. 

- Disporre di blocchi. Sposta il dominio, la pagina secondaria o la pagina Web nell'elenco Blocked (Elementi bloccati). È possibile selezionare il livello di blocco. Bing esclude il contenuto dal sito selezionato nei risultati della ricerca. 
- Aumentare la priorità. Aumenta la priorità del contenuto del dominio o della pagina secondaria nei risultati della ricerca. Selezionare se si vuole aumentare la priorità del contenuto dal dominio o dalla pagina secondaria a cui appartiene la pagina Web.
- Abbassare di livello. Abbassa di livello il contenuto del dominio o della pagina secondaria nei risultati della ricerca. Selezionare se si vuole abbassare di livello il contenuto dal dominio o dalla pagina secondaria a cui appartiene la pagina Web. 
- Aggiungere in alto. Definire la pagina Web che viene visualizzata in cima ai risultati se il termine della query corrisponde esattamente al termine della query usato. L'elenco Active (Elementi attivi) non deve contenere la pagina Web da aggiungere. 

La modifica della classificazione non è disponibile per l'esperienza di Ricerca immagini.

## <a name="boosting-and-demoting"></a>Aumento della priorità e abbassamento di livello
È possibile assegnare la posizione più alta a un dominio o a una pagina secondaria, aumentarne la priorità o abbassarne il livello nell'elenco Active (Elementi attivi). Per impostazione predefinita, tutte le sezioni vengono aggiunte con la stessa priorità. Gli elementi contrassegnati con Super boosted (Posizione più alta) o Boosted (Priorità aumentata) vengono inseriti in una posizione più alta nei risultati della ricerca (con la posizione più alta per gli elementi a cui è stata assegnata la posizione più alta rispetto a quelli per cui è stata aumentata la priorità). Gli elementi di cui viene abbassato il livello vengono inseriti in una posizione più bassa nei risultati della ricerca.

È importante notare che le operazioni di assegnazione della posizione più alta, di aumento della priorità e di abbassamento di livello attribuiscono rispettivamente priorità diverse ai domini o alle pagine secondarie. Questo è solo uno dei tanti modi usati dallo strumento di classificazione per determinare l'ordine dei risultati. Ciò significa che il risultato di una query specifica non è garantito perché molti altri fattori potrebbero influenzare la classificazione generale dei risultati Web.  Per determinare il possibile effetto prodotto dall'aumento della priorità o dall'abbassamento di livello sullo strumento di classificazione, provare l'esperienza di ricerca usando il riquadro di anteprima.

È possibile assegnare la posizione più alta agli elementi, aumentarne la priorità o abbassarne il livello usando i controlli di Ranking Adjust (Modifica della classificazione) nell'elenco Active (Elementi attivi) o usando i controlli Boost (Aumentare la priorità) e Demote (Abbassare di livello) nel riquadro di anteprima. Il servizio aggiunge la sezione all'elenco Active (Elementi attivi) e ne modifica di conseguenza la classificazione.

Le modifiche in seguito alle operazioni di assegnazione della posizione più alta, di aumento della priorità e di abbassamento di livello vengono salvate automaticamente e vengono applicate immediatamente all'endpoint di ricerca personalizzato. 

Le operazioni di assegnazione della posizione più alta, di aumento della priorità e di abbassamento di livello non sono disponibili per l'esperienza di Ricerca immagini.

## <a name="pin-to-top"></a>Aggiungere in alto
Per aggiungere una pagina Web in cima ai risultati della ricerca per una query specifica, scegliere una delle opzioni seguenti:

1.  Nella scheda Pinned (Elementi aggiunti in alto) immettere l'URL della pagina Web da aggiungere in cima ai risultati e la query esatta che attiverà l'aggiunta in alto. 
2.  Nel riquadro di anteprima immettere un termine di query e fare clic sull'icona di ricerca. Identificare quindi la pagina Web nei risultati che si vuole aggiungere in cima ai risultati, se l'utente immette la stessa query. Fare quindi clic su Pin to top (Aggiungi in alto). Il servizio aggiunge la pagina Web e la query all'elenco Pinned (Elementi aggiunti in alto). 

È possibile tenere traccia degli elementi aggiunti in alto nella scheda Pinned (Elementi aggiunti in alto). Gli elementi aggiunti in alto vengono visualizzati come coppie '\<query\>, \<pagina Web\>'. 

La pagina Web viene aggiunta in alto solo se la query dell'utente corrisponde esattamente alla query specificata. 

Per una query specifica, è possibile aggiungere in alto un massimo di una pagina Web in cima ai risultati.

Gli elementi aggiunti in alto non sono disponibili per l'esperienza di Ricerca immagini.

## <a name="use-bing-to-specify-slices"></a>Usare Bing per specificare le sezioni
Esistono due modi per specificare le sezioni del Web che costituiscono la ricerca personalizzata. Se si conoscono le sezioni da includere nell'istanza, aggiungerle all'elenco Active (Elementi attivi) dell'istanza. Per informazioni sull'aggiunta di elementi all'elenco Active (Elementi attivi), vedere [Elenchi Active (Elementi attivi) e Blocked (Elementi bloccati)](#active-and-blocked-lists).
Ma se non si è certi di quali sezioni includere, è possibile eseguire le query di Bing nel riquadro di anteprima e vedere i risultati restituiti da Bing. È quindi possibile selezionare le sezioni da includere nella ricerca personalizzata. È probabile che sia necessario eseguire più termini di query per assicurarsi di identificare tutte le sezioni desiderate per l'istanza. 

Seguire questi passaggi per usare Bing per aggiungere sezioni all'istanza di Ricerca personalizzata. 
1.  Accedere al [portale](https://customsearch.ai) di Ricerca personalizzata Bing.
2.  Creare un'istanza o selezionare un'istanza da aggiornare.
3.  Nel riquadro di anteprima a destra selezionare Bing nell'elenco a discesa.
4.  Nella casella di ricerca immettere un termine di query che è pertinente per l'istanza.
5.  Fare clic su **Add site** (Aggiungi sito) accanto al risultato da includere.
6.  Fare clic sul pulsante **OK** .

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

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