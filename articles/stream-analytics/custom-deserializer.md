---
title: 'Esercitazione: Deserializzatori .NET personalizzati per i processi cloud di Analisi di flusso di Azure'
description: Questa esercitazione illustra come creare un deserializzatore .NET personalizzato per un processo cloud di Analisi di flusso di Azure con Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: f5fa0a4398c904113dbce5d80844b42b6e775df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702431"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Esercitazione: Deserializzatori .NET personalizzati per Analisi di flusso di Azure

Analisi di flusso di Azure offre [supporto incorporato per tre formati di dati](stream-analytics-parsing-json.md): JSON, CSV e Avro. Con i deserializzatori .NET personalizzati, è possibile leggere dati da altri formati, ad esempio [buffer di protocollo](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) e altri formati definiti dall'utente per i processi cloud ed Edge.

Questa esercitazione illustra come creare un deserializzatore .NET personalizzato per un processo cloud di Analisi di flusso di Azure con Visual Studio. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un deserializzatore personalizzato per il buffer di protocollo.
> * Creare un processo di Analisi di flusso di Azure in Visual Studio.
> * Configurare un processo di analisi di flusso per l'uso del deserializzatore personalizzato.
> * Eseguire un processo di analisi di flusso per testare il deserializzatore personalizzato.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) o [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata.

* [Installare gli strumenti di analisi di flusso per Visual Studio](stream-analytics-tools-for-visual-studio-install.md) o eseguire l'aggiornamento all'ultima versione. Sono supportate le versioni seguenti di Visual Studio:
   * Visual Studio 2015
   * Visual Studio 2017

* Aprire **Cloud Explorer** in Visual Studio e accedere alla propria sottoscrizione di Azure.

* Creare un contenitore nell'account di Archiviazione di Azure.
Il contenitore creato verrà usato per archiviare gli asset correlati al processo di analisi di flusso. Se è già disponibile un account di archiviazione con contenitori esistenti, è possibile usarli. In caso contrario, sarà necessario [creare un nuovo contenitore](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Creare un deserializzatore personalizzato

1. Aprire Visual Studio e selezionare **File -> Nuovo -> Progetto**. Cercare **Analisi di flusso** e selezionare **Azure Stream Analytics Custom Deserializer Project (.NET)** (Progetto deserializzatore personalizzato di Analisi di flusso di Azure - .NET). Assegnare un nome al progetto, ad esempio **Protobuf Deserializer**.

   ![Creare un progetto libreria di classi standard di Visual Studio dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. Da Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Protobuf Deserializer** e scegliere **Gestisci pacchetti NuGet** dal menu. Installare quindi i pacchetti NuGet **Microsoft.Azure.StreamAnalytics** e **Google.Protobuf**.

3. Aggiungere la [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e la [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) al progetto.

4. Compilare il progetto **Protobuf Deserializer**.

## <a name="add-an-azure-stream-analytics-project"></a>Aggiungere un progetto di Analisi di flusso di Azure

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **Protobuf Deserializer** e scegliere **Aggiungi > Nuovo progetto**. In **Analisi di flusso di Azure > Analisi di flusso** scegliere **Applicazione Analisi di flusso di Azure**. Denominarla **ProtobufCloudDeserializer** e scegliere **OK**. 

2. Fare clic con il pulsante destro del mouse su **Riferimenti** nel progetto di Analisi di flusso di Azure **ProtobufCloudDeserializer**. In **Progetti** aggiungere **Protobuf Deserializer**. Dovrebbe popolarsi automaticamente.

## <a name="configure-a-stream-analytics-job"></a>Configurare un processo di analisi di flusso

1. Fare doppio clic su **JobConfig.json**. Usare le configurazioni predefinite, tranne per le impostazioni seguenti:

   |Impostazione|Valore consigliato|
   |-------|---------------|
   |Global Storage Settings Resource (Risorsa impostazioni di archiviazione globali)|Scegliere l'origine dati dall'account corrente|
   |Global Storage Settings Subscription (Sottoscrizione impostazioni di archiviazione globali)| < sottoscrizione >|
   |Global Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione globali)| < account di archiviazione >|
   |Custom Code Storage Settings Resource (Risorsa impostazioni di archiviazione codice personalizzato)|Scegliere l'origine dati dall'account corrente|
   |Custom Code Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione codice personalizzato)|< account di archiviazione >|
   |Custom Code Storage Settings Container (Contenitore impostazioni di archiviazione codice personalizzato)|< contenitore di archiviazione >|

2. In **Input** fare doppio clic su **Input.json**. Usare le configurazioni predefinite, tranne per le impostazioni seguenti:

   |Impostazione|Valore consigliato|
   |-------|---------------|
   |Source (Sorgente)|Archiviazione BLOB|
   |Risorsa|Scegliere l'origine dati dall'account corrente|
   |Subscription|< sottoscrizione >|
   |Account di archiviazione|< account di archiviazione >|
   |Contenitore|< contenitore di archiviazione >|
   |Formato di serializzazione eventi|Altro (Protobuf, XML, proprietario...)|
   |Risorsa|Caricare dal riferimento al progetto ASA o CodeBehind|
   |Nome dell'assembly CSharp|ProtobufDeserializer.dll|
   |Nome della classe|MessageBodyProto.MessageBodyDeserializer|
   |Tipo di compressione eventi|Nessuna|

3. Aggiungere la query seguente al file **Script.asaql**.

   ```sql
   SELECT * FROM Input
   ```

4. Scaricare il [file di input protobuf di esempio](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Nella cartella **Input** fare clic con il pulsante destro del mouse su **Input.json** e scegliere **Aggiungi input locale**. Fare doppio clic quindi su **local_Input.json** e configurare le impostazioni seguenti:

   |Impostazione|Valore consigliato|
   |-------|---------------|
   |Alias di input|Input|
   |Tipo di origine|Flusso dati|
   |Formato di serializzazione eventi|Altro (Protobuf, XML, proprietario...)|
   |Nome dell'assembly CSharp|ProtobufDeserializer.dll|
   |Nome della classe|MessageBodyProto.MessageBodyDeserializer|
   |Percorso del file di input locale|< percorso del file di input protobuf di esempio scaricato >|

## <a name="execute-the-stream-analytics-job"></a>Eseguire il processo di analisi di flusso

1. Aprire **script.asaql** e selezionare **Esegui localmente**.

2. Osservare i risultati in **Stream Analytics Local Run Results** (Risultati esecuzione locale analisi di flusso).

Il deserializzatore personalizzato per il processo di analisi di flusso è stato implementato correttamente. In questa esercitazione è stato testato in locale il deserializzatore personalizzato. Per i dati effettivi, è necessario configurare correttamente l'input e l'output. Inviare quindi il processo ad Azure da Visual Studio per eseguirlo nel cloud usando il deserializzatore personalizzato appena implementato.

## <a name="debug-your-deserializer"></a>Eseguire il debug del deserializzatore

È possibile eseguire il debug del deserializzatore .NET in locale esattamente come si esegue il debug di codice .NET standard. 

1. Aggiungere punti di interruzione nella funzione.

2. Premere **F5** per avviare il debug. Il programma verrà interrotto in corrispondenza dei punti di interruzione nel modo previsto.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming utilizzate dal processo. Se si prevede di usare il processo in futuro, è possibile arrestarlo e riavviarlo in un secondo momento, quando è necessario. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa esercitazione seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi selezionare il nome della risorsa creata.  

2. Nella pagina del gruppo di risorse selezionare **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come implementare un deserializzatore .NET personalizzato per la serializzazione dell'input del buffer di protocollo. Per altre informazioni sulla creazione di deserializzatori personalizzati, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Creare deserializzatori .NET diversi per i processi di Analisi di flusso di Azure](custom-deserializer-examples.md)
