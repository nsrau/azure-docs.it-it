---
title: Connettere una soluzione end-to-end
titleSuffix: Azure Digital Twins
description: Esercitazione per creare una soluzione end-to-end di Gemelli digitali di Azure basata sui dati del dispositivo.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 0f9ddf2f4fba9e216ef0b5a5747fc32f4288cf67
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737275"
---
# <a name="build-out-an-end-to-end-solution"></a>Creare una soluzione end-to-end

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Per configurare una soluzione end-to-end completa basata su dati in tempo reale dell'ambiente, è possibile connettere l'istanza di Gemelli digitali di Azure ad altri servizi di Azure per la gestione di dispositivi e dati.

In questa esercitazione si apprenderà come:
* Configurare un'istanza di Gemelli digitali di Azure
* Seguire l'esempio di scenario edilizio e creare un'istanza di componenti scritti in precedenza
* Usare un'app di [Funzioni di Azure](../azure-functions/functions-overview.md) per instradare i dati di telemetria simulati da un dispositivo [hub IoT](../iot-hub/about-iot-hub.md) alle proprietà dei gemelli digitali
* Propagare le modifiche tramite il **grafo dei gemelli** elaborando le notifiche dei gemelli digitali con Funzioni di Azure, endpoint e route

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Introduzione allo scenario edilizio

Il progetto di esempio usato in questa esercitazione rappresenta uno **scenario edilizio** reale, che contiene un piano, una stanza e un termostato. Questi componenti verranno rappresentati digitalmente in un'istanza di Gemelli digitali di Azure, che verrà quindi connessa a un [hub IoT](../iot-hub/about-iot-hub.md), a [Griglia di eventi](../event-grid/overview.md) e a due [funzioni di Azure](../azure-functions/functions-overview.md) per facilitare lo spostamento dei dati.

Di seguito è riportato un diagramma che rappresenta lo scenario completo. 

Si creerà prima di tutto un'istanza di Gemelli digitali di Azure (**sezione A** nel diagramma), quindi si configurerà il flusso di dati di telemetria nei gemelli digitali (**freccia B**), infine si configurerà la propagazione dei dati tramite il grafo dei gemelli (**freccia C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Rappresentazione grafica dello scenario edilizio. Illustra il flusso dei dati da un dispositivo all'hub IoT, a un'istanza di Gemelli digitali di Azure tramite una funzione di Azure (sezione A), quindi a un'altra funzione di Azure per l'elaborazione tramite Griglia di eventi (freccia C)":::

Per completare lo scenario, si dovrà interagire con i componenti dell'app di esempio scritta e scaricata in precedenza.

Ecco i componenti implementati dall'app di esempio *AdtSampleApp* dello scenario edilizio:
* Autenticazione del dispositivo 
* Esempi di utilizzo di [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) (disponibili in *CommandLoop.cs*)
* Interfaccia console per chiamare l'API di Gemelli digitali di Azure
* *SampleClientApp*: una soluzione di esempio di Gemelli digitali di Azure
* *SampleFunctionsApp*: un'app di Funzioni di Azure che aggiorna il grafo di Gemelli digitali di Azure come risultato dei dati di telemetria provenienti da eventi dell'hub IoT e di Gemelli digitali di Azure

Il progetto di esempio contiene anche un componente di autorizzazione interattivo. Ogni volta che si avvia il progetto, viene visualizzata una finestra del browser in cui viene chiesto di accedere con l'account Azure.

### <a name="instantiate-the-pre-created-twin-graph"></a>Creare un'istanza del grafo dei gemelli creato in precedenza

