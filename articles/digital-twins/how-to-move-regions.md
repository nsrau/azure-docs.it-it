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
ms.openlocfilehash: cb532098cda290654d6bdebe9cec2edab8ccbf99
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355788"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Spostare un'istanza di Azure Digital Twins in un'altra area di Azure

Se è necessario spostare l'istanza di Azure Digital Twins da un'area a un'altra, il processo corrente consiste nel ricreare le risorse nella nuova area e quindi eliminare le risorse originali. Al termine di questo processo, si lavorerà con una nuova istanza di Azure Digital Twins identica alla prima, ad eccezione del percorso aggiornato.

Questo articolo fornisce indicazioni su come eseguire uno spostamento completo e copiare tutti gli elementi necessari per fare in modo che la nuova istanza corrisponda a quella originale.

Questo processo include i due passaggi seguenti:

1. Preparare: scaricare i modelli, i dispositivi gemelli e il grafo originali.
1. Move: creare una nuova istanza di Azure Digital Twins in una nuova area.
1. Move: ripopolare la nuova istanza di Azure Digital gemelli.
    - Caricare i modelli, i gemelli e il grafo originali.
    - Ricrea gli endpoint e le route.
    - Ricollegare le risorse connesse.
1. Pulisci risorse di origine: Elimina l'istanza originale.

## <a name="prerequisites"></a>Prerequisiti

Prima di provare a ricreare l'istanza di Azure Digital Twins, esaminare i componenti dell'istanza originale per ottenere un'idea chiara di tutti gli elementi che dovranno essere ricreati.

Ecco alcune domande da considerare:

* Quali sono i *modelli* caricati nell'istanza? Quanti sono?
* Quali sono i dispositivi *gemelli* nell'istanza? Quanti sono?
* Qual è la forma generale del *grafico* nell'istanza? Quante relazioni sono disponibili?
* Quali *endpoint* sono disponibili nell'istanza?
* Quali *Route* sono disponibili nell'istanza? I filtri sono disponibili?
* Dove l'istanza *si connette ad altri servizi di Azure* ? Alcuni punti di integrazione comuni includono:

    - Griglia di eventi di Azure, Hub eventi di Azure o bus di servizio di Azure
    - Funzioni di Azure
    - App per la logica di Azure
    - Azure Time Series Insights
    - Mappe di Azure
    - Servizio Device Provisioning in hub IoT di Azure
* Quali altre *app personali o aziendali* sono disponibili per connettersi all'istanza?

