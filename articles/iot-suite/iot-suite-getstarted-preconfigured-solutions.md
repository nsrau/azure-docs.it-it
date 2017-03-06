---
title: Introduzione alle soluzioni preconfigurate | Documentazione Microsoft
description: Seguire questa esercitazione per apprendere come distribuire una soluzione preconfigurata di Azure IoT Suite.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Esercitazione: Introduzione alle soluzioni preconfigurate
## <a name="introduction"></a>Introduzione
Le [soluzioni preconfigurate][lnk-preconfigured-solutions] di Azure IoT Suite combinano più servizi IoT di Azure per fornire soluzioni end-to-end che implementano scenari aziendali IoT comuni. La soluzione preconfigurata per il *monitoraggio remoto* si connette ai dispositivi e li monitora. È possibile usare la soluzione per analizzare il flusso di dati dai dispositivi e di migliorare i risultati aziendali facendo in modo che i processi rispondano automaticamente a quel flusso di dati.

Questa esercitazione illustra come effettuare il provisioning della soluzione preconfigurata per il monitoraggio remoto. Ne descrive anche le funzionalità di base. È possibile accedere a molte di queste funzionalità dal dashboard distribuito come parte della soluzione preconfigurata:

![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-dashboard]

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [!NOTE]
> Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Visualizzare il dashboard della soluzione
Il dashboard della soluzione consente di gestire la soluzione distribuita. Ad esempio, è possibile visualizzare dati di telemetria, aggiungere dispositivi e configurare regole.

1. Al termine del provisioning, quando il riquadro della soluzione preconfigurata indica **Pronto**, fare clic su **Avvia** per aprire il portale della soluzione di monitoraggio remoto in una nuova scheda.
   
   ![Avviare la soluzione preconfigurata][img-launch-solution]
2. Per impostazione predefinita, il portale della soluzione visualizza il *dashboard della soluzione*. È possibile selezionare altre viste usando il menu a sinistra.
   
   ![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-dashboard]

Il dashboard visualizza le informazioni seguenti:

* La mappa visualizza la posizione di ogni dispositivo connesso alla soluzione. Quando si esegue la soluzione per la prima volta, sono disponibili quattro dispositivi simulati. I dispositivi simulati vengono implementati come Processi Web di Azure e la soluzione usa l'API Bing Maps per tracciare le informazioni sulla mappa.
* Il pannello **Cronologia telemetria** traccia la telemetria di umidità e temperatura da un dispositivo selezionato in tempo quasi reale e visualizza i dati aggregati, ad esempio l'umidità massima, minima e media.
* Il pannello **Cronologia avvisi** mostra gli eventi di avviso recenti relativi a quando un valore di telemetria ha superato una soglia. È possibile definire i propri avvisi oltre agli esempi creati dalla soluzione preconfigurata.
* Il pannello **Processi** visualizza informazioni sui processi pianificati. È possibile pianificare i propri processi nella pagina **Processi di gestione**.

## <a name="view-the-device-list"></a>Visualizzare l'elenco dei dispositivi
L'*elenco dei dispositivi* mostra tutti i dispositivi registrati nella soluzione. Da tale elenco è possibile visualizzare e modificare i metadati dei dispositivi, aggiungere o rimuovere dispositivi e richiamare metodi su di essi.

1. Fare clic su **Dispositivi** nel menu a sinistra per visualizzare l'elenco dei dispositivi per la soluzione.
   
   ![Elenco dei dispositivi nel dashboard][img-devicelist]
2. L'elenco dei dispositivi mostra inizialmente quattro dispositivi simulati creati dal processo di provisioning. È possibile aggiungere altri dispositivi fisici e simulati alla soluzione.
3. È possibile personalizzare le informazioni visualizzate nell'elenco dei dispositivi facendo clic su **Column editor** (Editor colonne). È possibile aggiungere e rimuovere colonne che visualizzano i valori dei tag e delle proprietà segnalate, nonché riordinare e rinominare le colonne:
   
   ![Editor colonne nel dashboard][img-columneditor]
4. Per visualizzare i dettagli di un dispositivo, fare clic sul dispositivo nell'elenco.
   
   ![Dettagli del dispositivo nel dashboard][img-devicedetails]

Il pannello **Dettagli dispositivo** contiene sei sezioni:

