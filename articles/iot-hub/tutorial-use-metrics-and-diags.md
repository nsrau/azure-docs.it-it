---
title: Configurare e usare le metriche e i log di diagnostica con un hub IoT di Azure | Microsoft Docs
description: Configurare e usare le metriche e i log di diagnostica con un hub IoT di Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 7349287945a56bb7674e364f515d0b763015ed59
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262309"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Esercitazione: Configurare e usare le metriche e i log di diagnostica con un hub IoT

Se si dispone di una soluzione hub IoT in esecuzione nell'ambiente di produzione, è possibile configurare alcune metriche e abilitare i log di diagnostica. Se si verifica un problema, sarà quindi possibile esaminare i dati che consentono di diagnosticare il problema e risolverlo più rapidamente. In questo articolo verrà illustrato come abilitare i log di diagnostica e come controllarli per verificare la presenza di errori. Verranno inoltre configurate alcune metriche da controllare e gli avvisi che vengono generati quando le metriche raggiungono un determinato limite. Ad esempio, si potrebbe impostare la ricezione di un messaggio di posta elettronica quando il numero di messaggi di telemetria inviati supera un limite specifico o quando il numero di messaggi usati sta per raggiungere la quota di messaggi consentiti al giorno per l'hub IoT. 

Un esempio di caso d'uso è una stazione di servizio in cui le pompe sono dispositivi IoT che comunicano con un hub IoT. Le carte di credito vengono convalidate e la transazione finale viene scritta in un archivio dati. Se i dispositivi IoT smettono di connettersi all'hub e di inviare messaggi, sarà molto più difficile risolvere il problema se non si ha visibilità su ciò che sta succedendo.

Questa esercitazione usa l'esempio di Azure del [routing dell'hub IoT](tutorial-routing.md) per inviare messaggi all'hub IoT.

In questa esercitazione si eseguono le seguenti attività:

> [!div class="checklist"]
> * Creare un hub IoT, un dispositivo simulato e un account di archiviazione con l'interfaccia della riga di comando di Azure.  
> * Abilitare i log di diagnostica.
> * Abilitare le metriche.
> * Configurare gli avvisi per tali metriche. 
> * Scaricare ed eseguire un'app che simuli un dispositivo IoT che invia messaggi all'hub. 
> * Eseguire l'app finché non vengono generati gli avvisi. 
> * Visualizzare i risultati di metriche e controllare i log di diagnostica. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Installare [Visual Studio](https://www.visualstudio.com/). 

- Un account di posta elettronica in grado di ricevere posta.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurare le risorse

Per questa esercitazione, è necessario un hub IoT, un account di archiviazione e un dispositivo IoT simulato. Queste risorse possono essere create usando l'interfaccia della riga di comando di Azure o Azure PowerShell. Usare lo stesso gruppo di risorse e lo stesso percorso per tutte le risorse. Alla fine, poi, è possibile rimuovere tutti gli elementi in un unico passaggio tramite l'eliminazione del gruppo di risorse.

Questi sono i passaggi necessari.

1. Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). 

2. Creare un hub IoT.

3. Creare un account di archiviazione V1 standard con la replica Standard_LRS.

4. Creare un'identità del dispositivo per il dispositivo simulato che invia messaggi all'hub dell'utente. Salvare la chiave per la fase del test.

### <a name="set-up-resources-using-azure-cli"></a>Configurare le risorse usando l'interfaccia della riga di comando di Azure

Copiare e incollare lo script seguente in Cloud Shell. Presupponendo che si sia già eseguito l'accesso, lo script verrà eseguito una riga alla volta. Le nuove risorse vengono create nel gruppo di risorse ContosoResources.

Le variabili che devono essere globalmente univoche hanno l'elemento `$RANDOM` concatenato. Quando viene eseguito lo script e le variabili vengono configurate, viene generata una stringa numerica casuale che viene concatenata alla fine della stringa fissa, rendendola univoca.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Quando si crea l'identità del dispositivo, è possibile che venga visualizzato l'errore seguente: *Nessuna chiave trovata per i criteri iothubowner dell'hub IoT ContosoTestHub*. Per correggere questo errore, aggiornare l'estensione IoT dell'interfaccia della riga di comando di Azure e quindi eseguire nuovamente gli ultimi due comandi dello script. 
>
>Di seguito è riportato il comando per aggiornare l'estensione. Eseguire questo comando nell'istanza di Cloud Shell.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Abilitare i log di diagnostica 

