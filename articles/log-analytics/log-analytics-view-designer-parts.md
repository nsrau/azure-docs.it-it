---
title: Progettazione viste di Log Analytics | Microsoft Docs
description: Progettazione viste di Log Analytics consente di creare viste personalizzate nella console di OMS contenenti visualizzazioni diverse dei dati nel repository di OMS. In questo articolo vengono fornite informazioni di riferimento sulle impostazioni relative a ciascuna parte di visualizzazione disponibile per l'uso nelle viste personalizzate.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: bwren

---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Riferimento alla parte di visualizzazione relativa a Progettazione viste di Log Analytics
Progettazione viste di Log Analytics consente di creare viste personalizzate nella console di OMS contenenti visualizzazioni diverse dei dati provenienti dal repository OMS. In questo articolo vengono fornite informazioni di riferimento sulle impostazioni relative a ciascuna parte di visualizzazione disponibile per l'uso nelle viste personalizzate.

Altri articoli disponibili su Progettazione viste sono:

* [Progettazione viste](log-analytics-view-designer.md): una panoramica su Progettazione viste e sulle procedure per creare e modificare viste personalizzate.
* [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md): informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate. 

La tabella seguente descrive i diversi tipi di riquadri disponibili in Progettazione viste.  Nelle sezioni seguenti vengono descritti i dettagli di ogni tipo di riquadro e le relative proprietà.

