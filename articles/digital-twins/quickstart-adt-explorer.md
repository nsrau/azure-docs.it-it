---
title: 'Avvio rapido: Esplorare uno scenario di esempio'
titleSuffix: Azure Digital Twins
description: "Avvio rapido: Usare l'applicazione di esempio ADT Explorer per visualizzare ed esplorare uno scenario predefinito."
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: d203cb5ccef90fd09659ba64b7bcbc8b9be9e47a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358100"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Avvio rapido: Esplorare uno scenario di Gemelli digitali di Azure di esempio con ADT Explorer

Con Gemelli digitali di Azure è possibile creare e interagire con modelli live degli ambienti reali. In primo luogo, è necessario singoli elementi come *gemelli digitali* e quindi connetterli in un *grafo* della conoscenza che possa rispondere agli eventi live ed essere sottoposto a query per ottenere informazioni.

In questo argomento di avvio rapido si esplorerà un grafo predefinito di Gemelli digitali di Azure con l'aiuto di un'applicazione di esempio denominata [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Usare ADT Explorer per:

- Caricare una rappresentazione digitale di un ambiente.
- Visualizzare immagini visive dei gemelli e del grafo creati per rappresentare l'ambiente in Gemelli digitali di Azure.
- Eseguire altre attività di gestione tramite un'esperienza visiva basata su browser.

L'avvio rapido illustra i passaggi principali seguenti:

1. Configurare un'istanza di Gemelli digitali di Azure e ADT Explorer.
1. Caricare i modelli predefiniti e i dati del grafo per creare lo scenario di esempio.
1. Esplorare il grafico dello scenario creato.
1. Apportare modifiche al grafo.

Il grafo di esempio con cui si lavorerà rappresenta un edificio con due piani e due camere. Il grafo sarà simile a questa immagine:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Visualizzazione di un grafo costituito da quattro nodi circolari connessi tramite frecce. Un cerchio con etichetta 'Floor1' è connesso da una freccia con etichetta 'contains' a un cerchio con etichetta 'Room1'. Un cerchio con etichetta 'Floor0' è connesso da una freccia con etichetta 'contains' a un cerchio con etichetta 'Room0'. 'Floor1' e 'Floor0' non sono connessi.":::

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, [crearne una gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

È anche necessario **Node.js** nel computer. Per ottenere la versione più recente, vedere [Node.js](https://nodejs.org/).

Infine, occorre anche scaricare l'esempio da usare durante la procedura descritta nell'argomento di avvio rapido. L'applicazione di esempio è **ADT Explorer**. Questo esempio contiene l'app usata nella guida di avvio rapido per caricare ed esplorare uno scenario di Gemelli digitali di Azure. Contiene anche i file dello scenario di esempio. Per scaricare l'esempio, vedere [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selezionare il pulsante **Scarica ZIP** per scaricare nel computer un file ZIP di questo codice di esempio nel computer. Decomprimere la cartella **Azure_Digital_Twins__ADT__explorer.zip** ed estrarre i file.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurare Gemelli digitali di Azure e ADT Explorer

La prima cosa da fare per usare Gemelli digitali di Azure è configurare un'istanza di Gemelli digitali di Azure. Dopo aver creato un'istanza del servizio e configurato le credenziali per l'autenticazione con ADT Explorer, sarà possibile connettersi all'istanza in ADT Explorer e popolarla con i dati di esempio più avanti nella guida di avvio rapido.

Nella parte restante di questa sezione vengono illustrati questi passaggi.

### <a name="set-up-an-azure-digital-twins-instance"></a>Configurare un'istanza di Gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurare le credenziali di Azure locali

L'applicazione ADT Explorer usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte della libreria `Azure.Identity`) per l'autenticazione degli utenti con l'istanza di Gemelli digitali di Azure eseguita nel loro computer locale. Per altre informazioni sui diversi modi con cui un'app client può eseguire l'autenticazione con Gemelli digitali di Azure, vedere [Scrivere il codice di autenticazione dell'app](how-to-authenticate-client.md).

Con questo tipo di autenticazione, ADT Explorer cercherà le credenziali nell'ambiente locale, ad esempio un account di accesso di Azure nell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) locale o in Visual Studio o Visual Studio Code. Per questo motivo è necessario *accedere ad Azure in locale* tramite uno di questi meccanismi per configurare le credenziali per l'app ADT Explorer.

Se è già stato effettuato l'accesso ad Azure in uno di questi modi, è possibile passare alla [sezione successiva](#run-and-configure-adt-explorer).

In caso contrario, è possibile installare l'interfaccia della riga di comando di Azure locale con questa procedura:

1. Per completare l'installazione corrispondente al sistema operativo, seguire la procedura descritta in [questo collegamento](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
1. Aprire una finestra della console nel computer.
1. Eseguire `az login` e seguire le istruzioni sull'autenticazione per accedere all'account Azure.

Dopo l'accesso, ADT Explorer dovrebbe prelevare automaticamente le credenziali di Azure quando viene eseguito nella sezione successiva.

Facoltativamente, è possibile chiudere la finestra della console di autenticazione oppure tenerla aperta per usarla nel passaggio successivo.

### <a name="run-and-configure-adt-explorer"></a>Eseguire e configurare ADT Explorer

Eseguire ora l'applicazione ADT Explorer e configurarla per l'istanza di Gemelli digitali di Azure.

1. Passare alla cartella scaricata e decompressa **Azure_Digital_Twins__ADT__explorer**.
Aprire una finestra della console nel percorso della cartella **Azure_Digital_Twins__ADT__explorer/client/src**.

1. Eseguire `npm install` per scaricare tutte le dipendenze necessarie.

1. Avviare l'app eseguendo `npm run start`.

   Dopo alcuni secondi si aprirà una finestra del browser in cui verrà visualizzata l'app.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Finestra del browser che mostra un'app in esecuzione all'indirizzo localhost:3000. L'app è denominata ADT Explorer e contiene le caselle per Query Explorer, Model View, Graph View e Property Explorer. Non sono ancora presenti dati sullo schermo." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Selezionare il pulsante **Sign in** (Accedi) nell'angolo in alto a destra della finestra, illustrato nell'immagine seguente, per configurare ADT Explorer in modo che funzioni con l'istanza configurata.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer con l'icona di accesso evidenziata nella parte superiore della finestra. L'icona mostra la semplice silhouette di una persona a cui è sovrapposta la silhouette di una chiave." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Immettere l'URL dell'istanza di Gemelli digitali di Azure ottenuto in precedenza nella sezione [Prerequisiti](#prerequisites) nel formato *https://{nome host istanza}* .

>[!NOTE]
> È possibile rivedere o modificare queste informazioni in qualsiasi momento selezionando la stessa icona per aprire la casella **Sign In** (Accesso). Verranno mantenuti i valori immessi.

> [!TIP]
> Se viene visualizzato il messaggio di errore `SignalRService.subscribe` quando ci si connette, assicurarsi che l'URL di Gemelli digitali di Azure inizi con *https://* .

Se viene visualizzata una finestra popup **Autorizzazioni richieste** da Microsoft, concedere il consenso per l'applicazione e accettare per continuare.

## <a name="add-the-sample-data"></a>Aggiungere i dati di esempio

A questo punto occorre importare lo scenario e il grafo di esempio in ADT Explorer. Lo scenario di esempio si trova anche nella cartella **Azure_Digital_Twins__ADT__explorer** scaricata in precedenza.

### <a name="models"></a>Modelli

Il primo passaggio da eseguire in una soluzione di Gemelli digitali di Azure è la definizione del vocabolario per l'ambiente. Si creeranno creati [modelli](concepts-models.md) personalizzati che descrivono i tipi di entità che esistono nell'ambiente.

Ogni modello è scritto in un linguaggio simile a JSON-LD denominato DTDL (Digital Twin Definition Language). Ogni modello descrive un unico tipo di entità in termini di *proprietà*, *telemetria*, *relazioni* e *componenti*. Successivamente si useranno questi modelli come base per i gemelli digitali che rappresentano istanze specifiche di questi tipi.

In genere il processo di creazione di un modello è costituito da tre passaggi:

1. Scrivere la definizione del modello. Nella guida di avvio rapido questo passaggio è già stato eseguito nell'ambito della soluzione di esempio.
1. Convalidarla per assicurarsi che la sintassi sia accurata. Nella guida di avvio rapido questo passaggio è già stato eseguito nell'ambito della soluzione di esempio.
1. Caricarla nell'istanza di Gemelli digitali di Azure.
 
Per questa guida di avvio rapido i file del modello sono già stati scritti e convalidati e sono inclusi nella soluzione scaricata. In questa sezione si caricheranno due modelli scritti in precedenza nell'istanza per definire questi componenti di un ambiente edilizio:

* Piano
* Room

#### <a name="upload-models"></a>Caricare i modelli

Per caricare modelli, seguire questa procedura.

1. Nella casella **MODEL VIEW** (Visualizzazione modello) selezionare l'icona **Upload a Model** (Carica un modello).

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Nella casella Model View l'icona centrale è evidenziata. Mostra una freccia che punta verso l'alto in una nuvola." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Nella casella di selezione file visualizzata, passare alla cartella **Azure_Digital_Twins__ADT__explorer/client/examples** nel repository scaricato.
1. Selezionare **Room.json** e **Floor.json** e selezionare **OK**. Se si vuole è possibile caricare anche altri modelli, che però non verranno usati in questa guida di avvio rapido.
1. Seguire le istruzioni della finestra di dialogo popup in cui viene chiesto di accedere al proprio account Azure.

>[!NOTE]
>Se viene visualizzato il messaggio di errore seguente: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Casella popup che segnala un &quot;errore durante il recupero dei modelli: ClientAuthError: errore durante l'apertura della finestra popup. Può succedere se si usa Internet Explorer o se i popup sono bloccati nel browser&quot;. In fondo alla finestra del messaggio di errore è presente un pulsante Close." border="false"::: 
> Provare a disabilitare il blocco dei popup o a usare un browser diverso.

ADT Explorer carica ora questi file di modello nell'istanza di Gemelli digitali di Azure. Dovrebbero essere visualizzati nella casella **MODEL VIEW** (VISUALIZZAZIONE MODELLO) unitamente al rispettivo nome descrittivo e ID modello completo. È possibile selezionare le icone di informazioni **View Model** (Visualizza modello) per visualizzare il codice DTDL sottostante.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Riquadro Model View contenente due definizioni di modello, Floor (dtmi:example:Floor;1) e Room (dtmi:example:Room;1). L'icona di informazioni View Model, con la lettera 'i' in un cerchio, è evidenziata per ogni modello." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Gemelli e grafo dei gemelli

Ora che alcuni modelli sono stati caricati nell'istanza di Gemelli digitali di Azure, è possibile aggiungere i [gemelli digitali](concepts-twins-graph.md) che seguono le definizioni dei modelli.

I gemelli digitali rappresentano le entità effettive all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, i fari di un'auto o, in questa guida di avvio rapido, le stanze di un piano di un edificio. È possibile creare molti gemelli di un determinato tipo di modello, ad esempio più stanze che usano tutte il modello *Room* e collegarli con relazioni in un *grafo dei gemelli* che rappresenta l'ambiente completo.

In questa sezione verranno caricati gemelli creati in precedenza che sono connessi tra loro in un grafo creato in precedenza. Il grafo contiene due piani e due stanze, connessi nel layout seguente:

* Floor0
    - Contiene Room0
* Floor1
    - Contiene Room1

#### <a name="import-the-graph"></a>Importare il grafo

Per importare il grafo, seguire questa procedura.

1. Nel riquadro **GRAPH VIEW** (Visualizzazione grafico) selezionare l'icona **Import Graph** (Importa grafo).

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Nel riquadro Graph View è evidenziata un'icona. Mostra una freccia che punta verso l'alto in una nuvola." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. Nella casella di selezione file passare alla cartella **Azure_Digital_Twins__ADT__explorer/client/examples** e selezionare il file di foglio di calcolo **buildingScenario.xlsx**. Questo file contiene una descrizione del grafo di esempio. Selezionare **OK**.

   Dopo alcuni secondi ADT Explorer apre una visualizzazione **Import** che mostra un'anteprima del grafo che verrà caricato.

3. Per confermare il caricamento del grafico, selezionare l'icona **Save** (Salva) nell'angolo in alto a destra del riquadro **GRAPH VIEW** (VISUALIZZAZIONE GRAFO).

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Icona di salvataggio evidenziata nel riquadro di anteprima del grafo" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::.
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. ADT Explorer userà ora il file caricato per creare i gemelli richiesti e le relazioni tra di essi. Al termine, verrà visualizzata una finestra di dialogo. Selezionare **Close** (Chiudi).

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Finestra di dialogo che indica che l'importazione del grafo è riuscita. Il messaggio indica: &quot;Importazione riuscita. 4 gemelli importati. 2 relazioni importate&quot;." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Il grafo è stato caricato in ADT Explorer. Per visualizzarlo, selezionare il pulsante **Esegui query** nel riquadro **GRAPH EXPLORER** nella parte superiore della finestra di ADT Explorer.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Il pulsante Esegui query nell'angolo in alto a destra della finestra viene evidenziato." lightbox="media/quickstart-adt-explorer/run-query.png":::

Verrà eseguita la query predefinita per selezionare e visualizzare tutti i gemelli digitali. ADT Explorer recupererà tutti i gemelli e le relazioni dal servizio e creerà il grafo definito da essi nel riquadro **GRAPH VIEW** (VISUALIZZAZIONE GRAFO).

## <a name="explore-the-graph"></a>Esplorare il grafo

A questo punto è possibile vedere il grafo caricato dello scenario di esempio.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Riquadro Graph View contenente un grafo dei gemelli. Un cerchio con etichetta 'floor1' è connesso da una freccia con etichetta 'contains' a un cerchio con etichetta 'room1'. Un cerchio con etichetta 'floor0' è connesso da una freccia con etichetta 'contains' a un cerchio con etichetta 'room0'.":::

I cerchi ("nodi" del grafo) rappresentano i gemelli digitali e le linee rappresentano le relazioni. Il gemello **Floor0** contiene **Room0** e il gemello **Floor1** contiene **Room1**.

Se si usa un mouse, è possibile trascinare parti del grafo per spostarle in altre posizioni.

### <a name="view-twin-properties"></a>Visualizzare le proprietà dei gemelli

È possibile selezionare un gemello per visualizzare un elenco delle relative proprietà e i relativi valori nel riquadro **PROPERTY EXPLORER** (Esplora proprietà).

Queste sono le proprietà di Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Evidenziazione del riquadro Property Explorer che mostra le proprietà di Room0, tra cui il campo $dtId con il valore Room0, il campo Temperature con il valore 70 e il campo Humidity con il valore 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 ha una temperatura impostata su 70.

Queste sono le proprietà di Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Evidenziazione del riquadro Property Explorer che mostra le proprietà di Room1, tra cui il campo $dtId con il valore Room1, il campo Temperature con il valore 80 e il campo Humidity con il valore 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 ha una temperatura impostata su 80.

### <a name="query-the-graph"></a>Eseguire una query sul grafo

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente.

Un modo per eseguire query sui gemelli nel grafo è tramite le relative *proprietà*. Eseguire query sul grafo in base alle proprietà può essere utile per rispondere a una serie di domande. Ad esempio, per trovare outlier nell'ambiente che potrebbero richiedere attenzione.

In questa sezione verrà eseguita una query per rispondere alla domanda relativa al numero di gemelli nell'ambiente con una temperatura superiore a 75.

Per vedere la risposta, eseguire la query seguente nel riquadro **QUERY EXPLORER** (Esplora query).

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

In base alle proprietà dei gemelli visualizzate in precedenza, tenere presente che Room0 ha una temperatura pari a 70 e Room1 ha una temperatura pari a 80. Per questo motivo nei risultati viene visualizzato solo Room1.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Risultati della query per proprietà, che mostrano solo Room1." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Nella query precedente sono supportati anche altri operatori di confronto, ossia (<,>, = o !=). Per provare a rispondere alle proprie domande, è possibile provare a inserire questi operatori con valori diversi o proprietà dei gemelli diverse nella query.

## <a name="edit-data-in-the-graph"></a>Modificare i dati nel grafo

È possibile usare ADT Explorer per modificare le proprietà dei gemelli rappresentati nel grafo. In questa sezione si aumenterà la temperatura di Room0 a 76.

Per iniziare, selezionare **Room0** per visualizzare il relativo elenco delle proprietà nel riquadro **PROPERTY EXPLORER** (ESPLORA PROPRIETÀ).

Le proprietà in questo elenco sono modificabili. Selezionare il valore di temperatura **70** per immettere un nuovo valore. Immettere **76** e selezionare l'icona **Save** (Salva) per aggiornare la temperatura a **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Riquadro Property Explorer con le proprietà di Room0. Il valore della temperatura è una casella modificabile che mostra 76 e l'icona di salvataggio è evidenziata." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Verrà ora visualizzata la finestra **Patch Information** (Informazioni patch) contenente il codice patch usato in background con le [API](how-to-use-apis-sdks.md) di Gemelli digitali di Azure per eseguire l'aggiornamento. Selezionare **Close** (Chiudi).

### <a name="query-to-see-the-result"></a>Eseguire una query per visualizzare il risultato

Per verificare che il grafo abbia registrato correttamente l'aggiornamento della temperatura di Room0, eseguire di nuovo la query precedente per ottenere tutti i gemelli dell'ambiente con una temperatura superiore a 75.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Ora che la temperatura di Room0 è stata modificata da 70 a 76, entrambi i gemelli dovrebbero essere visualizzati nel risultato.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Risultati della query per proprietà, che mostrano Room0 e Room1." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Rivedere e contestualizzare le nozioni apprese

In questa guida di avvio rapido è stata creata un'istanza di Gemelli digitali di Azure, che è stata quindi connessa ad ADT Explorer e compilata con uno scenario di esempio.

Il grafo è stato quindi esplorato nei modi seguenti:

* Usando una query per rispondere a una domanda sullo scenario.
* Modificando una proprietà in un gemello digitale.
* Eseguendo di nuovo la query per vedere come cambia la risposta in seguito all'aggiornamento.

Lo scopo di questo esercizio è illustrare come usare il grafo di Gemelli digitali di Azure per rispondere a domande sull'ambiente, anche quando l'ambiente continua a cambiare.

In questa guida di avvio rapido la temperatura è stata aggiornata manualmente. È pratica comune in Gemelli digitali di Azure connettere i gemelli digitali a dispositivi IoT reali, in modo che ricevano gli aggiornamenti automaticamente in base ai dati di telemetria. In questo modo è possibile creare un grafo dinamico che rifletta sempre lo stato reale dell'ambiente e usare query per ottenere informazioni su ciò che accade nell'ambiente in tempo reale.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Per concludere le attività di questa guida di avvio rapido, prima di tutto terminare l'app console in esecuzione. In questo modo verrà chiusa la connessione all'app ADT Explorer nel browser e non sarà più possibile visualizzare i dati in tempo reale nel browser. È possibile chiudere la scheda del browser.

Se si prevede di continuare con le esercitazioni su Gemelli digitali di Azure, è possibile riutilizzare l'istanza usata in questa guida di avvio rapido per gli articoli corrispondenti e non è necessario rimuoverla.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Infine, eliminare la cartella di esempio del progetto, **Azure_Digital_Twins__ADT__explorer**, scaricate nel computer locale. Potrebbe essere necessario eliminare le versioni compresse e decompresse.

## <a name="next-steps"></a>Passaggi successivi

Continuare quindi con le esercitazioni di Gemelli digitali di Azure per creare uno scenario di Gemelli digitali di Azure personalizzato e i relativi strumenti di interazione.

> [!div class="nextstepaction"]
> [Esercitazione: Scrivere il codice di un'app client](tutorial-code.md)