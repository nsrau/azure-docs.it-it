---
title: 'Avvio rapido: Esplorare uno scenario di esempio'
titleSuffix: Azure Digital Twins
description: "Avvio rapido: Usare l'applicazione di esempio ADT Explorer per visualizzare ed esplorare uno scenario predefinito."
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 455cf921cfcd4ac5d0e81fb4e092ec165070a3f1
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331567"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Avvio rapido: Esplorare uno scenario di Gemelli digitali di Azure di esempio con ADT Explorer

Con Gemelli digitali di Azure è possibile creare e interagire con modelli live degli ambienti reali. A questo scopo occorre modellare singoli elementi come **gemelli digitali** , quindi connetterli in un **grafo** della conoscenza che possa rispondere agli eventi live ed essere sottoposto a query per ottenere informazioni.

In questo argomento di avvio rapido si esplorerà un grafo predefinito di Gemelli digitali di Azure con l'aiuto di un'applicazione di esempio denominata [**Azure Digital Twins (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). ADT Explorer consente di caricare una rappresentazione digitale di un ambiente, visualizzare immagini visive dei gemelli e del grafo creati per rappresentare l'ambiente in Gemelli digitali di Azure ed eseguire altre attività di gestione tramite un'esperienza visiva basata sul browser.

L'avvio rapido illustra i passaggi principali seguenti:

1. Configurare un'istanza di Gemelli digitali di Azure e ADT Explorer
1. Caricare i modelli predefiniti e i dati del grafo per creare lo scenario di esempio
1. Esplorare il grafico dello scenario creato
1. Modificare il grafo

Il grafo di esempio con cui si lavorerà rappresenta un edificio con due piani e due camere. Il grafo sarà simile al seguente:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi.":::

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, **[crearne una gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

È anche necessario **Node.js** nel computer. È possibile scaricare la versione più recente da questo collegamento: [Node.js](https://nodejs.org/).

Infine, occorre anche scaricare due esempi da usare durante la procedura descritta nell'argomento di avvio rapido:
* L'applicazione di esempio **ADT Explorer** . Questo esempio contiene l'app principale usata nella guida di avvio rapido per caricare ed esplorare uno scenario di Gemelli digitali di Azure. È possibile ottenere l'app in [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Fare clic sul pulsante *Scarica ZIP* per scaricare nel computer un file *ZIP* di questo codice di esempio nel computer. Verrà scaricata una cartella ZIP nel computer denominata _**Azure_Digital_Twins__ADT__explorer.zip**_ . Decomprimere la cartella ed estrarre i file.
* Lo **scenario di esempio di Gemelli digitali di Azure** . È incluso un grafo predefinito di Gemelli digitali di Azure da caricare e usare in ADT Explorer. È possibile ottenere lo scenario in [Esempi end-to-end di Gemelli digitali di Azure](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Fare clic sul pulsante *Scarica ZIP* per scaricare nel computer un file *ZIP* di questo codice di esempio nel computer. Verrà scaricata una cartella ZIP nel computer denominata _**Azure_Digital_Twins_end_to_end_samples.zip**_ . Decomprimere la cartella ed estrarre i file.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurare Gemelli digitali di Azure e ADT Explorer

La prima cosa da fare per usare Gemelli digitali di Azure è configurare un' **istanza di Gemelli digitali di Azure** . Dopo aver creato un'istanza del servizio, sarà possibile popolarla con i dati di esempio più avanti nella guida di avvio rapido.

Occorre anche configurare le autorizzazioni per l'esecuzione di ADT Explorer nel computer e l'accesso all'istanza di Gemelli digitali di Azure. In questo modo sarà possibile usare l'app di esempio per esplorare l'istanza e i relativi dati.

### <a name="set-up-azure-digital-twins-instance-and-app-registration"></a>Configurare un'istanza di Gemelli digitali di Azure e una registrazione dell'app

Prima di tutto, **configurare un'istanza di Gemelli digitali di Azure** e l'autenticazione necessaria per poterla usare. A tale scopo, seguire le istruzioni in [*Procedura: Configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md). A seconda dell'esperienza preferita, l'articolo relativo alla configurazione è disponibile per il [portale di Azure](how-to-set-up-instance-portal.md), l'[interfaccia della riga di comando](how-to-set-up-instance-cli.md) o un [esempio di script di distribuzione automatizzato di Cloud Shell](how-to-set-up-instance-scripted.md). Tutte le versioni delle istruzioni contengono anche le operazioni da eseguire per verificare che ogni passaggio sia stato completato correttamente e sia quindi possibile passare all'uso della nuova istanza.
* Dopo aver configurato l'istanza di Gemelli digitali di Azure, è necessario avere il **_nome host_** dell'istanza, [reperibile nel portale](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values).

Per autenticare l'applicazione ADT Explorer, è anche necessario configurare una **registrazione dell'app** . Seguire le istruzioni in [*Procedura dettagliata: Creare una registrazione dell'app*](how-to-create-app-registration.md) per configurare questa impostazione. 
* Una volta eseguita la registrazione dell'app, è necessario avere l' **_ID applicazione (client)_** e l' **_ID directory (tenant)_** della registrazione, [reperibili nel portale](how-to-create-app-registration.md#collect-client-id-and-tenant-id).

### <a name="set-adt-explorer-permissions"></a>Impostare le autorizzazioni di ADT Explorer

Preparare quindi l'istanza di Gemelli digitali di Azure creata per lavorare con ADT Explorer, che è un'applicazione Web ospitata in locale. Accedere alla pagina [Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) nel portale di Azure e selezionare nell'elenco il nome della **registrazione dell'app** creata nella sezione precedente.

Selezionare *Autenticazione* nel menu della registrazione e quindi selezionare *+ Aggiungi una piattaforma* .

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

Nella pagina *Configura le piattaforme* selezionare *Web* .
Compilare i dettagli di configurazione nel modo seguente:
* **URI di reindirizzamento** : aggiungere l'URI di reindirizzamento *http://localhost:3000* .
* **Concessione implicita** : selezionare la casella *Token di accesso* .

Selezionare *Configura* per terminare.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

A questo punto si dispone di una configurazione Web che verrà usata da ADT Explorer. Questa configurazione si riflette nella scheda Autenticazione del portale di Azure. Dopo aver verificato le sezioni riportate di seguito, fare clic su *Salva* .

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi.":::

### <a name="run-and-configure-adt-explorer"></a>Eseguire e configurare ADT Explorer

Eseguire ora l'applicazione ADT Explorer e configurarla per l'istanza di Gemelli digitali di Azure.

Passare alla cartella scaricata e decompressa _**Azure_Digital_Twins__ADT__explorer**_ . Aprire un prompt dei comandi in corrispondenza del percorso della cartella *Azure_Digital_Twins__ADT__explorer/client/src* .

Eseguire `npm install` per scaricare tutte le dipendenze necessarie.

Avviare quindi l'app con il comando `npm run start`.

Dopo alcuni secondi si aprirà una finestra del browser in cui verrà visualizzata l'app.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Fare clic sul pulsante *Sign in* (Accedi) nella parte superiore della finestra (illustrato nell'immagine seguente) per configurare ADT Explorer in modo che funzioni con l'istanza configurata. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Immettere le informazioni importanti raccolte in precedenza nella sezione [Prerequisiti](#prerequisites):
* ID applicazione (client)
* ID directory (tenant)
* URL dell'istanza di Gemelli digitali di Azure in formato *https://{nome host dell'istanza}*

>[!NOTE]
> È possibile rivedere o modificare queste informazioni in qualsiasi momento selezionando la stessa icona per visualizzare nuovamente la casella di accesso. Verranno mantenuti i valori immessi.

> [!TIP]
> Se viene visualizzato il messaggio di errore `SignalRService.subscribe` quando ci si connette, assicurarsi che l'URL di Gemelli digitali di Azure inizi con *https://* .

Se viene visualizzata una finestra popup *Autorizzazioni richieste* da Microsoft, concedere il consenso per l'applicazione e accettare per continuare.

## <a name="add-the-sample-data"></a>Aggiungere i dati di esempio

A questo punto occorre importare lo scenario e il grafo di esempio in ADT Explorer.

Passare alla cartella _**Azure_Digital_Twins_end_to_end_samples**_ scaricata e decompressa, in cui si trova lo scenario di esempio.

### <a name="models"></a>Modelli

Il primo passaggio da eseguire in una soluzione di Gemelli digitali di Azure è la definizione del vocabolario per l'ambiente. A questo scopo occorre creare [**modelli**](concepts-models.md) personalizzati che descrivano i tipi di entità esistenti nell'ambiente. 

Ogni modello è scritto in un linguaggio simile a JSON-LD detto **DTDL (Digital Twin Definition Language)** e descrive un singolo tipo di entità in termini di *proprietà* , *dati di telemetria* , *relazioni* e *componenti* . Successivamente si useranno questi modelli come base per i gemelli digitali che rappresentano istanze specifiche di questi tipi.

In genere il processo di creazione di un modello è costituito da tre passaggi:
1. Scrivere la definizione del modello (nella guida di avvio rapido questa operazione è già stata eseguita nell'ambito della soluzione di esempio)
2. Convalidarla per assicurarsi che la sintassi sia accurata (nella guida di avvio rapido questa operazione è già stata eseguita nell'ambito della soluzione di esempio)
3. Caricarla nell'istanza di Gemelli digitali di Azure
 
Per questa guida di avvio rapido i file del modello sono già stati scritti e convalidati preventivamente e sono inclusi nella soluzione scaricata. In questa sezione si caricheranno due modelli precedentemente scritti nell'istanza per definire questi componenti di un ambiente edilizio:
* Piano
* Room

#### <a name="upload-models"></a>Caricare i modelli

Nella casella *MODEL VIEW* (Visualizzazione modello) selezionare l'icona *Upload a Model* (Carica un modello).

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Nella casella di selezione file visualizzata passare alla cartella *Azure_Digital_Twins_end_to_end_samples/AdtSampleApp/SampleClientApp/models* nel repository scaricato.
2. Selezionare *Room.json* e *Floor.json* e scegliere OK. Se si vuole è possibile caricare anche gli altri modelli, ma non verranno usati in questa guida di avvio rapido.
3. Seguire le istruzioni della finestra di dialogo popup in cui viene chiesto di accedere al proprio account Azure.

>[!NOTE]
>Se viene visualizzato il messaggio di errore seguente: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." border="false"::: 
> Provare a disabilitare il blocco dei popup o a usare un browser diverso.

ADT Explorer caricherà ora questi file di modello nell'istanza di Gemelli digitali di Azure. Dovrebbero essere visualizzati nella finestra *MODEL VIEW* (Visualizzazione modello), con il rispettivo nome descrittivo e ID modello completo. È possibile fare clic sulle icone di informazioni *View Model* (Visualizza modello) per visualizzare il codice DTDL sottostante.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Gemelli e grafo dei gemelli

Ora che alcuni modelli sono stati caricati nell'istanza di Gemelli digitali di Azure, è possibile aggiungere i [**gemelli digitali**](concepts-twins-graph.md) che seguono le definizioni dei modelli. 

I gemelli digitali rappresentano le entità reali all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, i fari di un'auto o, in questa guida di avvio rapido, le stanze di un piano di un edificio. È possibile creare molti gemelli di un determinato tipo di modello (come più stanze che usano tutte il modello *Room* ) e collegarli con relazioni in un **grafo dei gemelli** che rappresenta l'ambiente completo.

In questa sezione verranno caricati gemelli creati in precedenza che sono connessi tra loro in un grafo creato preventivamente. Il grafo contiene due piani e due stanze, connessi nel layout seguente:
* *Floor0*
    - contiene *Room0*
* *Floor1*
    - contiene *Room1*

#### <a name="import-the-graph"></a>Importare il grafo

Nel riquadro *GRAPH VIEW* (Visualizzazione grafico) selezionare l'icona *Import Graph* (Importa grafo).

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/import-graph.png":::

Nella casella di selezione file passare alla cartella *Azure_Digital_Twins_end_to_end_samples/AdtSampleApp/SampleClientApp* e scegliere il file del foglio di calcolo _**buildingScenario.xlsx**_ . Questo file contiene una descrizione del grafo di esempio. Scegliere OK.

Dopo alcuni secondi ADT Explorer aprirà una visualizzazione *Import* che mostra un'anteprima del grafo che verrà caricato.

Per confermare il caricamento del grafico, selezionare l'icona *Save* (Salva) nell'angolo superiore destro del riquadro *GRAPH VIEW* (Visualizzazione grafo):

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer userà ora il file caricato per creare i gemelli richiesti e le relazioni tra di essi. Al termine dell'operazione verrà visualizzata una finestra di dialogo. Scegliere *Close* (Chiudi).

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Il grafo è stato caricato in ADT Explorer. Per visualizzarlo, selezionare il pulsante *Esegui query* nel riquadro *GRAPH EXPLORER* nella parte superiore della finestra di ADT Explorer. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/run-query.png":::

Verrà eseguita la query predefinita per selezionare e visualizzare tutti i gemelli digitali. ADT Explorer recupererà tutti i gemelli e le relazioni dal servizio e creerà il grafo definito da essi nel riquadro *GRAPH VIEW* (Visualizzazione grafo).

## <a name="explore-the-graph"></a>Esplorare il grafo

A questo punto è possibile vedere il grafo caricato dello scenario di esempio:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." del grafo) rappresentano i gemelli digitali e le linee rappresentano le relazioni. Come si può vedere, il gemello *Floor0* contiene *Room0* e il gemello *Floor1* contiene *Room1* .

Se si usa un mouse, è possibile fare clic e trascinare parti del grafo per spostarle in altre posizioni.

### <a name="view-twin-properties"></a>Visualizzare le proprietà dei gemelli 

È possibile selezionare un gemello per visualizzare un elenco delle relative proprietà e i relativi valori nel riquadro *PROPERTY EXPLORER* (Esplora proprietà). 

Queste sono le proprietà di *Room0* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Si noti che *Room0* ha una temperatura impostata su **70** .

Queste sono le proprietà di *Room1* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Si noti che *Room1* ha una temperatura impostata su **80** .

### <a name="query-the-graph"></a>Eseguire una query sul grafo

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente. 

Un modo per eseguire query sui gemelli nel grafo è tramite le relative *proprietà* . Eseguire query sul grafo in base alle proprietà può essere utile per rispondere a una serie di domande, anche per trovare outlier nell'ambiente che potrebbero richiedere attenzione.

In questa sezione si eseguirà una query per rispondere alla domanda seguente: _**Quali sono i gemelli dell'ambiente con una temperatura superiore a 75?**_

Per vedere la risposta, eseguire la query seguente nel riquadro *QUERY EXPLORER* (Esplora query):

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

In base alle proprietà dei gemelli visualizzate in precedenza, tenere presente che *Room0* ha una temperatura pari a **70** e *Room1* ha una temperatura pari a **80** . Di conseguenza, nei risultati viene visualizzato solo _**Room1**_ .
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Nella query riportata sopra sono supportati anche altri operatori di confronto, ossia *<* , *>* , *=* o *!=* . Per provare a rispondere alle proprie domande, è possibile provare a inserire questi operatori con valori diversi o proprietà dei gemelli diverse nella query.

## <a name="edit-data-in-the-graph"></a>Modificare i dati nel grafo

È possibile usare ADT Explorer per modificare le proprietà dei gemelli rappresentati nel grafo. In questa sezione si **_aumenterà la temperatura di_ Room0 a 76** .

A questo scopo selezionare *Room0* per visualizzare il relativo elenco delle proprietà nel riquadro *PROPERTY EXPLORER* (Esplora proprietà).

Le proprietà in questo elenco sono modificabili. Selezionare il valore di temperatura **70** per immettere un nuovo valore. Immettere **76** e selezionare l'icona *Save* (Salva) per aggiornare la temperatura a **76** .

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dopo il salvataggio, verrà visualizzata la finestra *Patch Information* (Informazioni patch) contenente il codice patch usato in background con le [API](how-to-use-apis-sdks.md) di Gemelli digitali di Azure per eseguire l'aggiornamento. Scegliere *Close* (Chiudi).

### <a name="query-to-see-the-result"></a>Eseguire una query per visualizzare il risultato

Per verificare che il grafo abbia registrato correttamente l'aggiornamento della temperatura di *Room0* , eseguire di nuovo la query precedente per **ottenere tutti i gemelli dell'ambiente con una temperatura superiore a 75** :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Ora che la temperatura di *Room0* è stata modificata da **70** a **76** , entrambi i gemelli dovrebbero essere visualizzati nel risultato.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta &quot;Floor1&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room1&quot;. Un cerchio con etichetta &quot;Floor0&quot; è connesso da una freccia con etichetta &quot;contains&quot; a un cerchio con etichetta &quot;Room0&quot;. 'Floor1' e 'Floor0' non sono connessi." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Rivedere e contestualizzare le nozioni apprese

In questa guida di avvio rapido è stata creata un'istanza di Gemelli digitali di Azure, che è stata quindi connessa ad ADT Explorer e compilata con uno scenario di esempio. 

Il grafo è stato quindi esplorato nei modi seguenti:
1. Usando una query per rispondere a una domanda sullo scenario.
2. Modificando una proprietà in un gemello digitale.
3. Eseguendo di nuovo la query per vedere come cambia la risposta in seguito all'aggiornamento.

Lo scopo di questo esercizio è illustrare come usare il grafo di Gemelli digitali di Azure per rispondere a domande sull'ambiente, anche quando l'ambiente continua a cambiare. 

Anche se in questa guida di avvio rapido la temperatura è stata aggiornata manualmente, è pratica comune in Gemelli digitali di Azure connettere i gemelli digitali a dispositivi ioT reali, in modo che ricevano gli aggiornamenti automaticamente in base ai dati di telemetria. In questo modo è possibile creare un grafo dinamico che rifletta sempre lo stato reale dell'ambiente e usare query per ottenere informazioni su ciò che accade nell'ambiente in tempo reale.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Per concludere le attività di questa guida di avvio rapido, prima di tutto terminare l'app console in esecuzione. In questo modo verrà chiusa la connessione all'app ADT Explorer nel browser e non sarà più possibile visualizzare i dati in tempo reale nel browser. È possibile chiudere la scheda del browser.

Se si prevede di continuare con le esercitazioni su Gemelli digitali di Azure, è possibile riutilizzare l'istanza usata in questa guida di avvio rapido per gli articoli corrispondenti e non è necessario rimuoverla.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Infine, eliminare le cartelle di esempio del progetto scaricate nel computer locale ( _**Azure_Digital_Twins__ADT__explorer**_ e _**Azure_Digital_Twins_end_to_end_samples**_ ). Potrebbe essere necessario eliminare le versioni compresse e decompresse.

## <a name="next-steps"></a>Passaggi successivi 

Continuare quindi con le esercitazioni di Gemelli digitali di Azure per creare uno scenario di Gemelli digitali di Azure personalizzato e i relativi strumenti di interazione.

> [!div class="nextstepaction"]
> [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)