---
title: Cartelle di lavoro di monitoraggio di Azure con parametri personalizzati
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 19fd8c108e8075d30ca494ca75d52952849c284a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872843"
---
# <a name="interactive-workbooks"></a>Cartelle di lavoro interattive

Le cartelle di lavoro consentono agli autori di creare report interattivi ed esperienze per i consumer. L'interattività è supportata in diversi modi.

## <a name="parameter-changes"></a>Modifiche ai parametri
Quando un utente di una cartella di lavoro aggiorna un parametro, qualsiasi controllo che utilizza il parametro viene aggiornato e ridisegnato automaticamente per riflettere il nuovo stato. Questo è il modo in cui la maggior parte dei report portale di Azure supporta l'interattività. Le cartelle di lavoro forniscono questa operazione in modo semplice e con il minimo sforzo utente.

Altre informazioni sui [parametri nelle cartelle di lavoro](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Clic sulla riga della griglia
Le cartelle di lavoro consentono agli autori di creare scenari in cui fare clic su una riga in una griglia per aggiornare i grafici successivi in base al contenuto della riga. 

Ad esempio, un utente può avere una griglia che mostra un elenco di richieste e alcune statistiche come i conteggi degli errori. Potrebbero configurarlo in modo che facendo clic su una riga corrispondente a una richiesta venga visualizzato un grafico dettagliato sotto l'aggiornamento per filtrare solo la richiesta.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Impostazione dell'interattività sulla riga della griglia fare clic
1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
2. Usare il collegamento _Aggiungi query_ per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come _log_, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` per visualizzare i risultati
6. Fare clic sull'icona _delle impostazioni avanzate_ nel piè di pagina della query (l'icona ha l'aspetto di un ingranaggio). Verrà visualizzato il riquadro Impostazioni avanzate 
7. Controllare l'impostazione: `When an item is selected, export a parameter`
    1. Campo da esportare: `Request`
    2. Nome parametro: `SelectedRequest`
    3. Valore predefinito: `All requests`
    
    ![Immagine che mostra l'editor avanzato con le impostazioni per l'esportazione dei campi come parametri](./media/workbooks-interactive/advanced-settings.png)

8. Fare clic su `Done Editing`.
9. Aggiungere un altro controllo query usando i passaggi 2 e 3.
10. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` per visualizzare i risultati.
12. Modificare la _visualizzazione_ in `Area chart`
12. Fare clic su una riga nella prima griglia. Si noti che il grafico ad area seguente Filtra la richiesta selezionata.

Il report risultante ha un aspetto simile al seguente in modalità di modifica:

![Immagine che mostra la creazione di un'esperienza interattiva usando i clic della riga della griglia](./media/workbooks-interactive//grid-click-create.png)

Nell'immagine seguente viene illustrato un report interattivo più elaborato in modalità di lettura in base agli stessi principi. Il report USA i clic della griglia per esportare i parametri, che a sua volta viene usato in due grafici e in un blocco di testo.

![Immagine che mostra la creazione di un'esperienza interattiva usando i clic della riga della griglia](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Esportazione del contenuto di un'intera riga
A volte è preferibile esportare l'intero contenuto della riga selezionata anziché solo una colonna specifica. In questi casi, lasciare la proprietà `Field to export` annullata nel passaggio 7,1 precedente. Le cartelle di lavoro esporteranno l'intero contenuto della riga come JSON nel parametro. 

Nel controllo KQL di riferimento usare la funzione `todynamic` per analizzare il codice JSON e accedere alle singole colonne.

 ## <a name="grid-cell-clicks"></a>Clic sulla cella della griglia
Le cartelle di lavoro consentono agli autori di aggiungere interattività tramite un tipo speciale di renderer della colonna Grid denominato `link renderer`. Un renderer di collegamenti converte una cella della griglia in un collegamento ipertestuale in base al contenuto della cella. Le cartelle di lavoro supportano molti tipi di renderer di collegamenti, inclusi quelli che consentono l'apertura di pannelli di panoramica delle risorse, visualizzatori del contenitore delle proprietà, ricerca di App Insights, utilizzo, traccia delle transazioni e così via.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Impostazione dell'interattività tramite i clic della cella della griglia
1. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
2. Usare il collegamento _Aggiungi query_ per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come _log_, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` per visualizzare i risultati
6. Fare clic su _Impostazioni colonna_ per aprire il riquadro Impostazioni.
7. Nella sezione _Columns_ impostare:
    1. Renderer di colonna di _esempio_ : `Link`, visualizzazione da aprire: `Cell Details`, etichetta collegamento: `Sample`
    2. Renderer della colonna _count_ : `Bar`, tavolozza colori: `Blue`, valore minimo: `0`
    3. Renderer della colonna _richiesta_ : `Automatic`
    4. Fare clic su _Salva e Chiudi_ per applicare le modifiche
8. Fare clic su uno dei collegamenti `Sample` nella griglia. Verrà aperto un riquadro delle proprietà con i dettagli di una richiesta campionata.

    ![Immagine che mostra la creazione di un'esperienza interattiva usando i clic della cella della griglia](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Azioni renderer di collegamento
| Azione di collegamento | Azione su clic |
|:------------- |:-------------|
| `Generic Details` | Mostra i valori di riga in un pannello del contesto della griglia delle proprietà |
| `Cell Details` | Mostra il valore della cella in un pannello del contesto della griglia delle proprietà. Utile quando la cella contiene un tipo dinamico con informazioni, ad esempio JSON con proprietà della richiesta come la posizione, l'istanza del ruolo e così via. |
| `Cell Details` | Mostra il valore della cella in un pannello del contesto della griglia delle proprietà. Utile quando la cella contiene un tipo dinamico con informazioni, ad esempio JSON con proprietà della richiesta come la posizione, l'istanza del ruolo e così via. |
| `Custom Event Details` | Apre i dettagli della ricerca di Application Insights con l'ID evento personalizzato (itemId) nella cella |
| `* Details` | Simile ai dettagli dell'evento personalizzato, ad eccezione delle dipendenze, delle eccezioni, delle visualizzazioni di pagina, delle richieste e delle tracce. |
| `Custom Event User Flows` | Apre il Application Insights Flussi utente esperienza trasformata tramite pivot per il nome dell'evento personalizzato nella cella |
| `* User Flows` | Simile a Flussi utente eventi personalizzati ad eccezione delle eccezioni, delle visualizzazioni pagina e delle richieste |
| `User Timeline` | Apre la sequenza temporale dell'utente con l'ID utente (user_Id) nella cella |
| `Session Timeline` | Apre l'esperienza di ricerca Application Insights per il valore nella cella, ad esempio ricercare il testo "ABC", dove ABC è il valore nella cella. |
| `Resource overview` | Aprire la panoramica della risorsa nel portale in base al valore di ID risorsa nella cella |

## <a name="conditional-visibility"></a>Visibilità condizionale
La cartella di lavoro consente agli utenti di far apparire o scomparire determinati controlli in base ai valori dei parametri. In questo modo gli autori possono avere un aspetto diverso in base all'input dell'utente o allo stato di telemetria. Un esempio mostra agli utenti solo un riepilogo quando le cose sono valide, ma visualizzano i dettagli completi quando si verifica un errore.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Impostazione dell'interattività tramite visibilità condizionale
1. Per configurare due controlli interattivi, attenersi alla procedura descritta nella sezione `Setting up interactivity on grid row click`.
2. Aggiungere un nuovo parametro nella parte superiore:
    1. Nome: `ShowDetails`
    2. Tipo di parametro: `Drop down`
    3. Obbligatorio: `checked`
    4. Recuperare i dati da: `JSON`
    5. Input JSON: `["Yes", "No"]`
    6. Salva per eseguire il commit delle modifiche.
3. Impostare il valore del parametro su `Yes`
4. Nel controllo query con il grafico ad area fare clic sull'icona _delle impostazioni avanzate_ (icona a forma di ingranaggio).
5. Controllare l'impostazione `Make this item conditionally visible`
    1. Questo elemento è visibile se `ShowDetails` valore del parametro `equals` `Yes`
6. Fare clic su _modifica completato_ per confermare le modifiche.
7. Fare clic su _modifica eseguita_ sulla barra degli strumenti della cartella di lavoro per attivare la modalità di lettura.
8. Impostare il valore del parametro `ShowDetails` su `No`. Si noti che il grafico seguente scompare.

L'immagine seguente mostra il caso visibile in cui `ShowDetails` è `Yes`

![Immagine che mostra la visibilità condizionale in cui è visibile il grafico](./media/workbooks-interactive/conditional-visibility.png)

L'immagine seguente mostra il caso nascosto in cui `ShowDetails` è `No`

![Immagine che mostra la visibilità condizionale in cui il grafico è nascosto](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Passaggi successivi


* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