Prima di tutto, si userà la soluzione *AdtSampleApp* dal progetto di esempio per creare il componente Gemelli digitali di Azure dello scenario end-to-end (**sezione A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Estratto del grafo dello scenario edilizio completo che evidenzia la sezione A, ovvero l'istanza di Gemelli digitali di Azure":::

Nella finestra di Visual Studio in cui è aperto il progetto _**AdtE2ESample**_ premere questo pulsante sulla barra degli strumenti per eseguirlo:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Pulsante di avvio di Visual Studio (progetto SampleClientApp)":::

Viene aperta una finestra della console, in cui viene eseguita l'autenticazione e si attende un comando. In questa console eseguire il comando successivo per creare un'istanza della soluzione Gemelli digitali di Azure di esempio.

> [!IMPORTANT]
> Se nell'istanza di Gemelli digitali di Azure sono già presenti gemelli digitali e relazioni, questo comando li eliminerà e li sostituirà con i gemelli e le relazioni dello scenario di esempio.

```cmd/sh
SetupBuildingScenario
```

L'output di questo comando è costituito da una serie di messaggi di conferma, mentre vengono creati e connessi tre [**gemelli digitali**](concepts-twins-graph.md) nell'istanza di Gemelli digitali di Azure, ovvero un piano denominato *floor1*, una stanza denominata *room21*e un sensore di temperatura denominato *thermostat67*. Questi gemelli digitali rappresentano le entità che esisterebbero in un ambiente reale.

Sono connessi tramite relazioni nel [**grafo dei gemelli**](concepts-twins-graph.md) seguente. Il grafo dei gemelli rappresenta l'ambiente nel suo complesso, incluso il modo in cui le entità interagiscono e sono correlate tra loro.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Grafo che mostra che floor1 contiene room21 e che room21 contiene thermostat67" border="false":::

È possibile verificare che i gemelli sono stati creati con il comando seguente, che esegue una query sull'istanza di Gemelli digitali di Azure connessa per recuperare tutti i gemelli digitali che contiene:

```cmd/sh
Query
```

Successivamente, è possibile arrestare l'esecuzione del progetto. Mantenere però aperta la soluzione in Visual Studio, perché continuerà a essere usata nell'intera esercitazione.

## <a name="set-up-the-sample-function-app"></a>Configurare l'app per le funzioni di esempio

Il passaggio successivo prevede la configurazione di un'[app di Funzioni di Azure](../azure-functions/functions-overview.md) che verrà usata in questa esercitazione per elaborare i dati. L'app, *SampleFunctionsApp*, contiene due funzioni:
* *ProcessHubToDTEvents*: elabora i dati dell'hub IoT in ingresso e aggiorna di conseguenza l'istanza di Gemelli digitali di Azure
* *ProcessDTRoutedData*: elabora i dati provenienti dai gemelli digitali e aggiorna di conseguenza i gemelli padre nell'istanza di Gemelli digitali di Azure

In questa sezione si pubblicherà l'app per le funzioni scritta in precedenza e si verificherà che sia in grado di accedere a Gemelli digitali di Azure assegnandole un'identità di Azure Active Directory (AAD). Completando questi passaggi sarà possibile usare le funzioni all'interno dell'app per le funzioni nel resto dell'esercitazione. 

### <a name="publish-the-app"></a>Pubblicare l'app

Tornare nella finestra di Visual Studio in cui è aperto il progetto _**AdtE2ESample**_. Nel riquadro *Esplora soluzioni* fare clic con il pulsante destro del mouse per selezionare il file di progetto _**SampleFunctionsApp**_ e scegliere **Pubblica**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: pubblicare il progetto":::

Nella pagina *Pubblica* che segue lasciare la selezione di destinazione predefinita, **Azure**, e premere *Avanti*. 

Per una destinazione specifica, scegliere **App per le funzioni di Azure (Windows)** e premere *Avanti*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Pubblicare la funzione di Azure in Visual Studio: destinazione specifica":::

Nella pagina *Istanza di Funzioni* scegliere la propria sottoscrizione. Si dovrebbe popolare una finestra con i *gruppi di risorse* della sottoscrizione.

Selezionare il gruppo di risorse dell'istanza e quindi *+ Crea una nuova applicazione di Funzioni di Azure*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Pubblicare la funzione di Azure in Visual Studio: Istanza di Funzioni (prima dell'app per le funzioni)":::

Nella finestra *App per le funzioni (Windows) - Crea nuova* compilare i campi come segue:
* **Nome** è il nome del piano a consumo che verrà usato da Azure per ospitare l'app di Funzioni di Azure. Diventerà anche il nome dell'app per le funzioni che contiene la funzione effettiva. È possibile scegliere un valore univoco o lasciare il suggerimento predefinito.
* Assicurarsi che **Sottoscrizione** corrisponda alla sottoscrizione da usare 
* Assicurarsi che **Gruppo di risorse** corrisponda al gruppo di risorse da usare
* Lasciare **Consumo** per *Tipo di piano*
* In **Località** selezionare la località che corrisponde al gruppo di risorse
* Creare una nuova risorsa **Archiviazione di Azure** usando il collegamento *Nuovo*. Impostare la località che corrisponde al gruppo di risorse, usare gli altri valori predefiniti e premere "OK".

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Pubblicare la funzione di Azure in Visual Studio: App per le funzioni (Windows) - Crea nuova":::

