---
title: Usare il modello di Resource Manager per pubblicare l'hub IoT e l'account di archiviazione e instradare i messaggi
description: Usare il modello di Resource Manager per pubblicare l'hub IoT e l'account di archiviazione e instradare i messaggi
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 4112e8aae485e229beb16d21e90280750e1465e1
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462667"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Avvio rapido: Distribuire un hub IoT di Azure e un account di archiviazione con un modello di Resource Manager

In questo argomento di avvio rapido si usa un modello di Azure Resource Manager per creare un hub IoT per l'instradamento dei messaggi ad Archiviazione di Azure e un account di archiviazione per la conservazione dei messaggi. Dopo aver aggiunto manualmente un dispositivo IoT virtuale all'hub per l'invio dei messaggi, è necessario configurare tali informazioni di connessione in un'applicazione denominata *arm-read-write* per inviare i messaggi dal dispositivo all'hub. L'hub viene configurato in modo che i messaggi inviati all'hub vengano instradati automaticamente all'account di archiviazione. Al termine di questo argomento di avvio rapido, è possibile aprire l'account di archiviazione e visualizzare i messaggi inviati.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo argomento di avvio rapido è denominato `101-iothub-auto-route-messages` ed è incluso nei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Nel modello sono definite due risorse di Azure: 
* [Microsoft.Devices/Iothubs](/azure/templates/microsoft.devices/iothubs)
* [Microsoft.Storage/](/azure/templates/microsoft.storage/allversions)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Distribuire il modello ed eseguire l'app di esempio

Questa sezione illustra la procedura per distribuire il modello, creare un dispositivo virtuale ed eseguire l'applicazione arm-read-write per inviare i messaggi.

1. Creare le risorse distribuendo il modello di Resource Manager.

    > [!TIP]
    > Selezionare il pulsante in basso per avviare la distribuzione del modello. Mentre è in esecuzione, configurare l'applicazione arm-read-write per l'esecuzione.

    [![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Scaricare e decomprimere gli [esempi C# per IoT](https://docs.microsoft.com/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Aprire una finestra di comando e passare alla cartella in cui sono stati decompressi gli esempi di C# per IoT. Trovare la cartella con il file arm-read-write.csproj. Le variabili di ambiente vengono create in questa finestra di comando. Accedere al [portale di Azure](https://portal.azure.com ) per ottenere le chiavi. Selezionare **Gruppi di risorse** e quindi il gruppo di risorse usato per questo argomento di avvio rapido.

   ![Selezionare il gruppo di risorse](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Vengono visualizzati l'hub IoT e l'account di archiviazione che sono stati creati durante la distribuzione del modello di Resource Manager. Prima di continuare, attendere il completamento della distribuzione del modello. Selezionare quindi il gruppo di risorse per visualizzare le risorse.

   ![Visualizzare le risorse nel gruppo di risorse](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. È necessario il **nome dell'hub**. Selezionare l'hub nell'elenco delle risorse. Copiare negli Appunti di Windows il nome dell'hub visualizzato nella parte superiore della sezione Hub IoT. 
 
   ![Copiare il nome dell'hub](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Sostituire il nome dell'hub in questo comando dove indicato e quindi eseguire questo comando nella finestra di comando:
   
    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   che sarà simile a questo esempio:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. La variabile di ambiente successiva è la chiave del dispositivo IoT. Per aggiungere un nuovo dispositivo all'hub, selezionare **Dispositivi IoT** nel menu Hub IoT per l'hub. 

   ![Selezionare i dispositivi IoT](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Nella parte destra della schermata selezionare **+ NUOVO** per aggiungere un nuovo dispositivo. 

   Immettere il nome del nuovo dispositivo. In questo argomento di avvio rapido si usa un nome che inizia con **Contoso-Test-Device**. Salvare il dispositivo e quindi aprire di nuovo la schermata per recuperare la chiave del dispositivo. La chiave viene generata automaticamente quando si chiude il riquadro. Selezionare la chiave primaria o secondaria e copiarla negli Appunti di Windows. Nella finestra di comando impostare il comando da eseguire e quindi premere **INVIO**. Il comando dovrebbe essere simile al seguente, ma contenere anche la chiave del dispositivo incollata:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. L'ultima variabile di ambiente è l'**ID dispositivo**. Nella finestra di comando configurare il comando ed eseguirlo. 
   
   ```cms
   SET IOT_DEVICE_ID=<device-id-goes-here> 
   ```

   che sarà simile a questo esempio:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Per visualizzare le variabili di ambiente definite, digitare SET nella riga di comando, premere **INVIO** e quindi cercare quelle che iniziano con **IoT**.

   ![Visualizzare le variabili di ambiente](./media/horizontal-arm-route-messages/06-environment-variables.png)

Dopo aver impostato le variabili di ambiente eseguire l'applicazione dalla stessa finestra di comando. Dal momento che si usa la stessa finestra, le variabili saranno accessibili in memoria quando si esegue l'applicazione.

1. Per eseguire l'applicazione, digitare il comando seguente nella finestra di comando e premere **INVIO**.

    `dotnet run arm-read-write`

   L'applicazione genera e visualizza i messaggi nella console ogni volta che invia un messaggio all'hub IoT. Nel modello di Resource Manager l'hub è stato configurato per il routing automatizzato. I messaggi che contengono il testo "level = storage" vengono instradati automaticamente all'account di archiviazione. Lasciare in esecuzione l'app per 10-15 minuti e quindi premere **INVIO** una o due volte finché l'esecuzione non viene arrestata.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare prima il gruppo di risorse e quindi l'account di archiviazione.

1. Eseguire il drill-down nell'account di archiviazione fino a trovare i file.

   ![Esaminare i file dell'account di archiviazione](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Selezionare uno dei file, scegliere **Scarica** e scaricare il file in un percorso accessibile in un secondo momento. Il nome del file sarà numerico, ad esempio 47. Aggiungere ". txt" alla fine del nome e quindi fare doppio clic sul file per aprirlo.

1. Quando si apre il file, ogni riga è riferita a un messaggio diverso. Viene crittografato anche il corpo dei singoli messaggi. Questa operazione è necessaria per consentire l'esecuzione di query sul corpo del messaggio.

   ![Visualizzare i messaggi inviati](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Questi messaggi vengono codificati in formato UTF-32 e Base64. Se si legge di nuovo il messaggio, è necessario decodificarlo dal formato Base64 e UTF-32 per leggerlo in formato ASCII. Se si è interessati, è possibile usare il metodo ReadOneRowFromFile descritto nell'esercitazione sul routing per leggere uno alla volta questi file di messaggio e decodificarli in formato ASCII. ReadOneRowFromFile è disponibile nel repository degli esempi C# per IoT che è stato decompresso per questo argomento di avvio rapido. Ecco il percorso dalla radice della cartella: *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs*. Impostare il valore booleano `readTheFile` su true e impostare come hardcoded il percorso del file su disco. Il file verrà aperto e la prima riga del file verrà convertita.

È stato distribuito un modello di Resource Manager per creare un hub e un account di archiviazione ed è stato eseguito un programma per inviare messaggi all'hub. I messaggi vengono quindi archiviati automaticamente nell'account di archiviazione in cui possono essere visualizzati.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Per rimuovere le risorse aggiunte per questo argomento di avvio rapido, accedere al [portale di Azure](https://portal.azure.com). Selezionare **Gruppi di risorse** e quindi trovare il gruppo di risorse usato per questo argomento di avvio rapido. Selezionare il gruppo di risorse e quindi *Elimina*. Tutte le risorse presenti nel gruppo verranno eliminate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare e distribuire il primo modello di Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