| Tipo di visualizzazione | Descrizione |
|:--- |:--- |
| [Elenco di query](#list-of-queries-part) |Visualizza l'elenco delle query di ricerca log.  L'utente può fare clic su ogni query per visualizzarne i risultati.  Per impostazione predefinita la vista includerà una singola query ed è possibile aggiungerne altre facendo clic su **+ Query**. |
| [Numero ed elenco](#number-amp-list-part) |L'intestazione presenta un numero singolo che mostra il conteggio dei record risultanti da una query di ricerca log.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica. |
| [Due numeri ed elenco](#two-numbers-amp-list-part) |L'intestazione presenta due numeri che mostrano il conteggio dei record risultanti da query di ricerca log separate.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica. |
| [Anello ed elenco](#donut-amp-list-part) |Nell'intestazione viene visualizzato un singolo numero riepilogato da una colonna di valore in una query di log.  L'anello visualizza graficamente i risultati dei tre record principali. |
| [Due sequenze temporali ed elenco](#two-timelines-amp-list-part) |L'intestazione mostra i risultati di due query di log nel tempo come grafici a colonne con un callout in cui viene visualizzato un numero singolo riepilogato da una colonna di valori in una query di log.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica. |
| [Informazioni](#information-part) |L'intestazione mostra il testo statico e un collegamento opzionale.  L'elenco mostra uno o più elementi con titolo e testo statico. |
| [Grafico a linee, callout ed elenco](#line-chart-callout-amp-list-part) |Nell'intestazione viene visualizzato un grafico a linee con più serie provenienti da una query di log nel tempo e un callout con un valore riepilogato.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica. |
| [Grafico a linee ed elenco](#line-chart-amp-list-part) |Nell'intestazione viene visualizzato un grafico a linee con più serie provenienti da una query di log nel tempo.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica. |
| [Grafico a linee e grafico sparkline](#line-chart-amp-sparklines-part) |Nell'intestazione viene visualizzato un grafico a linee con più serie provenienti da una query di log nel tempo.  L'elenco mostra i primi dieci risultati da una query con grafico sparkline in cui viene visualizzato il valore di una colonna numerica in un intervallo di tempo selezionato. |

## <a name="list-of-queries-part"></a>Parte relativa all'elenco delle query
Visualizza l'elenco delle query di ricerca log.  L'utente può fare clic su ogni query per visualizzarne i risultati.  

![Visualizzazione dell'elenco delle query](media/log-analytics-view-designer/view-list-queries.png)

| Impostazione | Description |
|:--- |:--- |
| **Generale** | |
| Titolo |Il testo da visualizzare nella parte superiore della vista. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Filtri preselezionati |Elenco delimitato da virgole delle proprietà da includere nel riquadro filtro a sinistra quando l'utente seleziona una query. |
| Modalità di rendering |Visualizzazione iniziale aperta quando si seleziona la query.  L'utente può selezionare qualsiasi vista disponibile dopo l'apertura della query. |
| **Query** | |
| Query di ricerca |Query da eseguire. |
| Nome descrittivo |Nome descrittivo della query da mostrare all'utente. |

## <a name="number-&-list-part"></a>Parte relativa al numero e all'elenco
L'intestazione presenta un numero singolo che mostra il conteggio dei record risultanti da una query di ricerca log.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica.

![Visualizzazione dell'elenco delle query](media/log-analytics-view-designer/view-number-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Il testo da visualizzare nella parte superiore della vista. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Titolo** | |
| Legenda |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Query |La query da eseguire per l'intestazione.  Verrà visualizzata la quantità del numero di record restituiti dalla query. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verranno visualizzate le prime due proprietà per i primi dieci record nei risultati.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  Le barre vengono create automaticamente in base al valore relativo della colonna numerica.<br><br>Usare il comando ordina nella query per ordinare i record nell'elenco.  L'utente può fare clic su Visualizza tutto per eseguire la query e restituire tutti i record. |
| Colore |Il colore assegnato alle barre. |
| Nome e separatore valori |Delimitatore dei singoli caratteri per analizzare la proprietà text in più valori.  Se si specifica un delimitatore, è possibile assegnare i nomi ai campi separati dallo stesso delimitatore nella casella Nome.<br><br>Si consideri, ad esempio, una proprietà denominata "percorso" nella quale vengono inclusi dei valori, come *Redmond-Building 41* e *Bellevue-Building12*.  È possibile specificare – per il nome e separatore valori, così come *City-Building* per il nome.  Ciò comporterebbe l'analisi di ciascun valore in due proprietà chiamate *City* e *Building*. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare {selected item} per includere la sintassi per l'elemento selezionato dall'utente. <br><br>Ad esempio, se la query include una colonna denominata Computer e la query di navigazione è {selected item}, si deve eseguire una query tipo Computer="MyComputer" quando l'utente ha selezionato un computer.  Se la query di navigazione è Type=Event {selected item} viene eseguita la query Type=Event Computer="MyComputer". |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="two-numbers-&-list-part"></a>Parte relativa a Due numeri e all'elenco
L'intestazione presenta due numeri che mostrano il conteggio dei record risultanti da query di ricerca log separate.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica.

![Visualizzazione Due numeri ed elenco](media/log-analytics-view-designer/view-two-numbers-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Il testo da visualizzare nella parte superiore della vista. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Titolo** | |
| Legenda |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Query |La query da eseguire per l'intestazione.  Verrà visualizzata la quantità del numero di record restituiti dalla query. |
| **Secondo titolo** | |
| Legenda |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Query |La query da eseguire per l'intestazione.  Verrà visualizzata la quantità del numero di record restituiti dalla query. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verranno visualizzate le prime due proprietà per i primi dieci record nei risultati.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  Le barre vengono create automaticamente in base al valore relativo della colonna numerica.<br><br>Usare il comando ordina nella query per ordinare i record nell'elenco.  L'utente può fare clic su Visualizza tutto per eseguire la query e restituire tutti i record. |
| Colore |Il colore assegnato alle barre. |
| Nome e separatore valori |Delimitatore dei singoli caratteri per analizzare la proprietà text in più valori.  Se si specifica un delimitatore, è possibile assegnare i nomi ai campi separati dallo stesso delimitatore nella casella Nome.<br><br>Si consideri, ad esempio, una proprietà denominata "percorso" nella quale vengono inclusi dei valori, come Redmond-Building 41 e Bellevue-Building12.  È possibile specificare – per il nome e separatore valori, così come City-Building per il nome.  Ciò comporterebbe l'analisi di ciascun valore in due proprietà chiamate City e Building. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare {selected item} per includere la sintassi per l'elemento selezionato dall'utente. <br><br>Ad esempio, se la query include una colonna denominata Computer e la query di navigazione è {selected item}, si deve eseguire una query tipo Computer="MyComputer" quando l'utente ha selezionato un computer.  Se la query di navigazione è Type=Event {selected item} viene eseguita la query Type=Event Computer="MyComputer". |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="donut-&-list-part"></a>Parte relativa all'anello e all'elenco
Nell'intestazione viene visualizzato un singolo numero riepilogato da una colonna di valore in una query di log.  L'anello visualizza graficamente i risultati dei tre record principali.

![Visualizzazione dell'anello e dell'elenco](media/log-analytics-view-designer/view-donut-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo da visualizzare nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Sottotitolo |Il testo da visualizzare sotto al titolo nella parte superiore dell'intestazione. |
| **Anello** | |
| Query |La query da eseguire per l'anello.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico. |
| **Anello** |**> Centro** |
| Text |Il testo da visualizzare sotto il valore all'interno dell'anello. |
| Operazione |L'operazione da eseguire sulla proprietà del valore per riepilogare un singolo valore.<br><br>- Somma: aggiunge i valori di tutti i record.<br>- Percentuale: percentuale dei record restituiti in base ai valori in **Valori dei risultati usati nell'operazione relativa al centro** nel totale dei record nella query. |
| I valori dei risultati usati nell'operazione centrale |Facoltativamente, fare clic sul segno più per aggiungere uno o più valori.  I risultati della query saranno limitati ai record con i valori di proprietà specificati.  Se non vengono aggiunti valori, tutti i record sono inclusi nella query. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| Colore |Il colore assegnato alle barre. |
| Nome e separatore valori |Delimitatore dei singoli caratteri per analizzare la proprietà text in più valori.  Se si specifica un delimitatore, è possibile assegnare i nomi ai campi separati dallo stesso delimitatore nella casella Nome.<br><br>Si consideri, ad esempio, una proprietà denominata "percorso" nella quale vengono inclusi dei valori, come *Redmond-Building 41* e *Bellevue-Building12*.  È possibile specificare – per il nome e separatore valori, così come *City-Building* per il nome.  Ciò comporterebbe l'analisi di ciascun valore in due proprietà chiamate *City* e *Building*. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare *{selected item}* per includere la sintassi per l'elemento selezionato dall'utente.<br><br>Ad esempio, se la query include una colonna denominata *Computer* e la query di navigazione è *{selected item}*, si deve eseguire una query tipo *Computer="MyComputer"* quando l'utente ha selezionato un computer.  Se la query di navigazione è *Type=Event {selected item}* viene eseguita la query *Type=Event Computer="MyComputer"*. |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="two-timelines-&-list-part"></a>parte relativa alle due sequenze temporali e all'elenco
L'intestazione mostra i risultati di due query di log nel tempo come grafici a colonne con un callout in cui viene visualizzato un numero singolo riepilogato da una colonna di valori in una query di log.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica.   

![Visualizzazione di due sequenze temporali e dell'elenco](media/log-analytics-view-designer/view-two-timelines-list.png)

| Impostazione | Description |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo da visualizzare nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Primo grafico** | |
| Legenda |Testo da visualizzare nel callout della prima serie. |
| Colore |Il colore da usare per le colonne della prima serie. |
| Query |La query da eseguire per la prima serie.  Il conteggio del numero di record in ogni intervallo di tempo sarà rappresentato dalle colonne del grafico. |
| Operazione |L'operazione da eseguire sulla proprietà di valore per riepilogare in un singolo valore per il callout.<br><br>- Media: la media del valore da tutti i record.<br>- Numero: la quantità di tutti i record restituiti dalla query.<br>- Primo campione: il valore risultante dal primo intervallo incluso nel grafico.<br>- Ultimo campione: il valore risultante dall'ultimo intervallo incluso nel grafico.<br>- Max: il valore massimo risultante dagli intervalli inclusi nel grafico. |
| Query |La query da eseguire per il grafico a linee.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati.  Se la query usa la parola chiave **interval**, l'asse X del grafico userà questo intervallo di tempo.  Se la query non include la parola chiave **interval**, vengono usati intervalli orari per l'asse X. |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questa opzione per usare una scala logaritmica per l'asse Y. |
| Unità |Specifica le unità per i valori restituiti dalla query.  Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori.  Tipo di unità specifica la categoria dell'unità e definisce i valori Tipo di unità corrente che sono disponibili.  Se si seleziona un valore in Converti in, i valori numerici vengono convertiti dal tipo Unità corrente al tipo Converti in. |
| Etichetta personalizzata |Il testo da visualizzare per l'asse Y accanto all'etichetta per il tipo di unità.  Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo di unità. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| Colore |Il colore assegnato alle barre. |
| Nome e separatore valori |Delimitatore dei singoli caratteri per analizzare la proprietà text in più valori.  Se si specifica un delimitatore, è possibile assegnare i nomi ai campi separati dallo stesso delimitatore nella casella Nome.<br><br>Si consideri, ad esempio, una proprietà denominata "percorso" nella quale vengono inclusi dei valori, come *Redmond-Building 41* e *Bellevue-Building12*.  È possibile specificare – per il nome e separatore valori, così come *City-Building* per il nome.  Ciò comporterebbe l'analisi di ciascun valore in due proprietà chiamate *City* e *Building*. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare *{selected item}* per includere la sintassi per l'elemento selezionato dall'utente.<br><br>Ad esempio, se la query include una colonna denominata *Computer* e la query di navigazione è *{selected item}*, si deve eseguire una query tipo *Computer="MyComputer"* quando l'utente ha selezionato un computer.  Se la query di navigazione è *Type=Event {selected item}* viene eseguita la query *Type=Event Computer="MyComputer"*. |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="information-part"></a>Parte relativa alle informazioni
L'intestazione mostra il testo statico e un collegamento opzionale.  L'elenco mostra uno o più elementi con titolo e testo statico.

![Visualizzazione delle informazioni](media/log-analytics-view-designer/view-information.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo da visualizzare nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Colore |Il colore di sfondo applicato all'intestazione. |
| **Intestazione** | |
| Image |File di immagine da visualizzare nell'intestazione. |
| Etichetta |Testo da visualizzare nell'intestazione. |
| **Intestazione** |**> Collegamento** |
| Etichetta |Testo del collegamento. |
| Url |URL del collegamento. |
| **Elementi informazione** | |
| Titolo |Testo da visualizzare per il titolo di ogni elemento. |
| Content |Testo da visualizzare per ogni elemento. |

## <a name="line-chart,-callout,-&-list-part"></a>Parte relativa al grafico a linee, al callout e all'elenco
Nell'intestazione viene visualizzato un grafico a linee con più serie provenienti da una query di log nel tempo e un callout con un valore riepilogato.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica

![Visualizzazione del grafico a linee, del callout e dell'elenco](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Impostazione | Description |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo da visualizzare nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Sottotitolo |Il testo da visualizzare sotto al titolo nella parte superiore dell'intestazione. |
| **Grafico a linee** | |
| Query |La query da eseguire per il grafico a linee.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati.  Se la query usa la parola chiave **interval**, l'asse X del grafico userà questo intervallo di tempo.  Se la query non include la parola chiave **interval**, vengono usati intervalli orari per l'asse X. |
| **Grafico a linee** |**> Callout** |
| Titolo del callout |Testo da visualizzare sopra il valore di callout. |
| Nome della serie |Valore della proprietà per la serie da usare per il valore di callout.  Se non viene fornita alcuna serie, vengono usati tutti i record dalla query. |
| Operazione |L'operazione da eseguire sulla proprietà di valore per riepilogare in un singolo valore per il callout.<br><br>- Media: la media del valore di tutti i record.<br>- Numero: la quantità di tutti i record restituiti dalla query.<br>- Ultimo campione: il valore dell'ultimo intervallo incluso nel grafico.<br>- Max: il valore massimo degli intervalli inclusi nel grafico.<br>- Min: il valore minimo risultante dagli intervalli inclusi nel grafico.<br>- Somma: la somma del valore di tutti i record. |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questa opzione per usare una scala logaritmica per l'asse Y. |
| Unità |Specifica le unità per i valori restituiti dalla query.  Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori.  Tipo di unità specifica la categoria dell'unità e definisce i valori Tipo di unità corrente che sono disponibili.  Se si seleziona un valore in Converti in, i valori numerici vengono convertiti dal tipo Unità corrente al tipo Converti in. |
| Etichetta personalizzata |Il testo da visualizzare per l'asse Y accanto all'etichetta per il tipo di unità.  Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo di unità. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| Colore |Il colore assegnato alle barre. |
| Nome e separatore valori |Delimitatore dei singoli caratteri per analizzare la proprietà text in più valori.  Se si specifica un delimitatore, è possibile assegnare i nomi ai campi separati dallo stesso delimitatore nella casella Nome.<br><br>Si consideri, ad esempio, una proprietà denominata "percorso" nella quale vengono inclusi dei valori, come *Redmond-Building 41* e *Bellevue-Building12*.  È possibile specificare – per il nome e separatore valori, così come *City-Building* per il nome.  Ciò comporterebbe l'analisi di ciascun valore in due proprietà chiamate *City* e *Building*. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare *{selected item}* per includere la sintassi per l'elemento selezionato dall'utente.<br><br>Ad esempio, se la query include una colonna denominata *Computer* e la query di navigazione è *{selected item}*, si deve eseguire una query tipo *Computer="MyComputer"* quando l'utente ha selezionato un computer.  Se la query di navigazione è *Type=Event {selected item}* viene eseguita la query *Type=Event Computer="MyComputer"*. |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="line-chart-&-list-part"></a>Parte relativa al grafico a linee e all'elenco
Nell'intestazione viene visualizzato un grafico a linee con più serie provenienti da una query di log nel tempo.  L'elenco mostra i primi dieci risultati da una query con barre orizzontali indicanti il valore relativo di una colonna numerica.

![Visualizzazione del grafico a linee e dell'elenco](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Impostazione | Description |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo da visualizzare nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Sottotitolo |Il testo da visualizzare sotto al titolo nella parte superiore dell'intestazione. |
| **Grafico a linee** | |
| Query |La query da eseguire per il grafico a linee.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati.  Se la query usa la parola chiave **interval**, l'asse X del grafico userà questo intervallo di tempo.  Se la query non include la parola chiave **interval**, vengono usati intervalli orari per l'asse X. |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questa opzione per usare una scala logaritmica per l'asse Y. |
| Unità |Specifica le unità per i valori restituiti dalla query.  Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori.  Tipo di unità specifica la categoria dell'unità e definisce i valori Tipo di unità corrente che sono disponibili.  Se si seleziona un valore in Converti in, i valori numerici vengono convertiti dal tipo Unità corrente al tipo Converti in. |
| Etichetta personalizzata |Il testo da visualizzare per l'asse Y accanto all'etichetta per il tipo di unità.  Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo di unità. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| Colore |Il colore assegnato alle barre. |
| Nome e separatore valori |Delimitatore dei singoli caratteri per analizzare la proprietà text in più valori.  Se si specifica un delimitatore, è possibile assegnare i nomi ai campi separati dallo stesso delimitatore nella casella Nome.<br><br>Si consideri, ad esempio, una proprietà denominata "percorso" nella quale vengono inclusi dei valori, come *Redmond-Building 41* e *Bellevue-Building12*.  È possibile specificare – per il nome e separatore valori, così come *City-Building* per il nome.  Ciò comporterebbe l'analisi di ciascun valore in due proprietà chiamate *City* e *Building*. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare *{selected item}* per includere la sintassi per l'elemento selezionato dall'utente.<br><br>Ad esempio, se la query include una colonna denominata *Computer* e la query di navigazione è *{selected item}*, si deve eseguire una query tipo *Computer="MyComputer"* quando l'utente ha selezionato un computer.  Se la query di navigazione è *Type=Event {selected item}* viene eseguita la query *Type=Event Computer="MyComputer"*. |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="line-chart-&-sparklines-part"></a>Parte relativa al grafico a linee e al grafico sparkline
Nell'intestazione viene visualizzato un grafico a linee con più serie provenienti da una query di log nel tempo.  L'elenco mostra i primi dieci risultati da una query con grafico sparkline in cui viene visualizzato il valore di una colonna numerica in un intervallo di tempo selezionato.

![Grafico a linee e grafici sparkline](media/log-analytics-view-designer/view-line-chart-sparklines.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo da visualizzare nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questa opzione per creare un nuovo gruppo nella vista che inizia con la visualizzazione corrente. |
| Icona |File di immagine da visualizzare accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questa opzione per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Il testo da visualizzare nella parte superiore dell'intestazione. |
| Sottotitolo |Il testo da visualizzare sotto al titolo nella parte superiore dell'intestazione. |
| **Grafico a linee** | |
| Query |La query da eseguire per il grafico a linee.  La prima proprietà deve essere un valore di testo, mentre la seconda proprietà un valore numerico.  In genere si tratta di una query che usa la parola chiave **measure** per riepilogare i risultati.  Se la query usa la parola chiave **interval**, l'asse X del grafico userà questo intervallo di tempo.  Se la query non include la parola chiave **interval**, vengono usati intervalli orari per l'asse X. |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questa opzione per usare una scala logaritmica per l'asse Y. |
| Unità |Specifica le unità per i valori restituiti dalla query.  Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori.  Tipo di unità specifica la categoria dell'unità e definisce i valori Tipo di unità corrente che sono disponibili.  Se si seleziona un valore in Converti in, i valori numerici vengono convertiti dal tipo Unità corrente al tipo Converti in. |
| Etichetta personalizzata |Il testo da visualizzare per l'asse Y accanto all'etichetta per il tipo di unità.  Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo di unità. |
| **Elenco** | |
| Query |La query da eseguire per l'elenco.  Verrà visualizzato il conteggio del numero di record restituiti dalla query. |
| Operazione |L'operazione da eseguire su ogni proprietà nell'elenco per calcolare i valori per il grafico sparkline.<br><br>- Ultimo campione: l'ultimo valore per la serie nell'intervallo di tempo.<br>- Max: il valore massimo per la serie nell'intervallo di tempo.<br>- Min: il valore minimo per la serie nell'intervallo di tempo.<br>- Somma: la somma dei valori per la serie nell'intervallo di tempo.<br>-Riepilogo: usa lo stesso comando di misurazione come query nell'intestazione. |
| Query di navigazione |Query da eseguire quando l'utente seleziona un elemento nell'elenco.  Usare *{selected item}* per includere la sintassi per l'elemento selezionato dall'utente.<br><br>Ad esempio, se la query include una colonna denominata *Computer* e la query di navigazione è *{selected item}*, si deve eseguire una query tipo *Computer="MyComputer"* quando l'utente ha selezionato un computer.  Se la query di navigazione è *Type=Event {selected item}* viene eseguita la query *Type=Event Computer="MyComputer"*. |
| **Elenco** |**> Titoli di colonna** |
| Nome |Testo da visualizzare nella parte superiore della prima colonna dell'elenco. |
| Valore |Testo da visualizzare nella parte superiore della seconda colonna dell'elenco. |

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [ricerche log](log-analytics-log-searches.md) per supportare le query nelle parti di visualizzazione.

<!--HONumber=Oct16_HO2-->


