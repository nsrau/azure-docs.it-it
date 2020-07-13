---
title: Esplorare le nozioni di base con un'app client di esempio
titleSuffix: Azure Digital Twins
description: Esercitazione per esplorare gli SDK di Gemelli digitali di Azure usando un'applicazione da riga di comando di esempio
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d670ee0cb2396858d908ac13f70819efd57d7f63
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391826"
---
# <a name="explore-azure-digital-twins-with-a-sample-client-app"></a>Esplorare Gemelli digitali di Azure con un'app client di esempio

Questa esercitazione presenta un'applicazione di esempio che implementa un'applicazione client da riga di comando per interagire con un'istanza di Gemelli digitali di Azure. L'app client è simile a quella scritta in [Esercitazione: Scrivere il codice di un'app client](tutorial-code.md).

È possibile usare questo esempio per eseguire azioni essenziali di Gemelli digitali di Azure, ad esempio il caricamento di modelli, la creazione e la modifica di gemelli e la creazione di relazioni. È anche possibile esaminare il codice dell'esempio per acquisire informazioni sulle API di Gemelli digitali di Azure e provare a implementare i propri comandi modificando il progetto di esempio come si preferisce.

In questa esercitazione si apprenderà come:
1. Configurare un'istanza di Gemelli digitali di Azure
2. Configurare l'app da riga di comando di esempio per interagire con l'istanza
3. Usare l'app da riga di comando per esplorare Gemelli digitali di Azure, tra cui **modelli**, **gemelli digitali**, **relazioni** e **query**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Esplorare la soluzione di esempio

Ora che sono state configurate l'istanza e l'app di esempio, si userà il progetto di esempio e un codice di esempio già scritto per creare ed esplorare una soluzione di base di Gemelli digitali di Azure. I componenti principali della soluzione sono **modelli**, **gemelli digitali** e **relazioni**, che generano un **grafo dei gemelli** di un ambiente su cui è possibile eseguire query.

### <a name="model-a-physical-environment-with-dtdl"></a>Modellare un ambiente fisico con DTDL

Il primo passaggio per la creazione di una soluzione di Gemelli digitali di Azure consiste nel definire i [**modelli**](concepts-models.md) gemelli per l'ambiente. 

I modelli sono simili alle classi nei linguaggi di programmazione orientati a oggetti. Forniscono modelli definiti dall'utente per i [gemelli digitali](concepts-twins-graph.md) da seguire e di cui creare istanze in seguito. Sono scritti in un linguaggio simile a JSON, **DTDL (Digital Twin Definition Language)** , e consentono di definire le *proprietà*, i *dati di telemetria*, le *relazioni* e i *componenti* di un gemello.

> [!NOTE]
> DTDL consente anche di definire *comandi* nei gemelli digitali. Tuttavia, i comandi non sono attualmente supportati nel servizio Gemelli digitali di Azure.

Nella finestra di Visual Studio in cui è aperto il progetto _**AdtE2ESample**_ usare il riquadro *Esplora soluzioni* per passare alla cartella *AdtSampleApp\SampleClientApp\Models*. Questa cartella contiene modelli di esempio.

Selezionare *Room.json* per aprirlo nella finestra di modifica e cambiarlo nei modi seguenti:

* **Aggiornare il numero di versione** per indicare che si specifica una versione più aggiornata di questo modello. A tale scopo, sostituire *1* alla fine del valore `@id` con *2*. È anche possibile inserire qualsiasi numero maggiore del numero di versione corrente.
* **Modificare una proprietà**. Cambiare il nome della proprietà `Humidity` in *HumidityLevel* (o qualcosa di diverso se si preferisce). Se si usa un valore diverso da *HumidityLevel*, prendere nota del valore immesso e continuare a usarlo al posto di *HumidityLevel* nell'intera esercitazione.
* **Aggiungere una proprietà**. Sotto la proprietà `HumidityLevel` che termina alla riga 15 incollare il codice seguente per aggiungere una proprietà `RoomName` alla stanza:

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **Aggiungere una relazione**. Sotto la proprietà `RoomName` appena aggiunta incollare il codice seguente per aggiungere la possibilità che questo tipo di dispositivo gemello formi relazioni *contains* con altri gemelli:

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

Al termine, il modello aggiornato sarà simile al seguente:

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="File Room.json modificato con il numero di versione aggiornato, le proprietà HumidityLevel e RoomName e la relazione contains" border="false":::

Assicurarsi di salvare il file prima di procedere.

> [!TIP]
> Se si vuole provare a creare un modello personalizzato, è possibile incollare il codice del modello *Room* in un nuovo file salvato con un estensione *json* nella cartella *AdtSampleApp\SampleClientApp\Models*. Quindi, è possibile provare ad aggiungere proprietà e relazioni per rappresentare qualsiasi tipo di soluzione. È anche possibile esaminare gli altri modelli di esempio in questa cartella per trovare idee.

> [!TIP] 
> È disponibile un [esempio di validator DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) indipendente dal linguaggio che è possibile usare per controllare i documenti del modello e verificare che il file DTDL sia valido. È basato sulla libreria di parser DTDL, descritta in maggior dettaglio in [Procedura: Analizzare e convalidare modelli](how-to-use-parser.md).

### <a name="get-started-with-the-command-line-app"></a>Introduzione all'app da riga di comando

Ora che è stato definito un modello, i passaggi rimanenti consistono nell'usare l'app di esempio per interagire con l'istanza di Gemelli digitali di Azure. Eseguire il progetto premendo questo pulsante sulla barra degli strumenti:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Pulsante di avvio di Visual Studio (progetto SampleClientApp)":::

Viene aperta una finestra della console, in cui viene eseguita l'autenticazione e si attende un comando. 
* L'autenticazione viene gestita tramite il browser. Il Web browser predefinito verrà aperto con una richiesta di autenticazione. Usare la richiesta per accedere con le credenziali di Azure. È quindi possibile chiudere la scheda o la finestra del browser.

Ecco uno screenshot della console del progetto:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Messaggio di benvenuto dall'app da riga di comando":::

> [!TIP]
> Per un elenco di tutti i comandi che è possibile usare con questo progetto, immettere `help` nella console del progetto e premere INVIO.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Output del comando help":::

Mantenere in esecuzione la console del progetto per i passaggi rimanenti dell'esercitazione.

#### <a name="upload-models-to-azure-digital-twins"></a>Caricare modelli in Gemelli digitali di Azure

Dopo aver progettato i modelli, è necessario caricarli nell'istanza di Gemelli digitali di Azure. Questa operazione configura l'istanza del servizio Gemelli digitali di Azure con il vocabolario del dominio personalizzato. Una volta caricati i modelli, è possibile creare le istanze di gemelli che li usano.

Nella finestra della console del progetto eseguire il comando seguente per caricare il modello *Room* aggiornato, nonché un modello *Floor* che verrà usato anche nella sezione successiva per creare tipi diversi di gemelli.

```cmd/sh
CreateModels Room Floor
```

L'output dovrebbe indicare che i modelli sono stati creati correttamente.

> [!TIP]
> Se in precedenza è stato progettato un modello personalizzato, è possibile caricarlo qui aggiungendo il nome del file (è possibile omettere l'estensione) nell'elenco `Room Floor` del comando precedente.

Verificare se i modelli sono stati creati eseguendo il comando `GetModels true`. Viene eseguita una query sull'istanza di Gemelli digitali di Azure per recuperare tutti i modelli caricati e verranno stampate le relative informazioni complete. Cercare il modello *Room* modificato nei risultati:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Risultati di GetModels che mostrano il modello Room aggiornato":::

#### <a name="errors"></a>Errors

L'applicazione di esempio gestisce anche gli errori del servizio. 

Eseguire di nuovo il comando `CreateModels` per provare a ricaricare una seconda volta uno degli stessi modelli appena caricati:

```cmd/sh
CreateModels Room
```

Poiché i modelli non possono essere sovrascritti, ora viene restituito un errore del servizio.
Per informazioni dettagliate su come eliminare i modelli esistenti, vedere [Procedura: Gestire i modelli personalizzati](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>Creare i gemelli digitali

Ora che alcuni modelli sono stati caricati nell'istanza di Gemelli digitali di Azure, è possibile creare i [**gemelli digitali**](concepts-twins-graph.md) in base alle definizioni dei modelli. I gemelli digitali rappresentano le entità all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, le stanze di un edificio o i fari di un'auto. 

Per creare un gemello digitale, usare il comando `CreateDigitalTwin`. È necessario fare riferimento al modello su cui si basa il gemello e facoltativamente definire i valori iniziali per tutte le proprietà nel modello. In questa fase non è necessario passare informazioni sulle relazioni.

Eseguire questo codice nella console del progetto in esecuzione per creare diversi gemelli, in base al modello *Room* aggiornato in precedenza e a un altro modello, *Floor*. Tenere presente che *Room* include tre proprietà, quindi è possibile specificare argomenti con i valori iniziali per tali proprietà.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Se in precedenza è stato caricato un modello personalizzato, provare a creare un comando `CreateDigitalTwin` specifico in base ai comandi precedenti per aggiungere un gemello del proprio tipo di modello.

L'output di questi comandi dovrebbe indicare che i gemelli sono stati creati correttamente. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Estratto dei risultati dei comandi CreateDigitalTwin, che mostra floor0, floor1, room0 e room1":::

È anche possibile verificare se i gemelli sono stati creati eseguendo il comando `Query`. Questo comando esegue una query nell'istanza di Gemelli digitali di Azure per recuperare tutti i gemelli digitali che contiene. Cercare i gemelli *floor0*, *floor1*, *room0* e *Room1* nei risultati.

#### <a name="modify-a-digital-twin"></a>Modificare un gemello digitale

È anche possibile modificare le proprietà di un gemello creato. Provare a eseguire questo comando per cambiare la proprietà RoomName di *room0* da *Room0* a *PresidentialSuite*:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

L'output dovrebbe indicare che il gemello è stato aggiornato correttamente.

È anche possibile verificarlo eseguendo questo comando per visualizzare le informazioni di *room0*:

```cmd/sh
GetDigitalTwin room0
```

L'output dovrebbe riflettere il nome aggiornato.

> [!NOTE]
> L'API REST sottostante usa la patch di JSON per definire gli aggiornamenti di un gemello. L'app da riga di comando riflette questo formato, per cui è possibile sperimentare con il risultato effettivamente previsto dalle API sottostanti.

### <a name="create-a-graph-by-adding-relationships"></a>Creare un grafo con l'aggiunta di relazioni

Successivamente, è possibile creare **relazioni** tra questi gemelli, per connetterli in un [**grafo dei gemelli**](concepts-twins-graph.md). I grafi dei gemelli vengono usati per rappresentare l'intero ambiente. 

Per aggiungere una relazione, usare il comando `CreateRelationship`. Specificare il gemello da cui proviene la relazione, il tipo di relazione da aggiungere e il gemello a cui si connette la relazione. Infine, specificare un nome (ID) per la relazione.

Eseguire il codice seguente per aggiungere una relazione "contains" da ogni gemello *Floor* creato in precedenza a un gemello *Room* corrispondente. Si noti che, per eseguire questa operazione, è necessario che sia stata definita una relazione *contains* nel modello *Floor*.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

L'output di questi comandi conferma che le relazioni sono state create correttamente:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Estratto dei risultati dei comandi CreateRelationship, che mostra relationship0 e relationship1":::

È anche possibile verificare le relazioni con uno dei comandi seguenti, che eseguono query sulle relazioni nell'istanza di Gemelli digitali di Azure.
* Per vedere tutte le relazioni provenienti da ogni piano (visualizzazione delle relazioni da un lato):
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Per vedere tutte le relazioni in arrivo in ogni stanza (visualizzazione della relazione dall'"altro" lato):
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* Per eseguire query per recuperare queste relazioni singolarmente: 
    ```cmd/sh
    GetRelationship floor0 contains relationship0
    GetRelationship floor1 contains relationship1
    ```

I gemelli e le relazioni configurati in questa esercitazione formano il grafo concettuale seguente:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Grafo che mostra floor0 connesso tramite relationship0 a room0 e floor1 connesso tramite relationship1 a room1" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Eseguire una query sul grafo dei gemelli per rispondere alla domande sull'ambiente

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente. Eseguire i comandi seguenti nella console del progetto in esecuzione per avere un'idea di come funziona.

* **Quali sono tutte le entità dell'ambiente rappresentate in Gemelli digitali di Azure?** (query su tutto)

    ```cmd/sh
    Query
    ```

    In questo modo è possibile ottenere una panoramica dell'ambiente e assicurarsi che tutti gli elementi siano rappresentati come previsto all'interno di Gemelli digitali di Azure. Il risultato è un output contenente ogni gemello digitale con i relativi dettagli. Ecco un estratto:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Risultati parziali della query sui gemelli, che mostrano room0 e floor1":::

    >[!NOTE]
    >Il comando `Query` senza argomenti aggiuntivi è l'equivalente di `Query SELECT * FROM DIGITALTWINS`.

* **Quali sono tutte le stanze dell'ambiente?** (query per modello)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    È possibile limitare la query ai gemelli di un determinato tipo, per ottenere informazioni più specifiche su quello che rappresentano. Il risultato di questa operazione mostra *room0* e *room1*, ma **non** mostra *floor0* o *floor1* (perché si tratta di piani, non di stanze).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Risultati della query per modello, che mostrano solo room0 e room1":::

* **Quali sono tutte le stanze in *floor0*?** (query per relazione)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    È possibile eseguire query in base alle relazioni nel grafo, per ottenere informazioni sul modo in cui i gemelli sono connessi o limitare la query a una determinata area. In *floor0* è presente solo *room0*, che quindi è l'unica stanza nel risultato.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Risultati della query per relazione, che mostrano room0":::

* **Quali sono gli altri gemelli dell'ambiente con una temperatura superiore a 75?** (query per proprietà)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    È possibile eseguire una query sul grafo in base alle proprietà per rispondere a una serie di domande, anche per trovare outlier nell'ambiente che potrebbero richiedere attenzione. Sono supportati anche altri operatori di confronto, ossia *<* , *>* , *=* o *!=* . I risultati mostrano *room1*, perché ha una temperatura pari a 80.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Risultati della query per proprietà, che mostrano solo room1":::

* **Quali sono tutte le stanze in *floor0* con una temperatura superiore a 75?** (query composta)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    È anche possibile combinare le query precedenti come in SQL, usando operatori di combinazione come `AND`, `OR`, `NOT`. Questa query usa `AND` per rendere più specifica la query precedente sulle temperature dei gemelli. Il risultato include ora solo le stanze con temperature superiori a 75 che si trovano in *floor0*, ovvero in questo caso nessuna stanza. Il set di risultati è vuoto.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Risultati della query composta, senza risultati":::

## <a name="clean-up-resources"></a>Pulire le risorse

Il progetto di questa esercitazione costituisce la base per quella successiva, [Esercitazione: Connettere una soluzione end-to-end](tutorial-end-to-end.md). Se si prevede di continuare con l'esercitazione successiva, è possibile mantenere le risorse configurate qui per continuare a usare questa istanza di Gemelli digitali di Azure e l'app di esempio configurata.
* In questo caso, è possibile usare i comandi `DeleteAllTwins` e `DeleteAllModels` dell'app di esempio per cancellare rispettivamente i gemelli e i modelli nell'istanza. In questo modo si ottiene un ambiente pulito per l'esercitazione successiva.

Se le risorse create in questa esercitazione non sono più necessarie, seguire questa procedura per eliminarle.

Usando [Azure Cloud Shell](https://shell.azure.com), è possibile eliminare tutte le risorse di Azure di un gruppo di risorse con il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Questo comando rimuove il gruppo di risorse e l'istanza di Gemelli digitali di Azure.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

Aprire un'istanza di Azure Cloud Shell ed eseguire il comando seguente per eliminare il gruppo di risorse e tutti gli elementi contenuti al suo interno.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare quindi la registrazione dell'app di Azure Active Directory creata per l'app client con questo comando:

```azurecli
az ad app delete --id <your-application-ID>
```

Infine, eliminare la cartella dell'esempio di progetto scaricata nel computer locale.

## <a name="next-steps"></a>Passaggi successivi 

In questa esercitazione sono state configurate un'istanza di Gemelli digitali di Azure e un'applicazione client per interagire con l'istanza. L'app client è stata usata per esplorare Gemelli digitali di Azure, creando modelli, gemelli digitali e relazioni. Sono state anche eseguite alcune query sulla soluzione, per avere un'idea dei tipi di domande a cui Gemelli digitali di Azure è in grado di rispondere su un ambiente.

Continuare con l'esercitazione successiva per usare l'app da riga di comando di esempio in combinazione con altri servizi di Azure per completare uno scenario end-to-end basato sui dati:

> [!div class="nextstepaction"]
> [Esercitazione: Connettere una soluzione end-to-end](tutorial-end-to-end.md)

Oppure iniziare a esaminare la documentazione concettuale per altre informazioni sugli elementi usati nell'esercitazione:
* [Concetti: Modelli personalizzati](concepts-models.md)

È anche possibile approfondire ulteriormente i processi illustrati in questa esercitazione a partire dagli articoli sulle procedure:
* [Procedura: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure](how-to-use-cli.md)