* Una raccolta di collegamenti che consentono di personalizzare l'icona del dispositivo, disabilitare il dispositivo, aggiungere una regola, richiamare un metodo o inviare un comando. Per un confronto dei comandi (messaggi da dispositivo a cloud) e dei metodi (metodi diretti), vedere [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance].
* La sezione **Device Twin (Dispositivo gemello) - Tag** consente di modificare i valori dei tag per il dispositivo. È possibile visualizzare i valori dei tag nell'elenco dei dispositivi e usarli per filtrare l'elenco.
* La sezione **Device Twin (Dispositivo gemello) - Proprietà desiderate** consente di impostare i valori delle proprietà da inviare al dispositivo.
* La sezione **Device Twin (Dispositivo gemello) - Proprietà segnalate** visualizza i valori delle proprietà inviati dal dispositivo.
* La sezione **Proprietà dispositivo** visualizza informazioni del registro di identità, come l'ID dispositivo e le chiavi di autenticazione.
* La sezione **Processi recenti** visualizza informazioni sui processi recentemente indirizzati al dispositivo.

## <a name="customize-the-device-icon"></a>Personalizzare l'icona del dispositivo

È possibile personalizzare l'icona del dispositivo visualizzata nell'elenco dei dispositivi dal pannello **Dettagli dispositivo**, come indicato di seguito:

1. Fare clic sull'icona a forma di matita per aprire il pannello per la **modifica dell'immagine** per un dispositivo:
   
   ![Editor di immagini per il dispositivo: apertura][img-startimageedit]
2. Caricare una nuova immagine oppure usarne una esistente e quindi fare clic su **Salva**:
   
   ![Editor di immagini per il dispositivo: modifica][img-imageedit]
3. L'immagine selezionata verrà visualizzata nella colonna **Icona** del dispositivo.

> [!NOTE]
> L'immagine viene archiviata in un archivio BLOB. Un tag nel dispositivo gemello contiene un collegamento all'immagine nell'archivio BLOB.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Richiamare un metodo su un dispositivo
Dal pannello **Dettagli dispositivo** è possibile richiamare metodi sul dispositivo. Quando un dispositivo viene avviato per la prima volta, invia alla soluzione informazioni sui metodi che supporta.

1. Fare clic su **Metodi** nel pannello **Dettagli dispositivo** del dispositivo selezionato:
   
   ![Metodi del dispositivo nel dashboard][img-devicemethods]
2. Selezionare **Reboot** nell'elenco dei metodi.
3. Fare clic su **Richiama metodo**.
4. Lo stato della chiamata al metodo viene visualizzato in Cronologia metodo.
   
   ![Stato dei metodi nel dashboard][img-pingmethod]

La soluzione tiene traccia dello stato di ogni metodo richiamato. Quando il dispositivo completa il metodo, viene visualizzata una nuova voce nella tabella della cronologia.

Alcuni metodi avviano processi asincroni nel dispositivo. Il metodo **InitiateFirmwareUpdate**, ad esempio, avvia un'attività asincrona per eseguire l'aggiornamento. Il dispositivo usa le proprietà segnalate per segnalare lo stato dell'aggiornamento del firmware durante l'esecuzione.

## <a name="send-a-command-to-a-device"></a>Inviare un comando a un dispositivo
Dal pannello **Dettagli dispositivo** è possibile inviare comandi al dispositivo. Quando un dispositivo viene avviato per la prima volta, invia alla soluzione informazioni sui comandi che supporta.

1. Fare clic su **Comandi** nel pannello **Dettagli dispositivo** del dispositivo selezionato:
   
   ![Comandi del dispositivo nel dashboard][img-devicecommands]
2. Selezionare **PingDevice** dall'elenco dei comandi.
3. Fare clic su **Invia comando**.
4. È possibile visualizzare lo stato del comando nella cronologia dei comandi.
   
   ![Stato dei comandi nel dashboard][img-pingcommand]

La soluzione tiene traccia dello stato di ogni comando inviato. All'inizio il risultato è **In sospeso**. Quando il dispositivo segnala che il comando è stato eseguito, il risultato viene impostato su **Operazione riuscita**.

## <a name="add-a-new-simulated-device"></a>Aggiungere un nuovo dispositivo simulato
Quando si distribuisce la soluzione preconfigurata, viene effettuato automaticamente il provisioning di quattro dispositivi che vengono visualizzati nell'elenco dei dispositivi. Si tratta di *dispositivi simulati* in esecuzione in un processo Web di Azure. I dispositivi simulati consentono di provare facilmente la soluzione preconfigurata senza la necessità di distribuire dispositivi fisici reali. Per connettere un dispositivo reale alla soluzione, vedere l'esercitazione [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm].

La procedura seguente illustra come aggiungere un dispositivo simulato alla soluzione:

1. Tornare all'elenco dei dispositivi.
2. Per aggiungere un dispositivo, fare clic su **Aggiungi un dispositivo** nell'angolo inferiore sinistro.
   
   ![Aggiungere un dispositivo alla soluzione preconfigurata][img-adddevice]
