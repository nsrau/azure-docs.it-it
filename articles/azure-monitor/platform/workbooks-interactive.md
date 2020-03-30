---
title: Cartelle di lavoro di Monitoraggio di Azure con parametri personalizzatiAzure Monitor workbooks with custom parameters
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658269"
---
# <a name="interactive-workbooks"></a>Cartelle di lavoro interattive

Le cartelle di lavoro consentono agli autori di creare report ed esperienze interattive per i consumatori. L'interattività è supportata in diversi modi.

## <a name="parameter-changes"></a>Modifiche ai parametri
Quando un utente di una cartella di lavoro aggiorna un parametro, qualsiasi controllo che utilizza il parametro viene aggiornato e ridisegnato automaticamente per riflettere il nuovo stato. Questo è il modo in cui la maggior parte dei report del portale di Azure supporta l'interattività. Le cartelle di lavoro forniscono questo in modo molto semplice con il minimo sforzo da parte dell'utente.

Ulteriori informazioni sui [parametri nelle cartelle di lavoroLearn](workbooks-parameters.md) more about Parameters in Workbooks

## <a name="grid-row-clicks"></a>Clic sulla riga della griglia
Le cartelle di lavoro consentono agli autori di creare scenari in cui facendo clic su una riga in una griglia i grafici successivi vengono aggiornati in base al contenuto della riga. 

Ad esempio, un utente può avere una griglia che mostra un elenco di richieste e alcune statistiche come i conteggi degli errori. Essi potrebbero impostare in modo tale che facendo clic su una riga corrispondente a una richiesta, si tradurrà in grafici dettagliati sotto l'aggiornamento per filtrare verso il basso per solo quella richiesta.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Impostazione dell'interattività nella griglia clic
1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento _modifica_ della barra degli strumenti.
2. Utilizzare il collegamento _Aggiungi query_ per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come _Log_, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per accedere al KQL per l'analisi
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`per vedere i risultati
6. Fare clic sull'icona _Impostazioni avanzate_ nel piè di pagina della query (l'icona ha l'aspetto di un ingranaggio). Si apre il riquadro delle impostazioni avanzate 
7. Controllare l'impostazione:`When an item is selected, export a parameter`
    1. Campo da esportare:`Request`
    2. Nome parametro:`SelectedRequest`
    3. Valore predefinito: `All requests`
    
    ![Immagine che mostra l'editor avanzato con le impostazioni per l'esportazione dei campi come parametri](./media/workbooks-interactive/advanced-settings.png)

8. Fare clic su `Done Editing`.
9. Aggiungere un altro controllo query utilizzando i passaggi 2 e 3.Add another query control using steps 2 and 3.
10. Utilizzare l'editor di query per accedere al KQL per l'analisi
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`per vedere i risultati.
12. Cambia _visualizzazione_ in`Area chart`
12. Fare clic su una riga nella prima griglia. Si noti come il grafico ad area sottostante filtra la richiesta selezionata.

Il report risultante è simile al seguente in modalità di modifica:The resulting report looks like this in edit mode:

