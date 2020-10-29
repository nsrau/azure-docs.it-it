---
title: Spostare un'istanza in un'altra area di Azure
titleSuffix: Azure Digital Twins
description: Vedere come spostare un'istanza di Azure Digital Twins da un'area di Azure a un'altra.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e586e9acc9510dc1aaae511fa51e5a0c3255bd8f
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026497"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Spostare un'istanza di Azure Digital Twins in un'altra area di Azure

Se è necessario spostare l'istanza di Azure Digital Twins da un'area a un'altra, il processo corrente consiste nel **ricreare le risorse nella nuova area** e quindi eliminare le risorse originali. Al termine di questo processo, si lavorerà con una nuova istanza di Azure Digital Twins identica alla prima, ad eccezione del percorso aggiornato.

Questo articolo fornisce indicazioni su come eseguire uno spostamento completo, copiando tutti gli elementi necessari per fare in modo che la nuova istanza corrisponda a quella originale.

Questo processo include i due passaggi seguenti:
1. Preparare: scaricare i modelli, i dispositivi gemelli e il grafo originali.
2. Move: creare una nuova istanza di Azure Digital gemelli, in una nuova area.
3. Move: ripopolare la nuova istanza di Azure Digital gemelli.
    - Caricare modelli originali, gemelli e Graph.
    - Ricreare gli endpoint e le route.
    - Collegare nuovamente le risorse connesse.
4. Pulisci risorse di origine: Elimina l'istanza originale.

## <a name="prerequisites"></a>Prerequisiti

Prima di provare a ricreare l'istanza di Azure Digital Twins, è consigliabile esaminare i componenti dell'istanza originale e ottenere un'idea chiara di tutti gli elementi che dovranno essere ricreati.

Di seguito sono riportate alcune domande che è possibile prendere in considerazione:
* Quali sono i **modelli** caricati nell'istanza? Quanti sono?
* Quali sono i dispositivi **gemelli** nell'istanza? Quanti sono?
* Qual è la forma generale del **grafico** nell'istanza? Quante relazioni sono disponibili?
* Quali **endpoint** sono disponibili nell'istanza?
* Quali **Route** sono disponibili nell'istanza? I filtri sono disponibili?
* Dove l'istanza **si connette ad altri servizi di Azure** ? Alcuni punti di integrazione comuni includono...
    - Griglia di eventi, Hub eventi o bus di servizio
    - Funzioni di Azure
    - App per la logica
    - Time Series Insights
    - Mappe di Azure
    - Servizio Device provisioning (DPS)
* Quali altre **app personali o aziendali** sono disponibili per connettersi all'istanza?