Scegliere quindi **Create** (Crea).

Verrà visualizzata di nuovo la pagina *Istanza di Funzioni*, in cui è ora visibile la nuova app per le funzioni sotto il gruppo di risorse. Premere *Fine*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Pubblicare la funzione di Azure in Visual Studio: Istanza di Funzioni (dopo l'app per le funzioni)":::

Nel riquadro *Pubblica* visualizzato di nuovo nella finestra principale di Visual Studio verificare che tutte le informazioni siano corrette e selezionare **Pubblica**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Pubblicare la funzione di Azure in Visual Studio: pubblicazione":::

> [!NOTE]
> È possibile che venga visualizzato un popup simile al seguente: :::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Pubblicare la funzione di Azure in Visual Studio: credenziali di pubblicazione" border="false":::
> In questo caso, selezionare **Tentativo di recuperare le credenziali da Azure** e quindi **Salva**.

### <a name="assign-permissions-to-the-function-app"></a>Assegnare le autorizzazioni all'app per le funzioni

Per consentire l'accesso dell'app per le funzioni a Gemelli digitali di Azure, il passaggio successivo consiste nel configurare un'impostazione dell'app, assegnare all'app un'identità di AAD gestita dal sistema e assegnare a questa identità le autorizzazioni di *proprietario* nell'istanza di Gemelli digitali di Azure.

In Azure Cloud Shell usare il comando seguente per configurare un'impostazione che verrà usata dall'app per le funzioni per fare riferimento all'istanza di gemelli digitali.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-digital-twin-instance-URL>"
```

Usare il comando seguente per creare l'identità gestita dal sistema. Prendere nota del campo *principalId* nell'output.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Usare il valore di *principalId* nel comando seguente per assegnare l'identità dell'app per le funzioni al ruolo di *proprietario* per l'istanza di Gemelli digitali di Azure:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Il risultato di questo comando è costituito dalle informazioni sull'assegnazione di ruolo creata. L'app per le funzioni ha ora le autorizzazioni per accedere all'istanza di Gemelli digitali di Azure.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Elaborare i dati di telemetria simulati da un dispositivo hub IoT

Un grafo di Gemelli digitali di Azure è progettato per essere basato sui dati di telemetria di dispositivi reali. 

In questo passaggio un termostato simulato registrato nell'[hub IoT](../iot-hub/about-iot-hub.md) verrà connesso al gemello digitale che lo rappresenta in Gemelli digitali di Azure. I dati di telemetria emessi dal dispositivo simulato verranno instradati tramite la funzione di Azure *ProcessHubToDTEvents* che attiva un aggiornamento corrispondente nel gemello digitale. In questo modo, il gemello digitale rimane aggiornato con i dati del dispositivo reale. In Gemelli digitali di Azure il processo di instradamento dei dati degli eventi da una posizione a un'altra è detto [**routing di eventi**](concepts-route-events.md).

Questo evento si verifica in questa parte dello scenario end-to-end (**freccia B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Estratto della rappresentazione grafica dello scenario edilizio completo che evidenzia la freccia B, gli elementi prima di Gemelli digitali di Azure: il dispositivo, l'hub IoT e la prima funzione di Azure":::

Ecco le azioni che verranno completate per configurare la connessione del dispositivo:
1. Creare un hub IoT che gestirà il dispositivo simulato
2. Connettere l'hub IoT alla funzione di Azure appropriata configurando una sottoscrizione di eventi
3. Registrare il dispositivo simulato nell'hub IoT
4. Eseguire il dispositivo simulato e generare dati di telemetria
5. Eseguire query su Gemelli digitali di Azure per visualizzare i risultati in tempo reale

### <a name="create-an-iot-hub-instance"></a>Creare un'istanza dell'hub IoT

Gemelli digitali di Azure è un servizio progettato per funzionare insieme all'[hub IoT](../iot-hub/about-iot-hub.md), un servizio di Azure per la gestione dei dispositivi e dei relativi dati. In questo passaggio verrà configurato un hub IoT che gestirà il dispositivo di esempio di questa esercitazione.

In Azure Cloud Shell usare questo comando per creare un nuovo hub IoT:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

L'output di questo comando è costituito da informazioni sull'hub IoT creato.

Salvare il nome assegnato all'hub IoT. sarà necessario più avanti.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Connettere l'hub IoT alla funzione di Azure

Connettere quindi l'hub IoT alla funzione di Azure *ProcessHubToDTEvents* nell'app per le funzioni pubblicata in precedenza, in modo che i dati possano essere trasmessi dal dispositivo nell'hub IoT tramite la funzione, che aggiorna Gemelli digitali di Azure.

A tale scopo, creare una **sottoscrizione di eventi** nell'hub IoT, con la funzione di Azure come endpoint. In questo modo si "sottoscrive" la funzione agli eventi che si verificano nell'hub IoT.

Nel [portale di Azure](https://portal.azure.com/) passare all'hub IoT appena creato cercandone il nome nella barra di ricerca in alto. Scegliere *Eventi* dal menu dell'hub e selezionare *+ Sottoscrizione di eventi*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Portale di Azure: sottoscrizione di eventi dell'hub IoT":::

Verrà visualizzata la pagina *Crea sottoscrizione di eventi*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Portale di Azure: Crea sottoscrizione di eventi":::

Compilare i campi come indicato di seguito:
* *DETTAGLI SOTTOSCRIZIONE EVENTO* > **Nome**: assegnare un nome alla sottoscrizione di eventi.
* *TIPI DI EVENTO* > **Filtra per tipi di evento**: selezionare *Messaggi di telemetria del dispositivo* nelle opzioni del menu.
* *DETTAGLI ENDPOINT* > **Tipo di endpoint** : selezionare *Funzione di Azure* nelle opzioni del menu.
* *DETTAGLI ENDPOINT* > **Endpoint**: scegliere il collegamento *Seleziona un endpoint*. Verrà aperta la finestra *Seleziona funzione di Azure*: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Sottoscrizione di eventi nel portale di Azure: Seleziona funzione di Azure" border="false":::
    - Compilare i campi **Sottoscrizione**, **Gruppo di risorse**, **App per le funzioni** e **Funzione** (*ProcessHubToDTEvents*). È possibile che alcuni di questi campi vengano prepopolati dopo aver selezionato la sottoscrizione.
    - Premere **Confermare la selezione**.

Di nuovo nella pagina *Crea sottoscrizione di eventi* premere **Crea**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Registrare il dispositivo simulato con l'hub IoT 

In questa sezione viene creata una rappresentazione del dispositivo nell'hub IoT con l'ID *thermostat67*. Il dispositivo simulato si connetterà a questa rappresentazione e in questo modo gli eventi di telemetria verranno trasferiti dal dispositivo all'hub IoT, in cui la funzione di Azure sottoscritta nel passaggio precedente resta in ascolto, pronta a prelevare gli eventi e a continuare l'elaborazione.

In Azure Cloud Shell creare un dispositivo nell'hub IoT con il comando seguente:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

L'output è costituito da informazioni sul dispositivo creato.

### <a name="configure-and-run-the-simulation"></a>Configurare ed eseguire la simulazione

Configurare quindi il simulatore del dispositivo per inviare dati all'istanza dell'hub IoT.

Per iniziare, ottenere la *stringa di connessione dell'hub IoT* con questo comando:

```azurecli
az iot hub show-connection-string -n <your-IoT-hub-name>
```

Quindi, ottenere la *stringa di connessione del dispositivo* con questo comando:

```azurecli
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Questi valori verranno inseriti nel codice del simulatore del dispositivo nel progetto locale per connettere il simulatore a questo hub IoT e al dispositivo hub IoT.

In una nuova finestra di Visual Studio, nella cartella della soluzione scaricata, aprire _DeviceSimulator > **DeviceSimulator.sln**_.

>[!NOTE]
> A questo punto dovrebbero essere aperte due finestre di Visual Studio, una con _**DeviceSimulator.sln**_ e una precedente con _**AdtE2ESample.sln**_.

Nel riquadro *Esplora soluzioni* di questa nuova finestra di Visual Studio selezionare _DeviceSimulator/**AzureIoTHub.cs**_ per aprirlo nella finestra di modifica. Sostituire i valori della stringa di connessione seguente con quelli raccolti prima:

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

Salvare il file.

A questo punto, per visualizzare i risultati della simulazione dei dati configurata, eseguire il progetto **DeviceSimulator** premendo questo pulsante sulla barra degli strumenti:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Pulsante di avvio di Visual Studio (progetto DeviceSimulator)":::

Verrà aperta una finestra della console che visualizza i messaggi di telemetria simulati sulle temperature. Questi messaggi vengono inviati all'hub IoT, dove vengono quindi prelevati ed elaborati dalla funzione di Azure.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Output della console del simulatore del dispositivo che mostra l'invio di dati di telemetria sulle temperature":::

Non è necessario eseguire altre operazioni in questa console, ma lasciarla in esecuzione mentre si completano i passaggi successivi.

### <a name="see-the-results-in-azure-digital-twins"></a>Visualizzare i risultati in Gemelli digitali di Azure

La funzione *ProcessHubToDTEvents* pubblicata in precedenza resta in ascolto dei dati dell'hub IoT e chiama un'API di Gemelli digitali di Azure per aggiornare la proprietà *Temperature* nel gemello *thermostat67*.

Per visualizzare i dati dal lato Gemelli digitali di Azure, passare alla finestra di Visual Studio in cui è aperto il progetto _**AdtE2ESample**_ ed eseguirlo.

Nella finestra della console del progetto che viene visualizzata eseguire il comando seguente per ottenere le temperature segnalate dal gemello digitale *thermostat67*:

```cmd
ObserveProperties thermostat67 Temperature
```

Verranno visualizzate le temperature aggiornate in tempo reale *dall'istanza di Gemelli digitali di Azure* registrate nella console ogni 10 secondi.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Output della console che mostra il log dei messaggi sulle temperature del gemello digitale thermostat67":::

Dopo averne verificato il corretto funzionamento, è possibile arrestare l'esecuzione di entrambi i progetti. Tenere aperte le finestre di Visual Studio, perché si continuerà a usarle nel resto dell'esercitazione.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Propagare gli eventi di Gemelli digitali di Azure tramite il grafo

Finora in questa esercitazione è stato illustrato come aggiornare Gemelli digitali di Azure da dati di dispositivi esterni. Successivamente, si vedrà come propagare le modifiche apportate a un gemello digitale tramite il grafo di Gemelli digitali di Azure, in altri termini come aggiornare i gemelli da dati interni al servizio.

A tale scopo, si userà la funzione di Azure *ProcessDTRoutedData* per aggiornare un gemello *Stanza* quando viene aggiornato il gemello *Termostato* connesso. Questo evento si verifica in questa parte dello scenario end-to-end (**freccia C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Estratto della rappresentazione grafica dello scenario edilizio completo che evidenzia la freccia C, gli elementi dopo Gemelli digitali di Azure: Griglia di eventi e la seconda funzione di Azure":::

Ecco le azioni che verranno completate per configurare questo flusso di dati:
1. Creare un endpoint di Gemelli digitali di Azure che connette l'istanza a Griglia di eventi
2. Configurare una route all'interno di Gemelli digitali di Azure per inviare gli eventi di modifica delle proprietà dei gemelli all'endpoint
3. Distribuire un'app di Funzioni di Azure che resta in ascolto (tramite [Griglia di eventi](../event-grid/overview.md)) sull'endpoint e aggiorna gli altri gemelli di conseguenza
4. Eseguire il dispositivo simulato e sottoporre a query Gemelli digitali di Azure per vedere i risultati in tempo reale

### <a name="set-up-endpoint"></a>Configurare l'endpoint

[Griglia di eventi](../event-grid/overview.md) è un servizio di Azure che consente di instradare e recapitare gli eventi provenienti da servizi di Azure ad altre posizioni all'interno di Azure. È possibile creare un [argomento di Griglia di eventi](../event-grid/concepts.md) per raccogliere determinati eventi da un'origine. Quindi, i sottoscrittori possono restare in ascolto sull'argomento per ricevere gli eventi non appena vengono inviati.

In questa sezione viene creato un argomento di Griglia di eventi, quindi viene creato un endpoint all'interno di Gemelli digitali di Azure che punta (invia eventi) a tale argomento. 

In Azure Cloud Shell eseguire il comando seguente per creare un argomento di Griglia di eventi:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Per restituire un elenco di nomi di aree di Azure che possono essere passati ai comandi nell'interfaccia della riga di comando di Azure, eseguire questo comando:
> ```azurecli
> az account list-locations -o table
> ```

L'output di questo comando è costituito da informazioni sull'argomento di Griglia di eventi creato.

Successivamente, creare un endpoint di Gemelli digitali di Azure che punta all'argomento di Griglia di eventi. Usare il comando seguente, inserendo i valori necessari nei campi segnaposto:

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

L'output di questo comando è costituito da informazioni sull'endpoint creato.

È anche possibile verificare se la creazione dell'endpoint è stata completata eseguendo il comando seguente per eseguire query sull'istanza di Gemelli digitali di Azure e cercare l'endpoint:

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Cercare il campo `provisioningState` nell'output e verificare che il valore sia "Succeeded".

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Risultato della query che mostra l'endpoint con provisioningState Succeeded":::

Salvare i nomi assegnati all'argomento di Griglia di eventi e all'endpoint di Gemelli digitali di Azure. Verranno usati in seguito.

### <a name="set-up-route"></a>Configurare la route

Successivamente, creare una route di Gemelli digitali di Azure che invia eventi all'endpoint di Gemelli digitali di Azure appena creato.

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

L'output di questo comando è costituito da alcune informazioni sulla route creata.

#### <a name="connect-the-function-to-event-grid"></a>Connettere la funzione a Griglia di eventi

Successivamente, sottoscrivere la funzione di Azure *ProcessDTRoutedData* all'argomento di Griglia di eventi creato in precedenza, in modo che i dati di telemetria possano essere trasferiti dal gemello *thermostat67* attraverso l'argomento di Griglia di eventi alla funzione, che torna in Gemelli digitali di Azure e aggiorna il gemello *room21* di conseguenza.

A tale scopo, creare una **sottoscrizione di Griglia di eventi** dall'argomento di Griglia di eventi alla funzione di Azure *ProcessDTRoutedData* come endpoint.

Nel [portale di Azure](https://portal.azure.com/) passare all'argomento di Griglia di eventi cercandone il nome nella barra di ricerca in alto. Selezionare *+ Sottoscrizione di eventi*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Portale di Azure: Sottoscrizione di eventi di Griglia di eventi":::

La procedura per creare questa sottoscrizione di eventi è simile a quella eseguita per sottoscrivere la prima funzione di Azure all'hub IoT in precedenza in questa esercitazione. La differenza è che questa volta non è necessario specificare *Messaggi di telemetria del dispositivo* come tipo di evento di cui restare in ascolto e ci si connetterà a una funzione di Azure diversa.

Ecco di nuovo i passaggi per la sottoscrizione:

Nella pagina *Crea sottoscrizione di eventi* compilare i campi come segue:
* *DETTAGLI SOTTOSCRIZIONE EVENTO* > **Nome**: assegnare un nome alla sottoscrizione di eventi.
* *DETTAGLI ENDPOINT* > **Tipo di endpoint** : selezionare *Funzione di Azure* nelle opzioni del menu.
* *DETTAGLI ENDPOINT* > **Endpoint**: scegliere il collegamento *Seleziona un endpoint*. Verrà aperta la finestra *Seleziona funzione di Azure*:
    - Compilare i campi **Sottoscrizione**, **Gruppo di risorse**, **App per le funzioni** e **Funzione** (*ProcessDTRoutedData*). È possibile che alcuni di questi campi vengano prepopolati dopo aver selezionato la sottoscrizione.
    - Premere **Confermare la selezione**.

Di nuovo nella pagina *Crea sottoscrizione di eventi* premere **Crea**.

### <a name="run-the-simulation-and-see-the-results"></a>Eseguire la simulazione e visualizzare i risultati

A questo punto è possibile eseguire il simulatore del dispositivo per avviare il nuovo flusso di eventi configurato. Passare alla finestra di Visual Studio in cui è aperto il progetto _**DeviceSimulator**_, quindi eseguirlo.

Così come quando è stato eseguito il simulatore del dispositivo in precedenza, verrà aperta una finestra della console che visualizza i messaggi di telemetria simulati sulle temperature. Questi eventi passano attraverso il flusso configurato in precedenza per aggiornare il gemello *thermostat67* e quindi continuano a seguire il flusso configurato di recente per aggiornare il gemello *room21* di conseguenza.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Output della console del simulatore del dispositivo che mostra l'invio di dati di telemetria sulle temperature":::

Non è necessario eseguire altre operazioni in questa console, ma lasciarla in esecuzione mentre si completano i passaggi successivi.

Per visualizzare i dati dal lato Gemelli digitali di Azure, passare alla finestra di Visual Studio in cui è aperto il progetto _**AdtE2ESample**_ ed eseguirlo.

Nella finestra della console del progetto che viene visualizzata eseguire il comando seguente per ottenere le temperature segnalate **sia** dal gemello digitale *thermostat67* che dal gemello digitale *room21*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Verranno visualizzate le temperature aggiornate in tempo reale *dall'istanza di Gemelli digitali di Azure* registrate nella console ogni 10 secondi. Si noti che la temperatura per *room21* viene aggiornata in base agli aggiornamenti di *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Output della console che mostra il log dei messaggi sulle temperature del termostato e della stanza":::

Dopo averne verificato il corretto funzionamento, è possibile arrestare l'esecuzione di entrambi i progetti. È anche possibile chiudere le finestre di Visual Studio, in quanto l'esercitazione è stata completata.

## <a name="review"></a>Verifica

Ecco una revisione dello scenario creato in questa esercitazione.

1. Un'istanza di Gemelli digitali di Azure rappresenta digitalmente un piano, una stanza e un termostato (rappresentati dalla **sezione A** nel diagramma seguente)
2. I dati di telemetria del dispositivo simulato vengono inviati all'hub IoT, dove la funzione di Azure *ProcessHubToDTEvents* resta in ascolto di eventi di telemetria. La funzione di Azure *ProcessHubToDTEvents* usa le informazioni contenute in questi eventi per impostare la proprietà *Temperature* in *thermostat67* (**freccia B** nel diagramma).
3. Gli eventi di modifica delle proprietà in Gemelli digitali di Azure vengono instradati a un argomento di Griglia di eventi, dove la funzione di Azure *ProcessDTRoutedData* resta in ascolto di eventi. La funzione di Azure *ProcessDTRoutedData* usa le informazioni di questi eventi per impostare la proprietà *Temperature* in *room21* (**freccia C** nel diagramma).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Rappresentazione grafica dello scenario edilizio. Illustra il flusso dei dati da un dispositivo all'hub IoT, a un'istanza di Gemelli digitali di Azure tramite una funzione di Azure (sezione A), quindi a un'altra funzione di Azure per l'elaborazione tramite Griglia di eventi (freccia C)":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questa esercitazione non sono più necessarie, seguire questa procedura per eliminarle. 

Usando Azure Cloud Shell, è possibile eliminare tutte le risorse di Azure di un gruppo di risorse con il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Questo comando rimuove il gruppo di risorse, l'istanza di Gemelli digitali di Azure, l'hub IoT e la registrazione del dispositivo hub, l'argomento di Griglia di eventi e le sottoscrizioni associate, nonché entrambe le app di Funzioni di Azure, incluse le risorse associate, come l'archiviazione.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare quindi la registrazione dell'app di AAD creata per l'app client con questo comando:

```azurecli
az ad app delete --id <your-application-ID>
```

Infine, eliminare la cartella dell'esempio di progetto scaricata dal computer locale.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato uno scenario end-to-end che mostra un'istanza di Gemelli digitali di Azure basata su dati di dispositivo in tempo reale.

Quindi, iniziare a esaminare la documentazione concettuale per altre informazioni sugli elementi usati nell'esercitazione:
* [Concetti: Modelli personalizzati](concepts-models.md)

Oppure approfondire ulteriormente i processi illustrati in questa esercitazione a partire dagli articoli sulle procedure:
* [Procedura: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure](how-to-use-cli.md)