I [log di diagnostica](../azure-monitor/platform/resource-logs-overview.md) sono disabilitati per impostazione predefinita quando si crea un nuovo hub IoT. In questa sezione abilitare i log di diagnostica per l'hub.

1. In primo luogo, se non si è già nel proprio hub nel portale, fare clic su **Gruppi di risorse** e quindi sul gruppo di risorse Contoso-Resources. Selezionare l'hub dall'elenco di risorse visualizzate. 

2. Cercare la sezione **Monitoraggio** nel pannello Hub IoT. Fare clic su **Impostazioni di diagnostica**. 

   ![Schermata che mostra la parte delle impostazioni di diagnostica del pannello Hub IoT.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Verificare che la sottoscrizione e il gruppo di risorse siano corretti. In **Tipo di risorsa** deselezionare **Seleziona tutto**, quindi cercare e selezionare **Hub IoT**. Viene visualizzato di nuovo il segno di spunta accanto a *Seleziona tutto* che in questo caso può essere ignorato. In **Risorsa** selezionare il nome dell'hub. Verrà visualizzata una schermata simile alla seguente: 

   ![Schermata che mostra la parte delle impostazioni di diagnostica del pannello Hub IoT.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Fare clic su **Abilita diagnostica**. Viene visualizzato il riquadro Impostazioni di diagnostica. Specificare "diags-hub" come nome delle impostazioni dei log di diagnostica.

5. Selezionare **Archivia in un account di archiviazione**. 

   ![Schermata che mostra l'impostazione di diagnostica per l'archiviazione in un account di archiviazione.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Fare clic su **Configura** per visualizzare la schermata **Selezionare un account di archiviazione**, selezionare l'account corretto (*contosostoragemon*) e fare clic su **OK** per tornare al riquadro Impostazioni di diagnostica. 

   ![Schermata che mostra l'impostazione di log di diagnostica per l'archiviazione in un account di archiviazione.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. In **LOG** selezionare **Connessioni** e **Telemetria dei dispositivi** e impostare la **Conservazione (giorni)** su 7 giorni per ciascuna opzione. La schermata Impostazioni di diagnostica visualizzata dovrà ora essere simile alla seguente:

   ![Schermata che mostra le impostazioni finali dei log di diagnostica.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Fare clic su **Salva** per salvare le impostazioni. Chiudere il riquadro Impostazioni di diagnostica.

Successivamente, quando si esaminano i log di diagnostica, sarà possibile vedere la registrazione della connessione e della disconnessione del dispositivo. 

## <a name="set-up-metrics"></a>Configurare le metriche 

Configurare ora alcune metriche per controllare quando i messaggi vengono inviati all'hub. 

1. Nel riquadro delle impostazioni per l'hub IoT fare clic sull'opzione **Metriche** nella sezione **Monitoraggio**.

2. Nella parte superiore della schermata fare clic su **Ultime 24 ore (automatico)** . Nell'elenco a discesa visualizzato selezionare **Ultime 4 ore** per **Intervallo di tempo** e impostare **Granularità temporale** su **1 minuto**, ora locale. Fare clic su **Applica** per salvare le impostazioni. 

   ![Schermata che mostra le impostazioni temporali delle metriche.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. È presente una voce di metrica per impostazione predefinita. Lasciare il gruppo di risorse predefinito e lo spazio dei nomi della metrica. Nell'elenco a discesa **Metrica** selezionare **Messaggi di telemetria inviati**. Impostare **Aggregazione** su **Somma**.

   ![Schermata che mostra l'aggiunta di una metrica per i messaggi di telemetria inviati.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Fare clic su **Aggiungi metrica** per aggiungere un'altra metrica al grafico. Selezionare il gruppo di risorse (**ContosoTestHub**). In **Metrica** selezionare **Numero totale di messaggi usati**. Per **Aggregazione** selezionare **Media**. 

   Ora la schermata mostra la metrica minimizzata per i *Messaggi di telemetria inviati*, più la nuova metrica per il *Numero totale di messaggi usati*.

   ![Schermata che mostra l'aggiunta di una metrica per i messaggi di telemetria inviati.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Fare clic su **Aggiungi al dashboard**. Verrà aggiunta al dashboard del portale di Azure in modo che sia possibile accedervi nuovamente. Se non viene aggiunta al dashboard, le impostazioni non vengono mantenute.

## <a name="set-up-alerts"></a>Impostare gli avvisi

Andare all'hub nel portale. Fare clic su **Gruppi di risorse**, selezionare *ContosoResources*, quindi selezionare l'hub IoT *ContosoTestHub*. 

L'hub IoT non è ancora stato migrato alle [metriche in Monitoraggio di Azure](/azure/azure-monitor/platform/data-collection#metrics). È necessario usare gli [avvisi classici](/azure/azure-monitor/platform/alerts-classic.overview).

1. In **Monitoraggio** fare clic su **Avvisi**. Viene visualizzata la schermata principale degli avvisi. 

   ![Schermata che mostra come trovare gli avvisi classici.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Per visualizzare gli avvisi classici da qui, fare clic su **Visualizza avvisi classici**. 

    ![Schermata che mostra gli avvisi classici.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Compilare i campi: 

    **Sottoscrizione** lasciare questo campo impostato sulla sottoscrizione corrente.

    **Origine**: impostare questo campo su *Metriche*.

    **Gruppo di risorse**: impostare questo campo sul gruppo di risorse corrente *ContosoResources*. 

    **Tipo di risorsa**: impostare questo campo su Hub IoT. 

    **Resource** (Risorsa): selezionare l'hub IoT *ContosoTestHub*.

3. Fare clic su **Aggiungi avviso per la metrica (versione classica)** per configurare un nuovo avviso.

    Compilare i campi:

    **Nome**: specificare un nome per la regola di avviso, ad esempio *telemetry-messages*.

    **Descrizione**: fornire una descrizione dell'avviso, ad esempio *alert when there are 1000 telemetry messages sent*. 

    **Origine**: impostare questa opzione su *Metriche*.

    I campi **Sottoscrizione**, **Gruppo di risorse** e **Risorsa** devono essere impostati sui valori selezionati nella schermata **Visualizza avvisi classici**. 

    Impostare il campo **Metrica** su *Messaggi di telemetria inviati*.

    ![Schermata che mostra la configurazione di un avviso classico per i messaggi di telemetria inviati.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Dopo il grafico impostare i campi seguenti:

   **Condizione**: Impostare su *Maggiore di*.

   **Soglia**: Impostare su 1000.

   **Periodo**: Impostare su *Negli ultimi 5 minuti*.

   **Destinatari dei messaggi di posta elettronica di notifica**: Immettere l'indirizzo di posta elettronica personale. 

   ![Schermata che mostra la metà inferiore della schermata degli avvisi.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Fare clic su **OK** per salvare l'avviso. 

5. Configurare ora un altro avviso per il *numero totale di messaggi usati*. Questa metrica è utile se si vuole inviare un avviso quando il numero di messaggi usati sta per raggiungere la quota per l'hub IoT, per informare che presto l'hub inizierà a rifiutare i messaggi.

   Nella schermata **Visualizza avvisi classici** fare clic su **Aggiungi avviso per la metrica (versione classica)** , quindi compilare questi campi nel riquadro **Aggiungi regola**.

   **Nome**: specificare un nome per la regola di avviso, ad esempio *number-of-messages-used*.

   **Descrizione**: fornire una descrizione dell'avviso, ad esempio *alert when getting close to quota*.

   **Origine**: impostare questo campo su *Metriche*.

    I campi **Sottoscrizione**, **Gruppo di risorse** e **Risorsa** devono essere impostati sui valori selezionati nella schermata **Visualizza avvisi classici**. 

    Impostare il campo **Metrica** su *Numero totale di messaggi usati*.

6. Sotto il grafico compilare i campi seguenti:

   **Condizione**: Impostare su *Maggiore di*.

   **Soglia**: Impostare su 1000.

   **Periodo**: Impostare questo campo su *Negli ultimi 5 minuti*. 

   **Destinatari dei messaggi di posta elettronica di notifica**: Immettere l'indirizzo di posta elettronica personale. 

   Fare clic su **OK** per salvare la regola. 

5. Verranno visualizzati ora due avvisi nel riquadro degli avvisi classici: 

   ![Schermata che mostra gli avvisi classici con le nuove regole di avviso.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Chiudere il riquadro degli avvisi. 
    
    Con queste impostazioni, si riceverà un avviso quando il numero di messaggi inviati è superiore a 400 e quando il numero totale di messaggi sati supera il valore NUMERO.

## <a name="run-simulated-device-app"></a>Eseguire l'app di dispositivo simulato

In precedenza nella sezione di installazione dello script, è stato configurato un dispositivo per simulare l'uso di un dispositivo IoT. In questa sezione si scarica un'app console di .NET che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.  

Scaricare la soluzione per la [simulazione dispositivi IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Viene scaricato un repository contenente diverse applicazioni. La soluzione desiderata si trova in iot-hub/Tutorials/Routing/.

Fare doppio clic sul file di soluzione (SimulatedDevice.sln) per aprire il codice in Visual Studio, quindi aprire Program.cs. Sostituire `{iot hub hostname}` con il nome host dell'hub IoT. Il formato del nome host dell'hub IoT **{nome hub iot} .azure-devices.net**. Per questa esercitazione, il nome host dell'hub è **ContosoTestHub.azure devices.net**. Successivamente, sostituire `{device key}` con la chiave del dispositivo salvato in precedenza durante la configurazione del dispositivo simulato. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Esecuzione e test 

Nel file Program.cs modificare `Task.Delay` da 1000 a 10, riducendo la quantità di tempo che intercorre tra l'invio di messaggi da 1 secondo a 0.01 secondi. La riduzione di questo ritardo consente di aumentare il numero di messaggi inviati.

```csharp
await Task.Delay(10);
```

Eseguire l'applicazione console. Attendere qualche minuto (10-15). È possibile visualizzare i messaggi inviati dal dispositivo simulato all'hub nella finestra della console dell'applicazione.

### <a name="see-the-metrics-in-the-portal"></a>Visualizzare le metriche nel portale

Aprire le metriche nel dashboard. Impostare il valore su *Ultimi 30 minuti* con una granularità temporale di *1 minuto*. Nel grafico vengono visualizzati i messaggi di telemetria inviati e il numero totale di messaggi usati con i numeri più recenti nella parte inferiore del grafico.

   ![Schermata che mostra le metriche.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Visualizzare gli avvisi

Tornare agli avvisi. Fare clic su **Gruppi di risorse**, selezionare *ContosoResources*, quindi selezionare l'hub *ContosoTestHub*. Nella pagina delle proprietà visualizzata per l'hub selezionare **Avvisi**, quindi **Visualizza avvisi classici**. 

Quando il numero di messaggi inviati supera il limite, si inizierà a ricevere avvisi tramite posta elettronica. Per verificare se sono presenti avvisi attivi, passare all'hub e selezionare **Avvisi**. Vengono visualizzati gli avvisi attivi e se sono presenti altri eventuali avvisi. 

   ![Schermata che mostra gli avvisi che sono stati generati.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Fare clic sull'avviso per i messaggi di telemetria. Viene visualizzato il risultato delle metriche e un grafico con i risultati. Inoltre, il messaggio di posta elettronica inviato per avvisare della generazione dell'avviso sarà simile a quello mostrato in questa immagine:

   ![Schermata del messaggio di posta elettronica che mostra gli avvisi che sono stati generati.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Visualizzare i log di diagnostica

È possibile configurare i log di diagnostica da esportare nell'archiviazione BLOB. Passare al gruppo di risorse e selezionare l'account di archiviazione *contosostoragemon*. Selezionare i BLOB e quindi aprire il contenitore *insights-logs-connections*. Eseguire il drill-down fino a quando non viene visualizzata la data corrente e selezionare il file più recente. 

   ![Schermata del drill-down nel contenitore di archiviazione per visualizzare i log di diagnostica.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Fare clic su **Scarica** per scaricare e aprire il file. Vengono visualizzati i log del dispositivo che si connette e si disconnette mentre invia messaggi all'hub. Di seguito viene riportato un esempio:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Pulire le risorse 

Per rimuovere tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT, l'account di archiviazione e il gruppo di risorse stesso. Se sono state aggiunte metriche al dashboard, è necessario rimuoverle manualmente, facendo clic sui tre punti nell'angolo in alto a destra di ciascuna di esse e selezionando **Rimuovi**.

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare le metriche e i log di diagnostica effettuando le operazioni seguenti:

> [!div class="checklist"]
> * Creare un hub IoT, un dispositivo simulato e un account di archiviazione con l'interfaccia della riga di comando di Azure.  
> * Abilitare i log di diagnostica. 
> * Abilitare le metriche.
> * Configurare gli avvisi per tali metriche. 
> * Scaricare ed eseguire un'app che simuli un dispositivo IoT che invia messaggi all'hub. 
> * Eseguire l'app finché non vengono generati gli avvisi. 
> * Visualizzare i risultati di metriche e controllare i log di diagnostica. 

Passare all'esercitazione successiva per imparare a gestire lo stato di un dispositivo hub IoT. 

> [!div class="nextstepaction"]
> [Configurare i dispositivi da un servizio back-end](tutorial-device-twins.md)