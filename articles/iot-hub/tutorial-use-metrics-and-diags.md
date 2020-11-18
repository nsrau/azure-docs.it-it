---
title: Configurare e usare metriche e log con un hub IoT di Azure
description: Informazioni su come configurare e usare metriche e log con un hub IoT di Azure. Vengono forniti i dati da analizzare per facilitare la diagnosi dei problemi che l'hub potrebbe avere.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: d59e37cdcb6f530b08e980cf75d8834aed332252
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315197"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Esercitazione: Configurare e usare metriche e log con un hub IoT

È possibile usare Monitoraggio di Azure per raccogliere le metriche e i log per l'hub IoT che possono essere utili per monitorare il funzionamento della soluzione e risolvere gli eventuali problemi. Questo articolo illustra come creare grafici basati su metriche, creare avvisi che vengono attivati in base alle metriche, inviare le operazioni e gli errori dell'hub IoT ai log di Monitoraggio di Azure e verificare la presenza di errori nei log.

Questa esercitazione usa l'esempio di Azure dell'[avvio rapido relativo all'invio di dati di telemetria in .NET](quickstart-send-telemetry-dotnet.md) per inviare messaggi all'hub IoT. È sempre possibile usare un dispositivo o un altro esempio per inviare i messaggi, ma potrebbe essere necessario modificare alcuni passaggi di conseguenza.

Prima di iniziare questa esercitazione può essere utile acquisire familiarità con i concetti di Monitoraggio di Azure. Per altre informazioni, vedere [Monitoraggio dell'hub IoT di Azure](monitor-iot-hub.md). Per altre informazioni sulle metriche e i log delle risorse emessi dall'hub IoT, vedere [Informazioni di riferimento sul monitoraggio dei dati dell'hub IoT](monitor-iot-hub-reference.md).

In questa esercitazione si eseguono le seguenti attività:

> [!div class="checklist"]
>
> * Usare l'interfaccia della riga di comando di Azure per creare un hub IoT, registrare un dispositivo simulato e creare un'area di lavoro Log Analytics.  
> * Inviare i log delle risorse sulle connessioni e i dati di telemetria del dispositivo dell'hub IoT ai log di Monitoraggio di Azure nell'area di lavoro Log Analytics.
> * Usare lo strumento di esplorazione metriche per creare un grafico basato sulle metriche selezionate e aggiungerlo al dashboard.
> * Creare avvisi delle metriche in modo da ricevere una notifica tramite posta elettronica quando si verificano condizioni importanti.
> * Scaricare ed eseguire un'app che simula un dispositivo IoT che invia messaggi all'hub IoT.
> * Visualizzare gli avvisi generati quando si verificano determinate condizioni.
> * Visualizzare il grafico delle metriche nel dashboard.
> * Visualizzare gli errori e le operazioni dell'hub IoT nei log di Monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* È necessario .NET Core SDK 2.1 o versione successiva nel computer di sviluppo. È possibile scaricare .NET Core SDK per più piattaforme da [.NET](https://www.microsoft.com/net/download/all).

  È possibile verificare la versione corrente di C# installata nel computer di sviluppo tramite il comando seguente:

  ```cmd/sh
  dotnet --version
  ```

* Un account di posta elettronica in grado di ricevere posta.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questa esercitazione usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurare le risorse

Per questa esercitazione sono necessari un hub IoT, un'area di lavoro Log Analytics e un dispositivo IoT simulato. Queste risorse possono essere create usando l'interfaccia della riga di comando di Azure o Azure PowerShell. Usare lo stesso gruppo di risorse e lo stesso percorso per tutte le risorse. Al termine dell'esercitazione, sarà possibile rimuovere tutti gli elementi in un unico passaggio tramite l'eliminazione del gruppo di risorse.

Ecco la procedura da eseguire.

1. Creare un [gruppo di risorse](../azure-resource-manager/management/overview.md).

2. Creare un hub IoT.

3. Creare un'area di lavoro Log Analytics.

4. Registrare un'identità del dispositivo per il dispositivo simulato che invia messaggi all'hub IoT. Salvare la stringa di connessione del dispositivo da usare per configurare il dispositivo simulato.

### <a name="set-up-resources-using-azure-cli"></a>Configurare le risorse usando l'interfaccia della riga di comando di Azure

Copiare e incollare lo script seguente in Cloud Shell. Presupponendo che si sia già eseguito l'accesso, lo script verrà eseguito una riga alla volta. L'esecuzione di alcuni comandi potrebbe richiedere del tempo. Le nuove risorse vengono create nel gruppo di risorse *ContosoResources*.

Il nome di alcune risorse deve essere univoco in Azure. Lo script genera un valore casuale con la funzione `$RANDOM` e lo archivia in una variabile. Per queste risorse, lo script aggiunge questo valore casuale alla fine di un nome di base per la risorsa, rendendo univoco tale nome.

È consentito un solo hub IoT gratuito per sottoscrizione. Se nella sottoscrizione è già presente un hub IoT gratuito, eliminarlo prima di eseguire lo script oppure modificare lo script in modo da usare l'hub IoT gratuito già esistente o un hub IoT che usa il livello Standard o Basic.

Lo script visualizza il nome dell'hub IoT, il nome dell'area di lavoro Log Analytics e la stringa di connessione del dispositivo registrato. Prendere nota di questi dati, che saranno necessari più avanti in questo articolo.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Quando si crea l'identità del dispositivo, è possibile che venga visualizzato l'errore seguente: *Nessuna chiave trovata per i criteri iothubowner dell'hub IoT ContosoTestHub*. Per correggere questo errore, aggiornare l'estensione IoT dell'interfaccia della riga di comando di Azure e quindi eseguire nuovamente gli ultimi due comandi dello script. 
>
>Di seguito è riportato il comando per aggiornare l'estensione. Eseguire questo comando nell'istanza di Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Raccogliere log relativi alle connessioni e ai dati di telemetria del dispositivo

L'hub IoT emette i log delle risorse per diverse categorie di operazioni. Per visualizzare questi log occorre però creare un'impostazione di diagnostica per inviarli a una destinazione. Una destinazione adatta è costituita dai log di Monitoraggio di Azure, che sono raccolti in un'area di lavoro Log Analytics. I log delle risorse dell'hub IoT sono raggruppati in categorie diverse. È possibile selezionare le categorie che devono essere inviate ai log di Monitoraggio di Azure nell'impostazione di diagnostica. In questo articolo si raccoglieranno i log per le operazioni e gli errori correlati alle connessioni e ai dati di telemetria del dispositivo. Per un elenco completo delle categorie supportate per l'hub IoT, vedere [Log delle risorse dell'hub IoT](monitor-iot-hub-reference.md#resource-logs).

Per creare un'impostazione di diagnostica per l'invio dei log delle risorse dell'hub IoT ai log di Monitoraggio di Azure, seguire questa procedura:

1. In primo luogo, se non si è già nel proprio hub nel portale, selezionare **Gruppi di risorse** e quindi il gruppo di risorse ContosoResources. Selezionare l'hub IoT dall'elenco di risorse visualizzato.

1. Cercare la sezione **Monitoraggio** nel pannello Hub IoT. Selezionare **Impostazioni di diagnostica**. Quindi selezionare **Aggiungi impostazione di diagnostica**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Screenshot che evidenzia l'opzione Impostazioni di diagnostica nella sezione Monitoraggio.":::

1. Nel riquadro **Impostazione di diagnostica** assegnare all'impostazione un nome descrittivo, ad esempio "Invio dati di telemetria e connessioni ai log".

1. In **Dettagli categoria** selezionare **Connections** e **DeviceTelemetry**.

1. In **Dettagli della destinazione** selezionare **Invia a Log Analytics**, quindi usare lo strumento di selezione dell'area di lavoro Log Analytics per selezionare l'area di lavoro annotata in precedenza. Al termine, l'impostazione di diagnostica dovrebbe avere un aspetto simile allo screenshot seguente:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Schermata che mostra le impostazioni finali dei log di diagnostica.":::

1. Selezionare **Salva** per salvare le impostazioni. Chiudere il riquadro **Impostazione di diagnostica**. La nuova impostazione è ora presente nell'elenco delle impostazioni di diagnostica.

## <a name="set-up-metrics"></a>Configurare le metriche

A questo punto si userà lo strumento di esplorazione delle metriche per creare un grafico con le metriche da monitorare. Questo grafico verrà quindi aggiunto al dashboard predefinito nel portale di Azure.

1. Nel riquadro sinistro dell'hub IoT selezionare **Metriche** nella sezione **Monitoraggio**.

1. Nella parte superiore della schermata selezionare **Ultime 24 ore (automatico)** . Nell'elenco a discesa visualizzato selezionare **Ultime 4 ore** per **Intervallo di tempo**, impostare **Granularità temporale** su **1 minuto** e selezionare **Locale** per **Mostra ora come**. Selezionare **Applica** per salvare le impostazioni. L'impostazione ora dovrebbe essere **Ora locale: Ultime 4 ore (1 minuto)** .

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Schermata che mostra le impostazioni temporali delle metriche.":::

1. Nel grafico è visualizzata un'impostazione di metrica parziale nell'ambito dell'hub IoT. Lasciare invariati i valori predefiniti di **Ambito** e **Spazio dei nomi della metrica**. Selezionare l'impostazione **Metrica** e digitare "Telemetria", quindi selezionare **Messaggi di telemetria inviati** nell'elenco a discesa. L'opzione **Aggregazione** verrà impostata automaticamente su **Somma**. Si noti che anche il titolo del grafico cambia.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Screenshot che mostra l'aggiunta della metrica Messaggi di telemetria inviati al grafico.":::

1. Ora selezionare **Aggiungi metrica** per aggiungere un'altra metrica al grafico. In **Metrica** selezionare **Numero totale di messaggi usati**. L'opzione **Aggregazione** verrà impostata automaticamente su **Media**. Si noti che il titolo del grafico è cambiato ancora in modo da includere questa metrica.

   Ora la schermata mostra la metrica minimizzata per i *Messaggi di telemetria inviati*, più la nuova metrica per il *Numero totale di messaggi usati*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Screenshot che mostra l'aggiunta della metrica Numero totale di messaggi usati al grafico.":::

1. Nell'angolo in alto a destra del grafico selezionare **Aggiungi al dashboard**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Screenshot che evidenzia il pulsante Aggiungi al dashboard.":::

1. Nel riquadro **Aggiungi al dashboard** selezionare la scheda **Esistente**. Selezionare **Privato** e quindi selezionare **Dashboard** nell'elenco a discesa Dashboard. Infine, selezionare **Aggiungi** per aggiungere il grafico al dashboard predefinito nel portale di Azure. Se non si aggiunge il grafico a un dashboard, le impostazioni non vengono mantenute quando si esce dallo strumento di esplorazione delle metriche.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Screenshot che mostra le impostazioni per Aggiungi al dashboard.":::

## <a name="set-up-metric-alerts"></a>Configurare gli avvisi delle metriche

A questo punto occorre configurare gli avvisi da attivare sulla base delle metriche *Messaggi di telemetria inviati* e *Numero totale di messaggi usati*.

*Messaggi di telemetria inviati* è una metrica che è utile monitorare per tenere traccia della velocità effettiva dei messaggi ed evitare limitazioni. Per un hub IoT nel livello gratuito la limitazione massima è di 100 messaggi al secondo. Dato che con un solo dispositivo non è possibile raggiungere questo tipo di velocità effettiva, si configurerà l'avviso in modo che venga attivato se il numero di messaggi supera i 1000 in 5 minuti. In ambiente di produzione è possibile impostare il segnale su un valore più significativo in base al livello, all'edizione e al numero di unità dell'hub IoT.

La metrica *Numero totale di messaggi usati* tiene traccia del numero di messaggi usati giornalmente. Viene reimpostata ogni giorno alle 00:00 UTC. Se si supera la quota giornaliera oltre una determinata soglia, l'hub IoT non accetterà più messaggi. Per un hub IoT nel livello gratuito la quota giornaliera dei messaggi è pari a 8.000. Si configurerà l'avviso in modo che venga attivato se il numero totale di messaggi supera 4000, il 50% della quota. Nella pratica questa percentuale viene probabilmente impostata su un valore più alto. Il valore della quota giornaliera dipende dal livello, dall'edizione e dal numero di unità dell'hub IoT.

Per altre informazioni sui limiti delle quote e la limitazione delle operazioni con l'hub IoT, vedere [Quote e limitazioni](iot-hub-devguide-quotas-throttling.md).

Per configurare gli avvisi delle metriche:

1. Passare all'hub IoT nel portale di Azure.

1. In **Monitoraggio** selezionare **Avvisi**. Quindi selezionare **Nuova regola di avviso**.  Si aprirà il riquadro **Crea regola di avviso**.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Screenshot che mostra il riquadro Crea regola di avviso.":::

    Il riquadro **Crea regola di avviso** è suddiviso in quattro sezioni:

    * **Ambito** è già impostato sull'hub IoT, quindi non occorre fare nulla in questa sezione.
    * **Condizione** imposta il segnale e le condizioni che attiveranno l'avviso.
    * **Azioni** configura le azioni da eseguire all'attivazione dell'avviso.
    * **Dettagli regola di avviso** consente di impostare un nome e una descrizione per l'avviso.

1. Configurare prima di tutto la condizione in base alla quale verrà attivato l'avviso.

    1. In **Condizione** selezionare **Selezione condizione**. Nel riquadro **Configura logica dei segnali** digitare "telemetria" nella casella di ricerca e selezionare **Messaggi di telemetria inviati**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Screenshot che mostra la selezione della metrica.":::

    1. Nel riquadro **Configura logica dei segnali** impostare o confermare i campi seguenti in **Logica avvisi** (è possibile ignorare il grafico):

       **Soglia**:  *Statica*.

       **Operatore**: *Maggiore di*.

       **Tipo di aggregazione**: *Totale*.

       **Valore soglia**: 1000.

       **Granularità aggregazione (periodo)** : *5 minuti*.

       **Frequenza della valutazione**: *Ogni minuto*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Screenshot che mostra le impostazioni delle condizioni per gli avvisi.":::

       Queste impostazioni impostano il segnale sul totale del numero dei messaggi su un periodo di 5 minuti. Questo totale viene valutato ogni minuto e, se il totale per i 5 minuti precedenti supera i 1000 messaggi, viene attivato l'avviso.

       Selezionare **Fine** per salvare la logica dei segnali.

1. A questo punto occorre configurare l'azione per l'avviso.

    1. Tornare al riquadro **Crea regola di avviso** e in **Azioni** selezionare **Seleziona gruppo di azioni**. In **Selezionare un gruppo di azioni da collegare a questa regola di avviso** selezionare **Crea gruppo di azioni**.

    1. Nella scheda **Informazioni di base** del riquadro **Crea gruppo di azioni** assegnare al gruppo di azioni un nome e un nome visualizzato.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Screenshot che mostra la scheda Informazioni di base del riquadro Crea gruppo di azioni.":::

    1. Selezionare la scheda **Notifiche**. In **Tipo di notifica** selezionare **Email/SMS message/Push/Voice** (Messaggio di posta elettronica/Messaggio SMS/Push/Voce) dall'elenco a discesa. Si aprirà il riquadro **Email/SMS message/Push/Voice** (Messaggio di posta elettronica/Messaggio SMS/Push/Voce).

    1. Nel riquadro **Email/SMS message/Push/Voice** selezionare Posta elettronica e immettere l'indirizzo di posta elettronica, quindi scegliere **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Screenshot che mostra l'impostazione dell'indirizzo di posta elettronica.":::

    1. Nel riquadro **Notifiche** immettere un nome per la notifica.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Screenshot che mostra il riquadro Notifiche completato.":::

    1. (Facoltativo) Se si seleziona la scheda **Azioni** e quindi l'elenco a discesa **Tipo di azione**, è possibile vedere i tipi di azioni che possono essere attivati con un avviso. Ai fini di questo articolo si useranno solo le notifiche, pertanto è possibile ignorare le impostazioni di questa scheda.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Screenshot che mostra i tipi di azione disponibili nel riquadro Azioni.":::

    1. Selezionare la scheda **Rivedi e crea**, verificare le impostazioni e selezionare **Crea**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Screenshot che mostra il riquadro Rivedi e crea.":::

    1. Tornando al riquadro **Crea regola di avviso**, si può notare che il nuovo gruppo di azioni è stato aggiunto alle azioni per l'avviso.  

1. Infine, configurare i dettagli della regola di avviso e salvare la regola.

    1. Nella sezione Dettagli regola di avviso del riquadro **Crea regola di avviso** immettere un nome e una descrizione per l'avviso, ad esempio "Avvisa in caso di superamento di 1000 messaggi in 5 minuti". Verificare che la casella **Abilita regola di avviso alla creazione** sia selezionata. La regola di avviso completata sarà simile allo screenshot seguente.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Screenshot che mostra il riquadro Crea regola di avviso completato.":::

    1. Selezionare **Crea regola di avviso** per salvare la nuova regola.

1. Configurare ora un altro avviso per il *numero totale di messaggi usati*. Questa metrica è utile se si vuole inviare un avviso quando il numero di messaggi usati si sta avvicinando alla quota giornaliera per l'hub IoT, al raggiungimento della quale l'hub inizierà a rifiutare i messaggi. Seguire la procedura descritta in precedenza, con le differenze seguenti.

    * Per il segnale nel riquadro **Configura logica dei segnali** selezionare **Numero totale di messaggi usati**.

    * Nel riquadro **Configura logica dei segnali** impostare o confermare i campi seguenti (è possibile ignorare il grafico):

       **Soglia**:  *Statica*.

       **Operatore**: *Maggiore di*.

       **Tipo di aggregazione**: *Massimo*.

       **Valore soglia**: 4000.

       **Granularità aggregazione (periodo)** : *1 minuto*.

       **Frequenza della valutazione**: *Ogni minuto*

       Queste impostazioni impostano l'attivazione del segnale quando il numero di messaggi raggiunge i 4000. La metrica viene valutata ogni minuto.

    * Quando si specifica l'azione per la regola di avviso, selezionare semplicemente il gruppo di azioni creato in precedenza.

    * Per i dettagli dell'avviso, scegliere un nome e una descrizione diversi rispetto a quelli specificati in precedenza.

1. Selezionare **Avvisi** in **Monitoraggio** nel riquadro sinistro dell'hub IoT. Selezionare **Gestisci regole di avviso** nel menu nella parte superiore del riquadro **Avvisi**. Si aprirà il riquadro **Regole**. A questo punto si dovrebbero vedere i due avvisi:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Screenshot che mostra il riquadro Regole con le nuove regole di avviso.":::

1. Chiudere il riquadro **Regole**.

Con queste impostazioni verrà attivato un avviso e si riceverà una notifica tramite posta elettronica quando vengono inviati più di 1000 messaggi entro un intervallo di 5 minuti e anche quando il numero totale di messaggi usati è superiore a 4000 (il 50% della quota giornaliera per un hub IoT nel livello gratuito).

## <a name="run-the-simulated-device-app"></a>Eseguire un'app di dispositivo simulato

Nelle sezione [Configurare le risorse](#set-up-resources) è stata registrata un'identità del dispositivo da usare per la simulazione con un dispositivo IoT. In questa sezione si scarica un'app console .NET che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT, si configura l'app per inviare questi messaggi all'hub IoT e quindi la si esegue. 

> [!IMPORTANT]
>
> La configurazione completa e l'abilitazione degli avvisi da parte dell'hub IoT possono richiedere fino a 10 minuti. Attendere almeno 10 minuti tra il momento in cui viene configurato l'ultimo avviso e l'esecuzione dell'app del dispositivo simulato.

Scaricare la soluzione per la [simulazione dispositivi IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Tramite questo collegamento viene scaricato un repository contenente diverse applicazioni. La soluzione desiderata si trova in iot-hub/Quickstarts/simulated-device/.

1. In una finestra del terminale locale passare alla cartella radice della soluzione. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device**.

1. Aprire il file **SimulatedDevice.cs** in un editor di testo di propria scelta.

    1. Sostituire il valore della variabile `s_connectionString` con la stringa di connessione del dispositivo annotata quando è stato eseguito lo script per configurare le risorse.

    1. Nel metodo `SendDeviceToCloudMessagesAsync` cambiare il valore di `Task.Delay` da 1000 a 1, riducendo così la quantità di tempo che intercorre tra gli invii di messaggi da 1 secondo a 0,001 secondi. La riduzione di questo ritardo consente di aumentare il numero di messaggi inviati. Probabilmente non si otterrà una frequenza di 100 messaggi al secondo.

        ```csharp
        await Task.Delay(1);
        ```

    1. Salvare le modifiche nel file **SimulatedDevice.cs**.

1. Nella finestra del terminale locale eseguire il comando seguente per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

1. Nella finestra del terminale eseguire il comando seguente per compilare ed eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet run
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Screenshot che mostra l'output del dispositivo simulato.":::

Lasciare l'applicazione in esecuzione per almeno 10-15 minuti. L'ideale è lasciarla in esecuzione finché non smette di inviare messaggi (circa 20-30 minuti). Questo avviene quando si supera la quota giornaliera di messaggi per l'hub IoT e quest'ultimo non accetta più altri messaggi.

> [!NOTE]
> Se si lascia l'app del dispositivo in esecuzione per un periodo di tempo prolungato dopo che ha smesso di inviare messaggi, è possibile che venga generata un'eccezione. È possibile ignorare tranquillamente questa eccezione e chiudere la finestra dell'app.

## <a name="view-metrics-chart-on-your-dashboard"></a>Visualizzare il grafico delle metriche nel dashboard

1. Nell'angolo in alto a sinistra del portale di Azure aprire il menu del portale e quindi selezionare **Dashboard**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Screenshot che mostra come selezionare il dashboard.":::

1. Trovare il grafico aggiunto in precedenza e fare clic in un punto qualsiasi del riquadro all'esterno dei dati del grafico per espanderlo. Nel grafico vengono visualizzati i messaggi di telemetria inviati e il numero totale di messaggi usati. I numeri più recenti sono visualizzati nella parte inferiore del grafico. È possibile spostare il cursore all'interno del grafico per visualizzare i valori delle metriche per orari specifici. È anche possibile modificare il valore e la granularità nella parte superiore del grafico per limitare o espandere i dati a un periodo di tempo di interesse.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Screenshot che mostra il grafico delle metriche.":::

   In questo scenario la velocità effettiva dei messaggi del dispositivo simulato non è sufficientemente elevata da causare la limitazione dei messaggi da parte dell'hub IoT. In uno scenario che prevede effettivamente la limitazione, è possibile che i messaggi di telemetria inviati superino il limite impostato per l'hub IoT per un periodo di tempo limitato. Lo scopo è quello di consentire la gestione dei picchi di traffico. Per informazioni dettagliate, vedere [Traffic Shaping](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Visualizzare gli avvisi

Quando il numero di messaggi inviati supera i limiti impostati nelle regole di avviso, si inizierà a ricevere avvisi tramite posta elettronica.

Per verificare la presenza di avvisi attivi, selezionare **Avvisi** in **Monitoraggio** nel riquadro sinistro dell'hub IoT. Il riquadro **Avvisi** mostra il numero di avvisi generati ordinati per livello di gravità per l'intervallo di tempo specificato.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Screenshot che mostra il riepilogo degli avvisi.":::

Selezionare la riga relativa al livello Gravità 3. Si aprirà il riquadro **Tutti gli avvisi** in cui sono elencati gli avvisi con livello di gravità 3 generati.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Screenshot che mostra il riquadro Tutti gli avvisi.":::

Selezionare uno degli avvisi per visualizzarne i dettagli.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Screenshot che mostra i dettagli dell'avviso.":::

Controllare la posta in arrivo per verificare se sono presenti messaggi da Microsoft Azure. La riga dell'oggetto conterrà la descrizione dell'avviso attivato. Ad esempio, *Azure: Gravità attivata: 3 Avvisa in caso di superamento di 1000 messaggi in 5 minuti*. Il corpo del messaggio sarà simile a quello nell'immagine seguente:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Schermata del messaggio di posta elettronica che mostra gli avvisi che sono stati generati.":::

## <a name="view-azure-monitor-logs"></a>Visualizzare i log di Monitoraggio di Azure

Nella sezione [Raccogliere log relativi alle connessioni e ai dati di telemetria del dispositivo](#collect-logs-for-connections-and-device-telemetry) è stata creata un'impostazione di diagnostica per l'invio dei log delle risorse emessi dall'hub IoT per le operazioni di connessione e telemetria del dispositivo ai log di Monitoraggio di Azure. In questa sezione si eseguirà una query Kusto sui log di Monitoraggio di Azure per osservare gli eventuali errori.

1. In **Monitoraggio**, nel riquadro sinistro dell'hub IoT nel portale di Azure, selezionare **Log**. Chiudere la finestra **Query** iniziale, se aperta.

1. Nel riquadro Nuova query selezionare la scheda **Query** , quindi espandere **Hub IoT** per visualizzare l'elenco delle query predefinite.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Screenshot delle query predefinite dell'hub IoT.":::

1. Selezionare la query *Riepilogo errori*. La query viene visualizzata nel riquadro Editor di query. Selezionare **Esegui** nel riquadro dell'editor e osservare i risultati della query. Espandere una delle righe per visualizzare i dettagli.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Screenshot dei log restituiti dalla query Riepilogo errori.":::

   > [!NOTE]
   > Se non vengono visualizzati errori, provare a eseguire la query *Recently connected devices* (Dispositivi connessi di recente). La query dovrebbe restituire una riga per il dispositivo simulato.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso rimuove l'hub IoT, l'area di lavoro Log Analytics e il gruppo di risorse stesso. Se sono stati aggiunti grafici di metriche al dashboard, è necessario rimuoverli manualmente facendo clic sui tre puntini nell'angolo in alto a destra di ciascun grafico e selezionando **Rimuovi**. Assicurarsi di salvare le modifiche dopo aver eliminato i grafici.

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare le metriche di hub IoT e i log effettuando le operazioni seguenti:

> [!div class="checklist"]
>
> * Usare l'interfaccia della riga di comando di Azure per creare un hub IoT, registrare un dispositivo simulato e creare un'area di lavoro Log Analytics.  
> * Inviare i log delle risorse sulle connessioni e i dati di telemetria del dispositivo dell'hub IoT ai log di Monitoraggio di Azure nell'area di lavoro Log Analytics.
> * Usare lo strumento di esplorazione metriche per creare un grafico basato sulle metriche selezionate e aggiungerlo al dashboard.
> * Creare avvisi delle metriche in modo da ricevere una notifica tramite posta elettronica quando si verificano condizioni importanti.
> * Scaricare ed eseguire un'app che simula un dispositivo IoT che invia messaggi all'hub IoT.
> * Visualizzare gli avvisi generati quando si verificano determinate condizioni.
> * Visualizzare il grafico delle metriche nel dashboard.
> * Visualizzare gli errori e le operazioni dell'hub IoT nei log di Monitoraggio di Azure.

Passare all'esercitazione successiva per imparare a gestire lo stato di un dispositivo hub IoT. 

> [!div class="nextstepaction"]
> [Configurare i dispositivi da un servizio back-end](tutorial-device-twins.md)