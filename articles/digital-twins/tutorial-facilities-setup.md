---
title: Distribuire Gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come distribuire un'istanza di Gemelli digitali di Azure e configurare le risorse spaziali seguendo i passaggi descritti in questa esercitazione.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363334"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Esercitazione: Distribuire Gemelli digitali di Azure e configurare un grafico spaziale

Il servizio Gemelli digitali di Azure consente di riunire persone, luoghi e dispositivi in un sistema spaziale coerente. Questa serie di esercitazioni illustra come usare Gemelli digitali di Azure per determinare l'occupazione di una stanza con condizioni ottimali di temperatura e qualità dell'aria. Queste esercitazioni illustrano in modo dettagliato un'applicazione console .NET per creare uno scenario costituito da un palazzo di uffici, con più piani e stanze su ogni piano. Le stanze contengono dispositivi, con sensori collegati che rilevano il movimento, la temperatura ambiente e la qualità dell'aria. Si apprenderà come replicare le entità e le aree fisiche nell'edificio come oggetti digitali usando il servizio Gemelli digitali. Verranno simulati gli eventi dei dispositivi usando un'altra applicazione console. Si apprenderà quindi come monitorare gli eventi provenienti da tali entità e aree fisiche quasi in tempo reale. Un amministratore di un ufficio può usare queste informazioni per aiutare un dipendente che lavora in questo edifico a prenotare le sale riunioni con condizioni ottimali. Un responsabile delle strutture dell'ufficio può usare la configurazione per rilevare le tendenze di utilizzo delle stanze e monitorare le condizioni lavorative a scopo di manutenzione.

Nella prima esercitazione di questa serie si apprenderà come:

> [!div class="checklist"]
> * Distribuire Gemelli digitali
> * Concedere autorizzazioni all'app
> * Modificare l'app di esempio di Gemelli digitali
> * Effettuare il provisioning dell'edificio


Queste esercitazioni usano e modificano gli stessi esempi usati nella [guida introduttiva alla ricerca di stanze disponibili](quickstart-view-occupancy-dotnet.md) per fornire informazioni più dettagliate e approfondite sui concetti.


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Gli esempi di Gemelli digitali usati in queste esercitazioni sono scritti in C#. Assicurarsi di installare [.NET Core SDK versione 2.1.403 o successiva](https://www.microsoft.com/net/download) nel computer di sviluppo per compilare ed eseguire l'esempio. Controllare se nel computer è installata la versione corretta eseguendo `dotnet --version` in una finestra di comando.