![Immagine che mostra la creazione di un'esperienza interattiva con i clic sulle righe della griglia](./media/workbooks-interactive//grid-click-create.png)

L'immagine seguente mostra un report interattivo più elaborato in modalità di lettura basato sugli stessi principi. Il report utilizza i clic della griglia per esportare i parametri, che a loro volta vengono utilizzati in due grafici e un blocco di testo.

![Immagine che mostra la creazione di un'esperienza interattiva con i clic sulle righe della griglia](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Esportazione del contenuto di un'intera riga
A volte è opportuno esportare l'intero contenuto della riga selezionata anziché solo una colonna specifica. In questi casi, `Field to export` lasciare la proprietà non impostata nel passaggio 7.1 precedente. Le cartelle di lavoro esporterà l'intero contenuto della riga come json nel parametro . 

Nel controllo KQL di riferimento, `todynamic` utilizzare la funzione per analizzare il json e accedere alle singole colonne.

 ## <a name="grid-cell-clicks"></a>Clic sulle celle della griglia
Le cartelle di lavoro consentono agli autori di aggiungere interattività tramite un tipo speciale di renderer di colonne della griglia denominato oggetto . `link renderer` Un renderer di collegamento converte una cella della griglia in un collegamento ipertestuale in base al contenuto della cella. Le cartelle di lavoro supportano molti tipi di renderer di collegamenti, inclusi quelli che consentono di aprire blade di panoramica delle risorse, visualizzatori di contenitori di proprietà, ricerca di App Insights, utilizzo, traccia delle transazioni e così via.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Impostazione dell'interattività mediante i clic sulle celle della griglia
1. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento _modifica_ della barra degli strumenti.
2. Utilizzare il collegamento _Aggiungi query_ per aggiungere un controllo query di log alla cartella di lavoro. 
3. Selezionare il tipo di query come _Log_, tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per accedere al KQL per l'analisi
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`per vedere i risultati
6. Fare clic su _Impostazioni colonna_ per aprire il riquadro delle impostazioni.
7. Nella sezione _Colonne_ impostare:
    1. _Esempio_ - Rendering `Link`colonna: , `Cell Details`Visualizza per aprire: , Etichetta collegamento:`Sample`
    2. _Conteggio_ - Rendering `Bar`colonne: , `Blue`Tavolozza colori: , Valore minimo:`0`
    3. _Richiesta_ - Rendering colonne:`Automatic`
    4. Fare clic su _Salva e chiudi_ per applicare le modifiche
8. Fare clic su `Sample` uno dei collegamenti nella griglia. Verrà aperto un riquadro delle proprietà con i dettagli di una richiesta campionata.

    ![Immagine che mostra la creazione di un'esperienza interattiva con i clic sulle celle della griglia](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Collega azioni del renderer
| Azione di collegamento | Azione al clic |
|:------------- |:-------------|
| `Generic Details` | Mostra i valori di riga in un pannello di contesto della griglia delle proprietà |
| `Cell Details` | Visualizza il valore della cella in un pannello di contesto della griglia delle proprietà. Utile quando la cella contiene un tipo dinamico con informazioni (ad esempio, json con proprietà di richiesta come posizione, istanza del ruolo e così via). |
| `Cell Details` | Visualizza il valore della cella in un pannello di contesto della griglia delle proprietà. Utile quando la cella contiene un tipo dinamico con informazioni (ad esempio, json con proprietà di richiesta come posizione, istanza del ruolo e così via). |
| `Custom Event Details` | Apre i dettagli della ricerca di Application Insights con l'ID evento personalizzato (itemId) nella cella |
| `* Details` | Simile a Dettagli evento personalizzati, ad eccezione di dipendenze, eccezioni, visualizzazioni di pagina, richieste e tracce. |
| `Custom Event User Flows` | Apre l'esperienza flussi utente di Application Insights sottoposta pivot sul nome dell'evento personalizzato nella cella |
| `* User Flows` | Simile a Flussi utente eventi personalizzati ad eccezione di eccezioni, visualizzazioni di pagina e richieste |
| `User Timeline` | Apre la sequenza temporale dell'utente con l'ID utente (user_Id) nella cella |
| `Session Timeline` | Apre l'esperienza di ricerca di Application Insights per il valore nella cella (ad esempio, cercare il testo 'abc' dove abc è il valore nella cella) |
| `Resource overview` | Aprire la panoramica della risorsa nel portale in base al valore dell'ID risorsa nella cella |

## <a name="conditional-visibility"></a>Visibilità condizionale
La cartella di lavoro consente agli utenti di visualizzare o scomparire determinati controlli in base ai valori dei parametri. Ciò consente agli autori di avere report diversi in base all'input dell'utente o allo stato di telemetria. Un esempio è mostrare ai consumatori solo un riepilogo quando le cose sono buone, ma mostra tutti i dettagli quando qualcosa non va.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Impostazione dell'interattività mediante la visibilità condizionale
1. Seguire i passaggi `Setting up interactivity on grid row click` nella sezione per impostare due controlli interattivi.
2. Aggiungere un nuovo parametro nella parte superiore:
    1. Nome: `ShowDetails`
    2. Tipo di parametro:`Drop down`
    3. Obbligatorio:`checked`
    4. Ottenere dati da:`JSON`
    5. Input JSON:`["Yes", "No"]`
    6. Salva per eseguire il commit delle modifiche.
3. Impostare il valore del parametro su`Yes`
4. Nel controllo query con il grafico ad area, fare clic sull'icona _Impostazioni avanzate_ (icona a forma di ingranaggio)
5. Controllare l'impostazione`Make this item conditionally visible`
    1. Questo elemento è `ShowDetails` visibile `equals` se il valore del parametro`Yes`
6. Fare clic su _Modifica completata_ per eseguire il commit delle modifiche.
7. Fare clic su _Modifica completata_ sulla barra degli strumenti della cartella di lavoro per attivare la modalità di lettura.
8. Impostare il `ShowDetails` valore `No`del parametro su . Si noti che la tabella seguente scompare.

L'immagine seguente mostra `ShowDetails` il caso visibile in cui si trova`Yes`

![Immagine che mostra la visibilità condizionale in cui il grafico è visibile](./media/workbooks-interactive/conditional-visibility.png)

L'immagine qui sotto `ShowDetails` mostra il caso nascosto dove si trova`No`

![Immagine che mostra la visibilità condizionale in cui è nascosto il grafico](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Passaggi successivi


* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