3. Fare clic su **Aggiungi nuovo** nel riquadro **Simulated Device** (Dispositivo simulato).
   
   ![Impostare i dettagli del nuovo dispositivo nel dashboard][img-addnew]
   
   Oltre a creare un nuovo dispositivo simulato, è anche possibile aggiungere un dispositivo fisico se si sceglie di creare un **dispositivo personalizzato**. Per altre informazioni sulla connessione di dispositivi fisici alla soluzione, vedere [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite][lnk-connect-rm].
4. Selezionare **Let me define my own Device ID** (Definire l'ID dispositivo personale) e immettere un nome di ID dispositivo univoco, ad esempio **mydevice_01**.
5. Fare clic su **Crea**.
   
   ![Salvare un nuovo dispositivo][img-definedevice]
6. Nel passaggio 3 della procedura **Aggiungere un nuovo dispositivo simulato** fare clic su **Fatto** per tornare all'elenco dei dispositivi.
7. Verificare che lo stato del dispositivo nell'elenco sia **In esecuzione** .
   
    ![Visualizzare il nuovo dispositivo nell'elenco dei dispositivi][img-runningnew]
8. È anche possibile visualizzare la telemetria simulata dal nuovo dispositivo nel dashboard:
   
    ![Visualizzare i dati di telemetria dal nuovo dispositivo][img-runningnew-2]

## <a name="device-properties"></a>Proprietà dei dispositivi
La soluzione preconfigurata per il monitoraggio remoto usa i [dispositivi gemelli][lnk-device-twin] per sincronizzare i metadati dei dispositivi tra i dispositivi e il back-end della soluzione. Un dispositivo gemello è un documento JSON archiviato nell'hub IoT che contiene i valori delle proprietà di un singolo dispositivo. I dispositivi inviano regolarmente i metadati al back-end della soluzione sotto forma di *proprietà segnalate* da archiviare nel dispositivo gemello. Il back-end della soluzione può impostare *proprietà desiderate* nel dispositivo gemello per inviare aggiornamenti dei metadati ai dispositivi. Le proprietà segnalate mostrano i valori più recenti dei metadati inviati dal dispositivo. Per altre informazioni, vedere la sezione relativa a [registro delle identità dei dispositivi, dispositivo gemello e DocumentDB][lnk-devicemetadata].

> [!NOTE]
> La soluzione usa anche un database DocumentDB per archiviare i dati specifici del dispositivo relativi a comandi e metodi.
> 
> 

1. Tornare all'elenco dei dispositivi.
2. Selezionare il nuovo dispositivo nell'**elenco dei dispositivi** e quindi fare clic su **Modifica** per modificare le proprietà in **Device Twin (Dispositivo gemello) - Proprietà desiderate**:
   
   ![Modificare le proprietà desiderate del dispositivo][img-editdevice]
3. Impostare **Nome proprietà desiderata** su **Latitude** e il valore su **47.639521**. Fare quindi clic su **Salva le modifiche al Registro di sistema del dispositivo**:
   
    ![Modificare una proprietà desiderata del dispositivo][img-editdevice2]
4. Nel pannello **Dettagli dispositivo**, il nuovo valore di latitudine viene inizialmente visualizzato come proprietà desiderata e il valore di latitudine precedente viene visualizzato come proprietà segnalata:
   
    ![Visualizzare la proprietà segnalata][img-editdevice3]
5. Attualmente, i dispositivi simulati nella soluzione preconfigurata elaborano solo le proprietà desiderate **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval**. Un dispositivo reale dovrà leggere tutte le proprietà desiderate dall'hub IoT, apportare modifiche alla propria configurazione e segnalare i nuovi valori all'hub come proprietà segnalate.

Nel pannello **Dettagli dispositivo** è possibile anche modificare i valori in **Device Twin (Dispositivo gemello) - Tag** così come i valori in **Device Twin (Dispositivo gemello) - Proprietà desiderate**. A differenza delle proprietà desiderate, tuttavia, i tag non vengono sincronizzati con il dispositivo. I tag esistono solo nel dispositivo gemello nell'hub IoT e sono utili per creare filtri personalizzati nell'elenco dei dispositivi.

## <a name="sort-the-device-list"></a>Ordinare l'elenco dei dispositivi

È possibile ordinare l'elenco dei dispositivi facendo clic accanto a un'intestazione di colonna. Con il primo clic viene applicato l'ordinamento crescente, con il secondo clic quello decrescente:

![Ordinare l'elenco dei dispositivi][img-sortdevices]

## <a name="filter-the-device-list"></a>Filtrare l'elenco dei dispositivi

Nell'elenco dei dispositivi è possibile creare, salvare e ricaricare filtri per visualizzare un elenco personalizzato dei dispositivi connessi all'hub. Per creare un filtro:

1. Fare clic sull'icona di modifica filtro sopra l'elenco dei dispositivi:
   
   ![Aprire l'editor dei filtri][img-editfiltericon]
2. In **Editor dei filtri** aggiungere i campi, gli operatori e i valori per filtrare l'elenco dei dispositivi. È possibile aggiungere più clausole per perfezionare il filtro. Fare clic su **Filtra** per applicare il filtro:
   
   ![Creare un filtro][img-filtereditor]
3. In questo esempio, l'elenco è filtrato in base al produttore e al numero di modello:
   
   ![Elenco filtrato][img-filterelist]
4. Per salvare il filtro con un nome personalizzato, fare clic sull'icona **Salva con nome**:
   
   ![Salvare un filtro][img-savefilter]
5. Per riapplicare un filtro salvato in precedenza, fare clic sull'icona **Apri filtro salvato**:
   
   ![Aprire un filtro][img-openfilter]

È possibile creare filtri basati sull'ID dispositivo, sullo stato del dispositivo, sulle proprietà desiderate, sulle proprietà segnalate e sui tag.

> [!NOTE]
> In **Editor dei filtri** è possibile usare la **visualizzazione avanzata** per modificare direttamente il testo della query.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Aggiungere una regola per il nuovo dispositivo
Non sono presenti regole per il nuovo dispositivo appena aggiunto. In questa sezione si aggiunge una regola che attiva un avviso quando la temperatura segnalata dal nuovo dispositivo supera i 47 gradi. Prima di iniziare, si noti che la cronologia della telemetria per il nuovo dispositivo nel dashboard indica che la temperatura del dispositivo non supera mai i 45 gradi.

1. Tornare all'elenco dei dispositivi.
2. Per aggiungere una regola per il dispositivo, selezionare il nuovo dispositivo nell'**elenco dei dispositivi** e quindi fare clic su **Aggiungi regola**.
3. Creare una regola che usa **Temperature** come campo dati e **AlarmTemp** come output quando la temperatura supera i 47 gradi:
   
    ![Aggiungere una regola per il dispositivo][img-adddevicerule]
4. Per salvare le modifiche, fare clic su **Salva e visualizza regole**.
5. Fare clic su **Comandi** nel riquadro dei dettagli del nuovo dispositivo.
   
   ![Aggiungere una regola per il dispositivo][img-adddevicerule2]
6. Selezionare **ChangeSetPointTemp** nell'elenco dei comandi e impostare **SetPointTemp** su 45. Fare quindi clic su **Invia comando**:
   
   ![Aggiungere una regola per il dispositivo][img-adddevicerule3]
7. Tornare al dashboard della soluzione. Poco dopo verrà visualizzata una nuova voce nel riquadro **Cronologia avvisi** quando la temperatura segnalata dal nuovo dispositivo supera la soglia di 47 gradi:
   
   ![Aggiungere una regola per il dispositivo][img-adddevicerule4]
8. È possibile rivedere e modificare tutte le regole nella pagina **Regole** del dashboard:
   
    ![Elenco delle regole per il dispositivo][img-rules]
9. È possibile rivedere e modificare tutte le azioni che possono essere eseguite in risposta a una regola nella pagina **Azioni** del dashboard:
   
    ![Elenco delle azioni per il dispositivo][img-actions]

> [!NOTE]
> È possibile definire azioni che possono inviare un messaggio di posta elettronica o un SMS in risposta a una regola oppure eseguire un'integrazione con un sistema line-of-business tramite un'[app per la logica][lnk-logic-apps]. Per altre informazioni, vedere [Connettere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Azure IoT Suite][lnk-logicapptutorial].
> 
> 

## <a name="disable-and-remove-devices"></a>Disabilitare e rimuovere i dispositivi
È possibile disabilitare un dispositivo e dopo aver disabilitato è possibile rimuoverlo:

![Disabilitare e rimuovere un dispositivo][img-disable]

## <a name="run-jobs"></a>Eseguire i processi
È possibile pianificare processi per eseguire operazioni in blocco sui dispositivi. Si crea un processo per un elenco di dispositivi, che può contenere tutti i dispositivi oppure essere un elenco filtrato creato usando gli [strumenti di filtro](#filter-the-device-list) nell'elenco dei dispositivi. Un processo può richiamare un metodo su ogni dispositivo dell'elenco oppure aggiornare il dispositivo gemello di ogni dispositivo dell'elenco.

### <a name="create-a-job-to-invoke-a-method"></a>Creare un processo per richiamare un metodo

La procedura seguente illustra come creare un processo che richiama il metodo di aggiornamento del firmware su ogni dispositivo di un elenco. Il metodo viene richiamato solo sui dispositivi da cui è supportato:

1. Usare gli strumenti di filtro nell'elenco dei dispositivi per creare un elenco dei dispositivi che riceveranno l'aggiornamento del firmware:
   
   ![Aprire l'editor dei filtri][img-editfiltericon]
2. Nell'elenco filtrato fare clic su **Job scheduler** (Utilità di pianificazione processi):
   
   ![Aprire l'utilità di pianificazione dei processi][img-clickjobscheduler]
3. Nel pannello **Pianifica processo** fare clic su **Richiama metodo**.
4. Nella pagina **Richiama metodo** immettere i dettagli del metodo da richiamare e quindi fare clic su **Pianifica**:
   
   ![Configurare il processo per un metodo][img-invokemethodjob]

Il metodo **InitiateFirmwareUpdate** avvia un'attività sul dispositivo in modo asincrono e restituisce immediatamente un valore. Il processo di aggiornamento del firmware usa quindi le proprietà segnalate per segnalare lo stato del processo di aggiornamento mentre è in esecuzione.

### <a name="create-a-job-to-edit-the-device-twin"></a>Creare un processo per modificare il dispositivo gemello

La procedura seguente illustra come creare un processo che modifica il dispositivo gemello di ogni dispositivo di un elenco:

1. Usare gli strumenti di filtro nell'elenco dei dispositivi per creare un elenco dei dispositivi che riceveranno le modifiche del dispositivo gemello:
   
   ![Aprire l'editor dei filtri][img-editfiltericon]
2. Nell'elenco filtrato fare clic su **Job scheduler** (Utilità di pianificazione processi):
   
   ![Aprire l'utilità di pianificazione dei processi][img-clickjobscheduler]
3. Nel pannello **Pianifica processo** fare clic su **Edit Device Twin** (Modifica dispositivo gemello).
4. Nella pagina **Edit Device Twin** (Modifica dispositivo gemello) immettere quindi i dettagli delle **proprietà desiderate** e dei **tag** da modificare e quindi fare clic su **Pianifica**:
   
   ![Configurare il processo per un metodo][img-edittwinjob]

### <a name="monitor-the-job"></a>Monitorare il processo
È possibile monitorare lo stato dei processi pianificati nella pagina **Processi di gestione**. Il pannello **Dettagli processo** visualizza informazioni sul processo selezionato:
   
   ![Visualizzare lo stato di un processo][img-jobstatus]

È possibile visualizzare informazioni sui processi anche in **Dashboard**:
   
   ![Visualizzare i processi nel dashboard][img-jobdashboard]


## <a name="behind-the-scenes"></a>Dietro le quinte
Quando si distribuisce una soluzione preconfigurata, il processo di distribuzione crea più risorse nella sottoscrizione di Azure selezionata. È possibile visualizzare queste risorse nel [portale][lnk-portal] di Azure. Il processo di distribuzione crea un **gruppo di risorse** con un nome basato sul nome scelto per la soluzione preconfigurata:

![Soluzione preconfigurata nel portale di Azure][img-portal]

È possibile visualizzare le impostazioni di ogni risorsa selezionandola nell'elenco di risorse nel gruppo di risorse.

È anche possibile visualizzare il codice sorgente per la soluzione preconfigurata. Il codice sorgente della soluzione preconfigurata per il monitoraggio remoto si trova nel repository GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* La cartella **DeviceAdministration** contiene il codice sorgente per il dashboard.
* La cartella **Simulator** contiene il codice sorgente per il dispositivo simulato.
* La cartella **EventProcessor** contiene il codice sorgente per il processo back-end che gestisce la telemetria in ingresso.

Al termine, è possibile eliminare la soluzione preconfigurata dalla sottoscrizione di Azure nel sito [azureiotsuite.com][lnk-azureiotsuite]. Questo sito consente di eliminare facilmente tutte le risorse di cui è stato effettuato il provisioning quando si è creata la soluzione preconfigurata.

> [!NOTE]
> Per assicurarsi di eliminare tutti gli elementi correlati alla soluzione preconfigurata, eliminarli dal sito [azureiotsuite.com][lnk-azureiotsuite] e non eliminare il gruppo di risorse nel portale.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver distribuito una soluzione preconfigurata è possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

* [Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto][lnk-rm-walkthrough]
* [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm]
* [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