- [Visual Studio Code](https://code.visualstudio.com/) per esplorare il codice di esempio. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Distribuire Gemelli digitali

Usare i passaggi in questa sezione per creare una nuova istanza del servizio Gemelli digitali. È possibile creare una sola istanza per sottoscrizione. Se è già presente un'istanza in esecuzione, passare alla sezione successiva. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Concedere autorizzazioni all'app

Gemelli digitali usa [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) per controllare l'[accesso in lettura/scrittura](../active-directory/develop/v1-permissions-and-consent.md) al servizio. Qualsiasi applicazione che deve connettersi con l'istanza di Gemelli digitali deve essere registrata con Azure Active Directory. I passaggi in questa sezione illustrano come registrare l'app di esempio.

Se si dispone già di una *registrazione dell'app*, è possibile riutilizzarla per l'esempio. Esaminare tuttavia questa sezione per assicurarsi che la registrazione dell'app sia configurata correttamente.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Configurare l'esempio di Gemelli digitali

Questa sezione descrive in modo dettagliato un'applicazione di Gemelli digitali che comunica con le [API REST di Gemelli digitali](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Scaricare l'esempio
Se sono già stati scaricati gli esempi per la [guida introduttiva alla ricerca di stanze disponibili](quickstart-view-occupancy-dotnet.md), è possibile ignorare questi passaggi.

1. Scaricare gli [esempi .NET di Gemelli digitali](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Estrarre il contenuto della cartella ZIP nel computer. 

### <a name="explore-the-sample"></a>Esaminare l'esempio
Nella cartella degli esempi estratta, aprire il file **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** in Visual Studio Code. La cartella contiene due progetti: 

1. L'esempio di provisioning **_occupancy-quickstart_** consente di effettuare la configurazione e il provisioning di un [grafico di intelligenza spaziale](concepts-objectmodel-spatialgraph.md#graph), ovvero l'immagine digitalizzata degli spazi fisici e delle risorse in essi contenute. Viene usato un [modello a oggetti](concepts-objectmodel-spatialgraph.md#model) che definisce gli oggetti per un edificio intelligente. Per un elenco completo delle API REST e degli oggetti di Gemelli digitali, vedere la [documentazione delle API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) o l'URL dell'**API Gestione** creato per l'[istanza](#deploy) in uso.

   Per esaminare l'esempio e capire come avviene la comunicazione con l'istanza di Gemelli digitali, è possibile iniziare dalla cartella **_src\actions_**. I file in questa cartella implementano i comandi che verranno usati in queste esercitazioni:
    - Il file *provisionSample.cs* mostra come effettuare il provisioning del grafico spaziale.
    - Il file *getSpaces.cs* ottiene informazioni sugli spazi di cui è stato effettuato il provisioning.
    - Il file *getAvailableAndFreshSpaces.cs* ottiene i risultati di una funzione personalizzata detta funzione definita dall'utente.
    - Il file *createEndpoints.cs* crea gli endpoint per l'interazione con altri servizi.

1. L'esempio di simulazione **_device-connectivity_** simula i dati dei sensori e li invia all'hub IoT di cui è stato effettuato il provisioning per l'istanza di gemello digitale. L'esempio verrà usato nell'[esercitazione successiva dopo avere effettuato il provisioning del grafico spaziale](tutorial-facilities-udf.md#simulate). Gli identificatori di sensori e dispositivi usati per configurare questo esempio devono corrispondere a quelli usati per il provisioning del grafico.

### <a name="configure-the-provisioning-sample"></a>Configurare l'esempio di provisioning
1. Aprire una finestra di comando e passare all'esempio scaricato. Eseguire il comando seguente:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Ripristinare le dipendenze dal progetto di esempio eseguendo questo comando:

    ```cmd/sh
    dotnet restore
    ```

1. In Visual Studio Code aprire il file **_appSettings.json_** del progetto **occupancy-quickstart** e aggiornare i valori seguenti:
    1. *ClientId*: immettere l'**ID applicazione** dell'app registrata in AAD, annotato nella sezione relativa all'[impostazione delle autorizzazioni dell'app](#permissions).
    1. *Tenant*: immettere l'**ID directory** del [tenant AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant), annotato anch'esso nella sezione relativa all'[impostazione delle autorizzazioni dell'app](#permissions).
    1. *BaseUrl*: immettere l'URL dell'istanza di Gemelli digitali. Per ottenere questo URL, sostituire i segnaposto nell'URL con i valori per l'istanza: **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/**. È anche possibile ottenere l'URL modificando l'URL dell'**API Gestione** della [sezione relativa alla distribuzione](#deploy), sostituendo **swagger/** con **api/v1.0/**.

1. Per visualizzare un elenco delle funzionalità di Gemelli digitali che è possibile esplorare usando l'esempio, eseguire il comando seguente.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>Informazioni sul processo di provisioning
Questa sezione illustra in che modo l'esempio effettua il provisioning di un grafico spaziale di un edificio. 

In Visual Studio Code passare alla cartella **_occupancy-quickstart\src\actions_** e aprire il file *provisionSample.cs*. Osservare la funzione seguente:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Questa funzione usa il file *provisionSample.yaml* nella stessa cartella. Aprire questo file e osservare la gerarchia di un palazzo di uffici, costituita da sede, piano, area e stanze (oggetti *Venue*, *Floor*, *Area* e *Rooms*). Ognuno di questi spazi fisici può contenere dispositivi e sensori (oggetti *devices* e *sensors*). Ogni elemento ha un valore `type` predefinito, ad esempio *Floor*, *Room*. 

Il file *yaml* di esempio mostra un grafico spaziale tramite il modello a oggetti `Default` di Gemelli digitali. Questo modello fornisce nomi generici per la maggior parte dei tipi (ad esempio Temperature per SensorDataType, Map per SpaceBlobType) e tipi di spazio (ad esempio Room con i sottotipi FocusRoom, ConferenceRoom e così via) e ciò è sufficiente per un edificio. Se fosse necessario creare un grafico spaziale per una sede di diverso tipo, ad esempio una fabbrica, potrebbe essere necessario un modello a oggetti diverso. Per visualizzare i modelli disponibili, eseguire il comando `dotnet run GetOntologies` alla riga di comando per l'esempio di provisioning. Per altre informazioni sui grafici spaziali e sui modelli a oggetti, vedere [Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-sample-spatial-graph"></a>Modificare il grafico spaziale di esempio
Il file *provisionSample.yaml* contiene i nodi seguenti:

- **resources**: il nodo `resources` crea una risorsa dell'hub IoT per comunicare con i dispositivi configurati. Un hub IoT nel nodo radice del grafico può comunicare con tutti i dispositivi e i sensori nel grafico.  

- **spaces**: nel modello a oggetti di Gemelli digitali `spaces` rappresenta i luoghi fisici. Ogni spazio ha un valore `Type`, ad esempio *Region*, *Venue* o *Customer* e un oggetto `Name` descrittivo. Gli spazi possono appartenere ad altri spazi e viene così creata una struttura gerarchica. Il file *provisionSample.yaml* include un grafico spaziale di un edificio immaginario. Si noti l'annidamento logico degli spazi di tipo `Floor` all'interno di `Venue`, `Area` in un oggetto Floor e nodi `Room` in un oggetto Area. 

- **devices**: gli spazi possono contenere oggetti `devices`, che sono entità fisiche o virtuali che gestiscono diversi sensori. Un dispositivo può ad esempio essere il telefono di un utente, un pod di sensori Raspberry Pi, un gateway e così via. Nell'edificio immaginario di esempio osservare che la stanza denominata *Focus Room* contiene un dispositivo *Raspberry Pi 3 A1*. Ogni nodo di un dispositivo è identificato da un valore `hardwareId` univoco, che è hardcoded nell'esempio. Per configurare questo esempio per un ambiente di produzione reale, sostituire questi valori con quelli della configurazione in uso.  

- **sensors**: un dispositivo può contenere più oggetti `sensors`, che possono rilevare e registrare i cambiamenti fisici, ad esempio per quanto riguarda temperatura, movimento, livello delle batterie e così via. Ogni nodo di un sensore è identificato da un valore `hardwareId` univoco hardcoded. Per un'applicazione reale, sostituire questi valori con gli identificatori univoci dei sensori nella configurazione in uso. Il file *provisionSample.yaml* include due sensori per la registrazione dei valori di *Motion* e *CarbonDioxide*. Un altro sensore registra i valori di *Temperature*, aggiungendo le righe seguenti sotto a quelle per il sensore relativo a CarbonDioxide. Si noti che queste righe vengono fornite nel file *provisionSample.yaml* come righe impostate come commento. È sufficiente eliminare il carattere "#" all'inizio di ogni riga per rimuovere il commento. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Assicurarsi che le chiavi `dataType` e `hardwareId` siano allineate con le istruzioni sopra questo frammento di codice. Assicurarsi inoltre che l'editor non sostituisca gli spazi con tabulazioni. 

Salvare e chiudere il file *provisionSample.yaml*. Nella prossima esercitazione verranno aggiunte altre informazioni a questo file e quindi si effettuerà il provisioning dell'edificio di esempio di Gemelli digitali di Azure.


## <a name="clean-up-resources"></a>Pulire le risorse

Se si non si vuole esplorare ulteriormente Gemelli digitali di Azure, è possibile eliminare le risorse create in questa esercitazione:

1. Nel [portale di Azure](http://portal.azure.com) fare clic su **Tutte le risorse** nel menu a sinistra, selezionare il gruppo di risorse di Gemelli digitali e quindi fare clic su **Elimina**.
2. Se necessario, è possibile procedere anche all'eliminazione dell'applicazione di esempio dal computer di lavoro. 


## <a name="next-steps"></a>Passaggi successivi

Passare alla prossima esercitazione della serie per informazioni su come implementare una logica personalizzata per il monitoraggio delle condizioni nell'edificio di esempio. 
> [!div class="nextstepaction"]
> [Esercitazione: Effettuare il provisioning dell'edificio e monitorare le condizioni di lavoro](tutorial-facilities-udf.md)