È possibile raccogliere queste informazioni usando il [portale di Azure](https://portal.azure.com), le [API e gli SDK di Azure Digital](how-to-use-apis-sdks.md)gemelli, i [comandi dell'interfaccia](how-to-use-cli.md)della riga di comando di Azure Digital gemelli o l'esempio di esplorazione di [Azure Digital Twins (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Preparazione

In questa sezione si prepara la ricreazione dell'istanza **scaricando i modelli, i dispositivi gemelli e il grafo originali** dall'istanza originale. Questo articolo usa l'esempio di [Esplora risorse di Azure Digital gemelli (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

>[!NOTE]
>È possibile che siano già presenti file contenenti i modelli e/o il grafico nell'istanza. In tal caso, non è necessario scaricare di nuovo tutti gli elementi, solo quelli mancanti o elementi che potrebbero essere stati modificati da quando sono stati caricati originariamente questi file, ad esempio i dispositivi gemelli che potrebbero essere stati aggiornati con nuovi dati.

### <a name="limitations-of-adt-explorer"></a>Limitazioni di ADT Explorer

L' [esempio di esplorazione di Azure Digital Twins (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) è un esempio di app client che supporta una rappresentazione visiva del grafo e fornisce l'interazione visiva con l'istanza. Questo articolo illustra come usarlo per scaricare e caricare nuovamente i modelli, i dispositivi gemelli e i grafici.

Si noti, tuttavia, che si tratta di un **esempio** e non di uno strumento completo. Il test non è stato testato e non è stato compilato per gestire i grafici di grandi dimensioni. Di conseguenza, tenere presenti le limitazioni di esempio seguenti:
* L'esempio è attualmente stato testato solo su dimensioni del grafo fino a 1000 nodi e 2000 relazioni
* L'esempio non supporta il nuovo tentativo in caso di errori intermittenti
* L'esempio non invierà necessariamente notifiche all'utente se i dati caricati sono incompleti
* L'esempio non gestisce gli errori derivanti da grafici di grandi dimensioni che superano le risorse disponibili, ad esempio la memoria

Se l'esempio non è in grado di gestire le dimensioni del grafo, è possibile esportare e importare il grafo con altri strumenti di sviluppo di Azure Digital Twins:
* [Comandi dell'interfaccia della riga di comando di Azure Digital Twins](how-to-use-cli.md)
* [SDK e API per i dispositivi digitali gemelli di Azure](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Configurare l'applicazione di esplorazione di ADT

Per procedere con ADT Explorer, scaricare prima di tutto il codice dell'applicazione di esempio e configurarlo per l'esecuzione nel computer. 

Passare all'esempio qui: [Esplora risorse di Azure Digital gemelli (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selezionare il pulsante *Scarica ZIP* per scaricare nel computer un file *ZIP* di questo codice di esempio con il nome _**Azure_Digital_Twins__ADT__explorer.zip**_ . Decomprimere i file.

Successivamente, configurare e configurare le autorizzazioni per ADT Explorer. A tale scopo, seguire le istruzioni riportate nella sezione [*set up Azure Digital gemells and ADT Explorer*](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) della Guida introduttiva a dispositivi digitali gemelli di Azure. Questa sezione illustra i passaggi seguenti:
1. Configurare un'istanza di dispositivi gemelli digitali di Azure (è possibile ignorare questa parte perché è già presente un'istanza)
2. Configurare una **registrazione dell'app Azure ad** per fornire l'accesso all'istanza
3. Configurare le autorizzazioni per l'esecuzione di ADT Explorer nel computer
4. Eseguire ADT Explorer e configurarlo per la connessione all'istanza di. Si userà il **nome host** dell'istanza di Azure Digital Twins originale che si sta muovendo e l'ID **client** e l' **ID tenant** dalla registrazione dell'app.

A questo punto è necessario che l'app di esempio ADT Explorer sia in esecuzione in un browser nel computer. L'esempio deve essere connesso all'istanza originale di Azure Digital gemelli.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/explorer-blank.png":::

Per verificare la connessione, è possibile fare clic sul pulsante *Esegui query* per eseguire la query predefinita che Visualizza tutti i gemelli e le relazioni nel grafico nella casella *Esplora grafico* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/run-query.png":::

È possibile lasciare la finestra di esplorazione di ADT in esecuzione, perché verrà usata di nuovo più avanti in questo articolo per caricare nuovamente questi elementi nella nuova istanza nell'area di destinazione.

### <a name="download-models-twins-and-graph"></a>Scarica modelli, gemelli e Graph

Scaricare quindi i modelli, i dispositivi gemelli e il grafo della soluzione nel computer.

Per scaricare tutti questi in una sola volta, assicurarsi che il grafico completo venga visualizzato nella casella di *visualizzazione grafico* . a tale scopo, è possibile rieseguire la query predefinita `SELECT * FROM digitaltwins` nella casella *Esplora query* .
 
Quindi, fare clic sull'icona *Esporta grafico* nella casella di *visualizzazione grafico* .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/export-graph.png":::

In questo modo verrà abilitato un collegamento di *download* nella *visualizzazione grafico* . Selezionarlo per scaricare una rappresentazione basata su JSON del risultato della query, inclusi i modelli, i gemelli e le relazioni. Questa operazione dovrebbe scaricare un file con *estensione JSON* nel computer.

>[!NOTE]
>Se il file scaricato sembra avere un'estensione di file diversa, provare a modificare l'estensione direttamente e a modificarla in *JSON* .

## <a name="move"></a>Spostamento

Successivamente, si completerà lo "spostamento" dell'istanza creando una nuova istanza nell'area di destinazione e inserendola con i dati e i componenti dell'istanza originale.

### <a name="create-a-new-instance"></a>Crea una nuova istanza

Per prima cosa, **creare una nuova istanza di Azure Digital gemelli nell'area di destinazione** . A tale scopo, seguire la procedura descritta in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md), tenendo conto di questi puntatori:
* **Se** si trova in un gruppo di risorse diverso, è possibile usare lo stesso nome per la nuova istanza. Se è necessario usare lo stesso gruppo di risorse che contiene l'istanza originale, la nuova istanza dovrà avere un proprio nome distinto.
* Quando viene richiesta una località, immettere la nuova area di destinazione.

Al termine di questa operazione, sarà necessario il **nome host** della nuova istanza per continuare a impostarla con i dati. Se questa operazione non è stata annotata durante l'installazione, è possibile seguire [queste istruzioni](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) per ottenere ora dal portale di Azure.

### <a name="repopulate-old-instance"></a>Ripopola istanza precedente

Si procederà quindi alla configurazione della nuova istanza in modo che sia una copia del originale.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Caricare modelli originali, gemelli e Graph usando ADT Explorer

In questa sezione è possibile caricare nuovamente i modelli, i dispositivi gemelli e Graph nella nuova istanza. Se non sono presenti modelli, gemelli o grafici nell'istanza originale oppure non si vuole spostarli nella nuova istanza, è possibile passare alla [sezione successiva](#recreate-endpoints-and-routes).

In caso contrario, per continuare, tornare alla finestra del browser in cui è in esecuzione **ADT Explorer** e attenersi alla procedura riportata di seguito.

##### <a name="connect-to-the-new-instance"></a>Connetti alla nuova istanza

Attualmente, ADT Explorer è connesso all'istanza originale di Azure Digital gemelli. Impostare la connessione in modo che punti alla nuova istanza facendo clic sul pulsante *Accedi* nella parte superiore della finestra. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/sign-in.png":::

È possibile riutilizzare la stessa registrazione dell'app, quindi è sufficiente sostituire l' *URL ADT* per riflettere la nuova istanza. Modificare questo valore in modo che legga *https://{new instance hostname}* .

Premere *Connetti* . Potrebbe essere richiesto di eseguire di nuovo l'accesso con le credenziali di Azure e/o concedere il consenso dell'applicazione per l'istanza.

##### <a name="upload-models-twins-and-graph"></a>Caricare modelli, gemelli e Graph

Caricare quindi i componenti della soluzione scaricati in precedenza nella nuova istanza.

Per caricare i **modelli, i dispositivi gemelli e il grafo** , fare clic sull'icona *Importa grafico* nella casella di *visualizzazione grafico* . Questa opzione consente di caricare contemporaneamente tutti e tre i componenti (anche quelli non attualmente in uso nel grafico).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/import-graph.png":::

Nella casella Selettore file passare al grafico scaricato. Selezionare il file Graph *. JSON* e fare clic su *Apri* .

Dopo alcuni secondi ADT Explorer aprirà una visualizzazione *Import* che mostra un'anteprima del grafo che verrà caricato.

Per confermare il caricamento del grafico, selezionare l'icona *Save* (Salva) nell'angolo superiore destro del riquadro *GRAPH VIEW* (Visualizzazione grafo):

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer caricherà ora i modelli e il grafo (inclusi i gemelli e le relazioni) per la nuova istanza di Azure Digital gemelli. Verrà visualizzato un messaggio di operazione riuscita che rileva il numero di modelli, gemelli e relazioni caricati:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Per verificare che tutto sia stato caricato correttamente, fare clic sul pulsante *Esegui query* nella casella *Esplora grafico* per eseguire la query predefinita che Visualizza tutti i dispositivi gemelli e le relazioni nel grafico. Verrà inoltre aggiornato l'elenco dei modelli nella *vista modello* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/run-query.png":::

Il grafo dovrebbe essere visualizzato con tutti i gemelli e le relazioni visualizzate nella casella *Esplora grafico* . È anche possibile visualizzare i modelli elencati nella casella *vista modello* .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/post-upload.png":::

In questo modo viene confermato che i modelli, i dispositivi gemelli e i grafici sono stati caricati nuovamente nella nuova istanza nell'area di destinazione.

#### <a name="recreate-endpoints-and-routes"></a>Ricreare endpoint e Route

Se si dispone di **endpoint e/o route** nell'istanza originale, è necessario ricrearli nella nuova istanza. Se non sono presenti endpoint o route nell'istanza originale o non si vuole spostarli nella nuova istanza, è possibile passare alla [sezione successiva](#re-link-connected-resources).

In caso contrario, procedere, attenersi alla procedura descritta in [*How-to: Manage Endpoints and routes*](how-to-manage-routes-portal.md) using the new instance, tenendo conto di questi puntatori: 
* Non è **necessario** ricreare la griglia di eventi, l'hub eventi o la risorsa del bus di servizio che si sta usando per l'endpoint (sezioni dei *prerequisiti* nelle istruzioni dell'endpoint). È sufficiente creare nuovamente l'endpoint nell'istanza di Azure Digital Twins.
* È possibile riutilizzare gli endpoint e **i nomi** di route, perché hanno come ambito un'istanza diversa.
* Ricordarsi di aggiungere tutti i **filtri** necessari alle route create.

#### <a name="re-link-connected-resources"></a>Collega di nuovo le risorse connesse

Se si dispone di altre app o risorse di Azure connesse all'istanza originale di Azure Digital gemelli, sarà necessario modificare la connessione in modo che raggiunga invece la nuova istanza. Questo può includere altri servizi di Azure o app personali o aziendali configurate per l'uso con i dispositivi gemelli digitali di Azure.

Se non sono presenti altre risorse connesse all'istanza originale o non si vuole spostarle nella nuova istanza, è possibile passare alla [sezione successiva](#verify).

In caso contrario, per continuare, prendere in considerazione le risorse connesse nello scenario. Non è necessario eliminare e ricreare le risorse connesse. al contrario, è sufficiente modificare i punti in cui si connettono a un'istanza di Azure Digital Twins tramite il **nome host** e aggiornarli in modo da usare il nome host della nuova istanza anziché l'originale.

Le risorse esatte che è necessario modificare dipendono dallo scenario, ma di seguito sono riportati alcuni punti di integrazione comuni:
* Funzioni di Azure. Se si dispone di una funzione di Azure il cui codice include il nome host dell'istanza originale, è necessario aggiornare questo valore al nome host della nuova istanza e pubblicare nuovamente la funzione.
* Griglia di eventi, Hub eventi o bus di servizio
* App per la logica
* Time Series Insights
* Mappe di Azure
* Servizio Device provisioning (DPS)
* App personali o aziendali esterne ad Azure, ad esempio l' **app client** creata in [*esercitazione: scrivere codice per un'app client*](tutorial-code.md), che si connette all'istanza e chiama le API di Azure Digital gemelli

Al termine di questo passaggio, la nuova istanza nell'area di destinazione deve essere una copia dell'istanza originale.

## <a name="verify"></a>Verifica

Per verificare che la nuova istanza sia stata configurata correttamente, è possibile usare gli strumenti seguenti:
* Il [**portale di Azure**](https://portal.azure.com) (valido per verificare che la nuova istanza esista e si trovi nell'area di destinazione corretta; anche per verificare gli endpoint e le route e le connessioni ad altri servizi di Azure)
* I comandi dell'interfaccia della riga di [ **comando** di Azure Digital gemelli](how-to-use-cli.md) (validi per verificare che la nuova istanza esista e si trovi nell'area di destinazione corretta; può essere usata anche per verificare i dati dell'istanza)
* [**Esplora ADT**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (ideale per la verifica dei dati dell'istanza, ad esempio modelli, gemelli e grafi)
* [API e SDK per i dispositivi digitali gemelli di Azure](how-to-use-apis-sdks.md) (ideali per la verifica dei dati dell'istanza, ad esempio modelli, gemelli e Graph; anche per verificare gli endpoint e le route)

È anche possibile provare a eseguire eventuali app personalizzate o flussi end-to-end eseguiti con l'istanza originale, per consentire di verificare che funzionino correttamente con la nuova istanza.

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Ora che la nuova istanza è configurata nell'area di destinazione con una copia dei dati e delle connessioni dell'istanza originale, è possibile **eliminare l'istanza originale** .

Questa operazione può essere eseguita nel [portale di Azure](https://portal.azure.com), con l' [interfaccia](how-to-use-cli.md)della riga di comando o con le [API del piano di controllo](how-to-use-apis-sdks.md#overview-control-plane-apis).

Per eliminare l'istanza usando il portale di Azure, [aprire il portale](https://portal.azure.com) in una finestra del browser e passare all'istanza di Azure Digital Twins originale cercando il relativo nome nella barra di ricerca del portale.

Premere il pulsante *Elimina* e seguire le istruzioni per completare l'eliminazione.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo.":::