È possibile raccogliere queste informazioni usando il [portale di Azure](https://portal.azure.com), gli [SDK e le API dei dispositivi digitali gemelli di Azure](how-to-use-apis-sdks.md), i comandi dell'interfaccia della riga di [comando](how-to-use-cli.md)di Azure Digital gemelli o l'esempio di [esplorazione di Azure Digital Twins (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Preparazione

In questa sezione si prepara la ricreazione dell'istanza scaricando i modelli originali, i dispositivi gemelli e il grafo dall'istanza originale. Questo articolo usa l'esempio di [esplorazione di ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) per questa attività.

>[!NOTE]
>È possibile che siano già presenti file che contengono i modelli o il grafico nell'istanza. In tal caso, non è necessario scaricare di nuovo tutti gli elementi, ovvero solo quelli mancanti o elementi che potrebbero essere stati modificati da quando sono stati caricati originariamente questi file. Si potrebbero ad esempio avere dispositivi gemelli che sono stati aggiornati con nuovi dati.

### <a name="limitations-of-adt-explorer"></a>Limitazioni di ADT Explorer

L' [esempio di ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) è un esempio di app client che supporta una rappresentazione visiva del grafo e fornisce interazioni visive con l'istanza. Questo articolo illustra come usarlo per scaricare e successivamente ricaricare i modelli, i dispositivi gemelli e i grafici.

Questo esempio non è uno strumento completo. Il test non è stato testato e non è stato creato per gestire i grafici di grandi dimensioni. Di conseguenza, tenere presenti le limitazioni di esempio seguenti:

* L'esempio è attualmente stato testato solo su dimensioni del grafo fino a 1.000 nodi e 2.000 relazioni.
* L'esempio non supporta il nuovo tentativo in caso di errori intermittenti.
* L'esempio non invierà necessariamente notifiche all'utente se i dati caricati sono incompleti.
* L'esempio non gestisce gli errori derivanti da grafici di grandi dimensioni che superano le risorse disponibili, ad esempio la memoria.

Se l'esempio non è in grado di gestire le dimensioni del grafo, è possibile esportare e importare il grafo usando altri strumenti di sviluppo di Azure Digital Twins:

* [Comandi dell'interfaccia della riga di comando di Azure Digital Twins](how-to-use-cli.md)
* [SDK e API per i dispositivi digitali gemelli di Azure](how-to-use-apis-sdks.md)

### <a name="set-up-the-adt-explorer-application"></a>Configurare l'applicazione ADT Explorer

Per procedere con ADT Explorer, scaricare prima di tutto il codice dell'applicazione di esempio e configurarlo per l'esecuzione nel computer.

Per ottenere l'esempio, vedere [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selezionare il pulsante **Scarica zip** per scaricare un file zip di questo codice di esempio nel computer come **Azure_Digital_Twins__ADT__explorer.zip**. Decomprimere i file.

Successivamente, configurare e configurare le autorizzazioni per ADT Explorer. Seguire le istruzioni riportate nella sezione [set up Azure Digital gemells and ADT Explorer](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) della Guida introduttiva a dispositivi digitali gemelli di Azure. Questa sezione illustra i passaggi seguenti:

1. Configurare un'istanza di dispositivi gemelli digitali di Azure. È possibile ignorare questa parte perché è già presente un'istanza.
1. Configurare le credenziali di Azure locali per consentire l'accesso all'istanza di.
1. Eseguire ADT Explorer e configurarlo per la connessione all'istanza di. Si userà il *nome host* dell'istanza di Azure Digital Twins originale che si sta muovendo.

A questo punto è necessario che l'app di esempio ADT Explorer sia in esecuzione in un browser nel computer. L'esempio deve essere connesso all'istanza originale di Azure Digital gemelli.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Finestra del browser che mostra un'app in esecuzione in localhost: 3000. L'app è denominata ADT Explorer e contiene le caselle per Esplora query, visualizzazione modello, visualizzazione grafico ed Esplora proprietà. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/explorer-blank.png":::

Per verificare la connessione, selezionare il pulsante **Esegui query** per eseguire la query predefinita che Visualizza tutti i gemelli e le relazioni nel grafico nella casella **Esplora grafico** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Viene evidenziata una query di esecuzione per la lettura di un pulsante nell'angolo superiore destro della finestra." lightbox="media/how-to-move-regions/run-query.png":::

È possibile uscire da ADT Explorer in esecuzione perché verrà usato di nuovo più avanti in questo articolo per ricaricare questi elementi nella nuova istanza nell'area di destinazione.

### <a name="download-models-twins-and-graph"></a>Scarica modelli, gemelli e Graph

Scaricare quindi i modelli, i dispositivi gemelli e il grafo della soluzione nel computer.

Per scaricare tutti questi elementi contemporaneamente, assicurarsi che il grafo completo venga visualizzato nella casella **visualizzazione grafico** . Se il grafo completo non è già visualizzato, eseguire di nuovo la query predefinita di `SELECT * FROM digitaltwins` nella casella **Esplora query** .
 
Selezionare quindi l'icona **Esporta grafico** nella casella di **visualizzazione grafico** .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Nella casella visualizzazione grafico viene evidenziata un'icona. Mostra una freccia rivolta verso il basso da un cloud." lightbox="media/how-to-move-regions/export-graph.png":::

Questa azione Abilita un collegamento di **download** nella casella di **visualizzazione grafico** . Selezionarlo per scaricare una rappresentazione basata su JSON del risultato della query, che include modelli, gemelli e relazioni. Questa azione dovrebbe scaricare un file con estensione JSON nel computer.

>[!NOTE]
>Se il file scaricato sembra avere un'estensione di file diversa, provare a modificare l'estensione direttamente e a modificarla in JSON.

## <a name="move"></a>Spostamento

Successivamente, si completerà lo "spostamento" dell'istanza creando una nuova istanza nell'area di destinazione. Quindi verrà popolato con i dati e i componenti dell'istanza originale.

### <a name="create-a-new-instance"></a>Crea una nuova istanza

Per prima cosa, creare una nuova istanza di Azure Digital gemelli nell'area di destinazione. Eseguire la procedura descritta in [configurare un'istanza di e l'autenticazione](how-to-set-up-instance-portal.md)di. Tenere presente questi indicatori:

* *Se* si trova in un gruppo di risorse diverso, è possibile usare lo stesso nome per la nuova istanza. Se è necessario usare lo stesso gruppo di risorse che contiene l'istanza originale, la nuova istanza dovrà avere un proprio nome distinto.
* Quando viene richiesta una località, immettere la nuova area di destinazione.

Al termine di questo passaggio, sarà necessario il nome host della nuova istanza per continuare a configurare i dati. Se il nome host non è stato annotato durante l'installazione, seguire [queste istruzioni](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) per ottenere ora dal portale di Azure.

### <a name="repopulate-the-old-instance"></a>Ripopolare l'istanza precedente

Successivamente, si imposterà la nuova istanza in modo che sia una copia del originale.

#### <a name="upload-the-original-models-twins-and-graph-by-using-adt-explorer"></a>Caricare i modelli, i gemelli e il grafo originali usando ADT Explorer

In questa sezione è possibile ricaricare i modelli, i dispositivi gemelli e Graph nella nuova istanza. Se non sono presenti modelli, gemelli o grafici nell'istanza originale oppure non si vuole spostarli nella nuova istanza, è possibile passare alla [sezione successiva](#re-create-endpoints-and-routes).

In caso contrario, tornare alla finestra del browser in cui è in esecuzione ADT Explorer e attenersi alla seguente procedura.

##### <a name="connect-to-the-new-instance"></a>Connetti alla nuova istanza

Attualmente, ADT Explorer è connesso all'istanza originale di Azure Digital gemelli. Impostare la connessione in modo che punti alla nuova istanza selezionando il pulsante **Accedi** nell'angolo superiore destro della finestra.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Esplora ADT che evidenzia l'icona di accesso nell'angolo superiore destro della finestra. L'icona Mostra una silhouette semplice di una persona sovrapposta a una silhouette di una chiave." lightbox="media/how-to-move-regions/sign-in.png":::

Sostituire l' **URL ADT** per riflettere la nuova istanza. Modificare questo valore in modo che legga *https://{new instance host name}*.

Selezionare **Connetti**. Potrebbe essere richiesto di eseguire di nuovo l'accesso con le credenziali di Azure o concedere al consenso dell'applicazione per l'istanza.

##### <a name="upload-models-twins-and-graph"></a>Caricare modelli, gemelli e Graph

Caricare quindi i componenti della soluzione scaricati in precedenza nella nuova istanza.

Per caricare i modelli, i dispositivi gemelli e il grafo, selezionare l'icona **Importa grafico** nella casella di **visualizzazione grafico** . Questa opzione consente di caricare contemporaneamente tutti e tre i componenti. Carica anche i modelli che non sono attualmente in uso nel grafico.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Nel riquadro Graph View è evidenziata un'icona. Mostra una freccia che punta verso l'alto in una nuvola." lightbox="media/how-to-move-regions/import-graph.png":::

Nella casella Selettore file passare al grafico scaricato. Selezionare il file Graph **. JSON** e selezionare **Apri**.

Dopo alcuni secondi, in ADT Explorer viene aperta una visualizzazione di **importazione** che mostra un'anteprima del grafo da caricare.

Per confermare il caricamento del grafico, selezionare l'icona **Salva** nell'angolo in alto a destra della casella di **visualizzazione grafico** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Evidenziazione dell'icona Salva nel riquadro di anteprima grafico." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer carica ora i modelli e il grafo (inclusi i gemelli e le relazioni) per la nuova istanza di Azure Digital gemelli. Verrà visualizzato un messaggio di operazione riuscita che nota il numero di modelli, gemelli e relazioni caricati.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Finestra di dialogo che indica la riuscita dell'importazione del grafico. Indica che l'importazione è riuscita. 2 modelli importati. 4 gemelli importati. 2 relazioni importate." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Per verificare che tutto sia stato caricato correttamente, selezionare il pulsante **Esegui query** nella casella **Esplora grafico** per eseguire la query predefinita che Visualizza tutti i dispositivi gemelli e le relazioni nel grafico. Questa azione consente inoltre di aggiornare l'elenco dei modelli nella casella **vista modello** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Evidenziare intorno al pulsante Esegui query nell'angolo superiore destro della finestra." lightbox="media/how-to-move-regions/run-query.png":::

Il grafo dovrebbe essere visualizzato con tutti i gemelli e le relazioni visualizzate nella casella **Esplora grafico** . È anche possibile visualizzare i modelli elencati nella casella **vista modello** .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Visualizzazione di ADT Explorer che mostra due modelli evidenziati nella casella di visualizzazione modello e un grafico evidenziato nella casella Esplora grafico." lightbox="media/how-to-move-regions/post-upload.png":::

Queste visualizzazioni confermano che i modelli, i gemelli e il grafo sono stati caricati nuovamente nella nuova istanza nell'area di destinazione.

#### <a name="re-create-endpoints-and-routes"></a>Ricrea endpoint e Route

Se nell'istanza originale sono presenti endpoint o route, sarà necessario ricrearli nella nuova istanza. Se non sono presenti endpoint o route nell'istanza originale o non si vuole spostarli nella nuova istanza, è possibile passare alla [sezione successiva](#relink-connected-resources).

In caso contrario, seguire la procedura descritta in [How-to: Manage Endpoints and routes](how-to-manage-routes-portal.md) using the new instance. Tenere presente questi indicatori:

* *Non* è necessario ricreare la griglia di eventi, gli hub eventi o la risorsa del bus di servizio che si sta usando per l'endpoint. Per ulteriori informazioni, vedere la sezione "Prerequisiti" nelle istruzioni per l'endpoint. È sufficiente creare nuovamente l'endpoint nell'istanza di Azure Digital Twins.
* È possibile riutilizzare gli endpoint e i nomi di route in quanto hanno come ambito un'istanza diversa.
* Ricordarsi di aggiungere tutti i filtri necessari alle route create.

#### <a name="relink-connected-resources"></a>Ricollegare le risorse connesse

Se si dispone di altre app o risorse di Azure connesse all'istanza originale di Azure Digital gemelli, sarà necessario modificare la connessione in modo che raggiunga invece la nuova istanza. Queste risorse possono includere altri servizi di Azure o app personali o aziendali configurati per l'uso con i dispositivi gemelli digitali di Azure.

Se non sono presenti altre risorse connesse all'istanza originale o non si vuole spostarle nella nuova istanza, è possibile passare alla [sezione successiva](#verify).

In caso contrario, prendere in considerazione le risorse connesse nello scenario. Non è necessario eliminare e ricreare le risorse connesse. Al contrario, è sufficiente modificare i punti in cui si connettono a un'istanza di Azure Digital Twins tramite il nome host. Si aggiornano quindi questi punti per usare il nome host della nuova istanza anziché l'originale.

Le risorse esatte che è necessario modificare dipendono dallo scenario, ma di seguito sono riportati alcuni punti di integrazione comuni:

* Funzioni di Azure. Se si dispone di una funzione di Azure il cui codice include il nome host dell'istanza originale, è necessario aggiornare questo valore al nome host della nuova istanza e ripubblicare la funzione.
* Griglia di eventi, Hub eventi o bus di servizio.
* App per la logica.
* Time Series Insights.
* Mappe di Azure.
* Servizio Device provisioning in hub Internet.
* App personali o aziendali esterne ad Azure, ad esempio l'app client creata in [esercitazione: scrivere codice per un'app client](tutorial-code.md), che si connette all'istanza e chiama le API di Azure Digital gemelli.
* *Non* è necessario ricreare le registrazioni dell'app Azure ad. Se si usa una [registrazione dell'app](how-to-create-app-registration.md) per connettersi alle API dei dispositivi gemelli digitali di Azure, è possibile riusare la stessa registrazione dell'app con la nuova istanza.

Al termine di questo passaggio, la nuova istanza nell'area di destinazione deve essere una copia dell'istanza originale.

## <a name="verify"></a>Verifica

Per verificare che la nuova istanza sia stata configurata correttamente, usare gli strumenti seguenti:

* [Portale di Azure](https://portal.azure.com). Il portale è adatto per verificare che la nuova istanza esista e si trovi nell'area di destinazione corretta. È anche opportuno verificare gli endpoint e le route e le connessioni ad altri servizi di Azure.
* Comandi dell'interfaccia della riga di [comando di Azure Digital Twins](how-to-use-cli.md). Questi comandi sono utili per verificare che la nuova istanza esista e si trovi nell'area di destinazione corretta. Possono inoltre essere utilizzati per verificare i dati dell'istanza.
* [Esplora ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). ADT Explorer è adatto per la verifica dei dati dell'istanza, ad esempio modelli, gemelli e grafici.
* [SDK e API per i dispositivi digitali gemelli di Azure](how-to-use-apis-sdks.md). Queste risorse sono utili per la verifica dei dati dell'istanza, ad esempio modelli, gemelli e grafici. Sono utili anche per la verifica di endpoint e route.

È anche possibile provare a eseguire eventuali app personalizzate o flussi end-to-end che sono stati eseguiti con l'istanza originale per verificare che funzionino correttamente con la nuova istanza.

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Ora che la nuova istanza è configurata nell'area di destinazione con una copia dei dati e delle connessioni dell'istanza originale, è possibile eliminare l'istanza originale.

È possibile usare l' [portale di Azure](https://portal.azure.com), l' [interfaccia](how-to-use-cli.md)della riga di comando di Azure o le [API del piano di controllo](how-to-use-apis-sdks.md#overview-control-plane-apis).

Per eliminare l'istanza usando il portale di Azure, [aprire il portale](https://portal.azure.com) in una finestra del browser e passare all'istanza di Azure Digital Twins originale cercando il nome nella barra di ricerca del portale.

Selezionare il pulsante **Elimina** e seguire le istruzioni per completare l'eliminazione.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Visualizzare i dettagli dell'istanza di Azure Digital gemelli nella portale di Azure nella scheda Panoramica. Il pulsante Elimina è evidenziato.":::