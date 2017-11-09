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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="get-started-with-the-preconfigured-solutions"></a>Introduzione alle soluzioni preconfigurate

Le [soluzioni preconfigurate][lnk-preconfigured-solutions] di Azure IoT Suite combinano più servizi IoT di Azure per fornire soluzioni end-to-end che implementano scenari aziendali IoT comuni. La soluzione preconfigurata per il *monitoraggio remoto* si connette ai dispositivi e li monitora. È possibile usare la soluzione per analizzare il flusso di dati dai dispositivi e di migliorare i risultati aziendali facendo in modo che i processi rispondano automaticamente a quel flusso di dati.

Questa esercitazione illustra come effettuare il provisioning della soluzione preconfigurata per il monitoraggio remoto. Ne descrive anche le funzionalità di base. È possibile accedere a molte di queste funzionalità dal *dashboard* distribuito come parte della soluzione preconfigurata:

![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-dashboard]

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [!NOTE]
> Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Panoramica dello scenario

Nel momento in cui viene distribuita, la soluzione preconfigurata per il monitoraggio remoto viene prepopolata con risorse che consentono di eseguire uno scenario di monitoraggio remoto comune. In questo scenario, vari dispositivi connessi alla soluzione registrano valori di temperatura imprevisti. Le sezioni seguenti mostrano come:

* Identificare i dispositivi che inviano valori di temperatura imprevisti.
* Configurare i dispositivi per l'invio di dati di telemetria più dettagliati.
* Risolvere il problema aggiornando il firmware dei dispositivi.
* Verificare che il problema sia stato risolto.

Una particolarità di questo scenario consiste nella possibilità di eseguire tutte queste azioni in remoto dal dashboard della soluzione, senza dover accedere fisicamente ai dispositivi.

## <a name="view-the-solution-dashboard"></a>Visualizzare il dashboard della soluzione

Il dashboard della soluzione consente di gestire la soluzione distribuita. Ad esempio, è possibile visualizzare dati di telemetria, aggiungere dispositivi e configurare regole.

1. Al termine del provisioning, quando il riquadro della soluzione preconfigurata indica **Pronto**, scegliere **Avvia** per aprire il portale della soluzione di monitoraggio remoto in una nuova scheda.

    ![Avviare la soluzione preconfigurata][img-launch-solution]

1. Per impostazione predefinita, il portale della soluzione visualizza il *dashboard*. È possibile passare ad altre aree del portale della soluzione usando il menu sul lato sinistro della pagina.

    ![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-menu]

Il dashboard visualizza le informazioni seguenti:

* Una mappa che visualizza la posizione di ogni dispositivo connesso alla soluzione. Quando si esegue la soluzione per la prima volta, sono disponibili 25 dispositivi simulati. I dispositivi simulati vengono implementati come Processi Web di Azure e la soluzione usa l'API Bing Maps per tracciare le informazioni sulla mappa. Vedere le [domande frequenti][lnk-faq] per informazioni su come rendere dinamica la mappa.
* Un pannello **Cronologia telemetria** che traccia la telemetria di umidità e temperatura da un dispositivo selezionato in tempo quasi reale e visualizza i dati aggregati, ad esempio l'umidità massima, minima e media.
* Un pannello **Cronologia avvisi** che mostra recenti eventi di avviso relativi a valori di telemetria che hanno superato il limite soglia. È possibile definire i propri avvisi oltre agli esempi creati dalla soluzione preconfigurata.
* Un pannello **Processi** che visualizza informazioni sui processi pianificati. È possibile pianificare i propri processi nella pagina **Processi di gestione**.

## <a name="view-alarms"></a>Visualizzare gli avvisi

Il pannello Cronologia avvisi indica che in cinque dispositivi sono stati registrati valori di telemetria più alti del previsto.

![Cronologia avvisi nel dashboard della soluzione][img-alarms]

> [!NOTE]
> Questi avvisi vengono generati da una regola inclusa nella soluzione preconfigurata, che genera un avviso ogni volta che il valore di temperatura inviato da un dispositivo supera 60. È possibile definire regole e azioni personalizzate scegliendo [Regole](#add-a-rule) e [Azioni](#add-an-action) nel menu a sinistra.

## <a name="view-devices"></a>Visualizzare i dispositivi

L'elenco dei *dispositivi* mostra tutti i dispositivi registrati nella soluzione. Da tale elenco è possibile visualizzare e modificare i metadati dei dispositivi, aggiungere o rimuovere dispositivi e richiamare metodi su di essi. Nell'elenco dei dispositivi è possibile anche filtrare e ordinare l'elenco dei dispositivi, nonché personalizzare le colonne visualizzate.

1. Scegliere **Dispositivi** per visualizzare l'elenco dei dispositivi relativi alla soluzione.

   ![Visualizzare l'elenco dei dispositivi nel portale della soluzione][img-devicelist]

1. L'elenco dei dispositivi mostra inizialmente 25 dispositivi simulati creati dal processo di provisioning. È possibile aggiungere altri dispositivi fisici e simulati alla soluzione.

1. Scegliere un dispositivo dell'elenco per visualizzarne i dettagli.

   ![Visualizzare i dettagli di un dispositivo nel portale della soluzione][img-devicedetails]

Il pannello **Dettagli dispositivo** contiene sei sezioni:

* Una raccolta di collegamenti che consentono di personalizzare l'icona del dispositivo, disabilitare il dispositivo, aggiungere una regola, richiamare un metodo o inviare un comando. Per un confronto dei comandi (messaggi da dispositivo a cloud) e dei metodi (metodi diretti), vedere [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance].
* La sezione **Device Twin (Dispositivo gemello) - Tag** consente di modificare i valori dei tag per il dispositivo. È possibile visualizzare i valori dei tag nell'elenco dei dispositivi e usarli per filtrare l'elenco.
* La sezione **Device Twin (Dispositivo gemello) - Proprietà desiderate** consente di impostare i valori delle proprietà da inviare al dispositivo.
* La sezione **Device Twin (Dispositivo gemello) - Proprietà segnalate** visualizza i valori delle proprietà inviati dal dispositivo.
* La sezione **Proprietà dispositivo** visualizza informazioni del registro di identità, come l'ID dispositivo e le chiavi di autenticazione.
* La sezione **Processi recenti** visualizza informazioni sui processi recentemente indirizzati al dispositivo.

## <a name="filter-the-device-list"></a>Filtrare l'elenco dei dispositivi

È possibile usare un filtro per visualizzare solo i dispositivi che inviano valori di temperatura imprevisti. La soluzione preconfigurata per il monitoraggio remoto include il filtro **Dispositivi non integri** che consente di visualizzare i dispositivi con un valore di temperatura media superiore a 60. È possibile anche [creare filtri personalizzati](#add-a-filter).

1. Scegliere **Apri filtro salvato** per visualizzare un elenco dei filtri disponibili. Scegliere quindi **Dispositivi non integri** per applicare il filtro:

    ![Visualizzare l'elenco dei filtri][img-unhealthy-filter]

1. Nell'elenco dei dispositivi sono ora visualizzati solo i dispositivi con un valore di temperatura media superiore a 60.

    ![Visualizzare l'elenco dei dispositivi filtrato in modo che siano riportati solo i dispositivi non integri][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Aggiornare le proprietà desiderate

È stato identificato un set di dispositivi per i quali può essere necessaria un'azione correttiva. Si decide tuttavia che una frequenza dati pari a 15 secondi non è sufficiente per una diagnosi esatta del problema. Si imposta quindi la frequenza di telemetria su cinque secondi in modo da avere a disposizione una maggiore quantità di punti dati e diagnosticare meglio il problema. Questa modifica di configurazione può essere implementata nei dispositivi remoti dal portale della soluzione. È possibile, ad esempio, apportare la modifica una volta, valutarne l'impatto e, in base ai risultati, intraprendere le azioni necessarie.

Seguire questa procedura per eseguire un processo che consente di impostare la proprietà **TelemetryInterval** sul valore desiderato nei dispositivi interessati. Nel momento in cui dispositivi ricevono il nuovo valore della proprietà **TelemetryInterval**, modificano la propria configurazione in modo da inviare i dati di telemetria ogni cinque secondi anziché ogni 15 secondi.

1. Con l'elenco dei dispositivi impostato in modo da visualizzare solo i dispositivi non integri, scegliere **Pianificatore di processi** e quindi **Modifica dispositivo gemello**.

1. Chiamare il processo **Change telemetry interval**.

1. Impostare il valore di **Nome proprietà desiderata** **desired.Config.TelemetryInterval** su cinque secondi.

1. Scegliere **Pianifica**.

    ![Impostare la proprietà TelemetryInterval su cinque secondi][img-change-interval]

1. È possibile monitorare lo stato di avanzamento del processo nella pagina **Processi di gestione** del portale.

> [!NOTE]
> Se si vuole modificare il valore di una proprietà desiderata per un singolo dispositivo, usare la sezione **Proprietà desiderate** disponibile nel pannello **Dettagli dispositivo** anziché eseguire un processo.

Questo processo imposta il valore della proprietà desiderata **TelemetryInterval** nel dispositivo gemello di tutti i dispositivi selezionati dal filtro. I dispositivi recuperano quindi questo valore dal rispettivo dispositivo gemello e aggiornano il proprio comportamento. Quando un dispositivo recupera ed elabora una proprietà desiderata da un dispositivo gemello, imposta la proprietà del valore restituito corrispondente.

## <a name="invoke-methods"></a>Richiamare i metodi

Durante l'esecuzione del processo è possibile osservare come le versioni del firmware dei dispositivi non integri siano tutte non aggiornate (inferiori alla versione 1.6).

![Visualizzare la versione del firmware dei dispositivi non integri][img-old-firmware]

La versione del firmware può essere la causa principale dei valori di temperatura imprevisti perché nel frattempo altri dispositivi integri sono stati aggiornati alla versione 2.0. È possibile usare il filtro integrato **Old firmware devices** (Dispositivi con firmware non aggiornato) per identificare eventuali dispositivi con una versione del firmware non aggiornata. Dal portale è possibile aggiornare in remoto tutti i dispositivi che ancora eseguono versioni del firmware non aggiornate.

1. Scegliere **Apri filtro salvato** per visualizzare un elenco dei filtri disponibili. Scegliere quindi **Old firmware devices** (Dispositivi con firmware non aggiornato) per applicare il filtro.

    ![Visualizzare l'elenco dei filtri][img-old-filter]

1. Nell'elenco dei dispositivi sono ora visualizzati solo i dispositivi con versioni del firmware non aggiornate, inclusi i cinque dispositivi identificati dal filtro **Dispositivi non integri** e altri tre dispositivi.

    ![Visualizzare l'elenco dei dispositivi filtrato in modo che siano riportati solo i dispositivi con versioni del firmware non aggiornate][img-filtered-old-list]

1. Scegliere **Pianificatore di processi** e quindi **Richiama metodo**.

1. Impostare **Nome processo** su **Firmware update to version 2.0**.

1. Scegliere **InitiateFirmwareUpdate** come **Metodo**.

1. Impostare il parametro **FwPackageUri** su **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Scegliere **Pianifica**. Per impostazione predefinita, il processo viene eseguito ora.

    ![Creare un processo per aggiornare il firmware dei dispositivi selezionati][img-method-update]

> [!NOTE]
> Se si vuole richiamare un metodo su un singolo dispositivo, scegliere **Metodi** nel pannello **Dettagli dispositivo** anziché eseguire un processo.

Con questo processo viene richiamato il metodo diretto **InitiateFirmwareUpdate** su tutti i dispositivi selezionati dal filtro. I dispositivi rispondono immediatamente all'hub IoT e quindi avviano il processo di aggiornamento del firmware in modo asincrono. I dispositivi usano inoltre i valori di proprietà restituiti per fornire informazioni sullo stato del processo di aggiornamento del firmware, come illustrato nelle schermate seguenti. Scegliere l'icona **Aggiorna** per aggiornare le informazioni negli elenchi di processi e dispositivi.

![Elenco di processi con l'elenco degli aggiornamenti di firmware in esecuzione][img-update-1]
![Elenco di dispositivi con lo stato di aggiornamento del firmware][img-update-2]
![Elenco di processi con l'elenco degli aggiornamenti di firmware completati][img-update-3]

> [!NOTE]
> In un ambiente di produzione è possibile pianificare i processi in modo che vengano eseguiti in un intervallo di manutenzione prestabilito.

## <a name="scenario-review"></a>Analisi dello scenario

In questo scenario è stato identificato un potenziale problema in alcuni dispositivi remoti usando la cronologia degli avvisi sul dashboard e un filtro. Sono stati quindi usati il filtro e un processo per configurare in remoto i dispositivi in modo da fornire altre informazioni per facilitare la diagnosi del problema. Sono stati usati infine un filtro e un processo per pianificare la manutenzione dei dispositivi interessati. Se si torna al dashboard è possibile osservare che non è più presente alcun avviso proveniente da dispositivi inclusi nella soluzione. Con un apposito filtro è possibile anche verificare che il firmware sia aggiornato su tutti i dispositivi della soluzione e che non siano più presenti dispositivi non integri.

![Filtro che mostra come il firmware di tutti i dispositivi sia aggiornato][img-updated]

![Filtro che mostra come tutti i dispositivi siano integri][img-healthy]

## <a name="other-features"></a>Altre funzionalità

Le sezioni seguenti descrivono alcune funzionalità aggiuntive della soluzione preconfigurata per il monitoraggio remoto non descritte nell'ambito dello scenario precedente.

### <a name="customize-columns"></a>Personalizzare le colonne

È possibile personalizzare le informazioni visualizzate nell'elenco dei dispositivi scegliendo **Editor di colonne**. È possibile aggiungere e rimuovere colonne che visualizzano i valori dei tag e delle proprietà segnalate, nonché riordinare e rinominare le colonne:

   ![Editor di colonne nell'elenco dei dispositivi][img-columneditor]

### <a name="customize-the-device-icon"></a>Personalizzare l'icona del dispositivo

È possibile personalizzare l'icona del dispositivo visualizzata nell'elenco dei dispositivi dal pannello **Dettagli dispositivo**, come indicato di seguito:

1. Scegliere l'icona a forma di matita per aprire il pannello per la **modifica dell'immagine** per un dispositivo:

   ![Editor di immagini per il dispositivo: apertura][img-startimageedit]

1. Caricare una nuova immagine oppure usarne una esistente e quindi scegliere **Salva**:

   ![Editor di immagini per il dispositivo: modifica][img-imageedit]

1. L'immagine selezionata verrà visualizzata nella colonna **Icona** del dispositivo.

> [!NOTE]
> L'immagine viene archiviata in un archivio BLOB. Un tag nel dispositivo gemello contiene un collegamento all'immagine nell'archivio BLOB.

### <a name="add-a-device"></a>Aggiungere un dispositivo

Quando si distribuisce la soluzione preconfigurata, viene automaticamente effettuato il provisioning di 25 dispositivi che vengono visualizzati nell'elenco dei dispositivi. Si tratta di *dispositivi simulati* in esecuzione in un processo Web di Azure. I dispositivi simulati consentono di provare facilmente la soluzione preconfigurata senza la necessità di distribuire dispositivi fisici reali. Per connettere un dispositivo reale alla soluzione, vedere l'esercitazione [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm].

La procedura seguente illustra come aggiungere un dispositivo simulato alla soluzione:

1. Tornare all'elenco dei dispositivi.

1. Per aggiungere un dispositivo, scegliere **Aggiungi un dispositivo** nell'angolo inferiore sinistro.

   ![Aggiungere un dispositivo alla soluzione preconfigurata][img-adddevice]

1. Scegliere **Aggiungi nuovo** nel riquadro **Dispositivo simulato**.

   ![Impostare i dettagli del nuovo dispositivo nel dashboard][img-addnew]

   Oltre a creare un nuovo dispositivo simulato, è anche possibile aggiungere un dispositivo fisico se si sceglie di creare un **dispositivo personalizzato**. Per altre informazioni sulla connessione di dispositivi fisici alla soluzione, vedere [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite][lnk-connect-rm].

1. Selezionare **Let me define my own Device ID** (Definire l'ID dispositivo personale) e immettere un nome di ID dispositivo univoco, ad esempio **mydevice_01**.

1. Scegliere **Create**.

   ![Salvare un nuovo dispositivo][img-definedevice]

1. Nel passaggio 3 della procedura **Add a simulated device** (Aggiungi un dispositivo simulato) scegliere **Fatto** per tornare all'elenco dei dispositivi.

1. Verificare che lo stato del dispositivo nell'elenco sia **In esecuzione** .

    ![Visualizzare il nuovo dispositivo nell'elenco dei dispositivi][img-runningnew]

1. È anche possibile visualizzare la telemetria simulata dal nuovo dispositivo nel dashboard:

    ![Visualizzare i dati di telemetria dal nuovo dispositivo][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Disabilitare e rimuovere un dispositivo

È possibile disabilitare un dispositivo e dopo aver disabilitato è possibile rimuoverlo:

![Disabilitare e rimuovere un dispositivo][img-disable]

### <a name="add-a-rule"></a>Aggiungere una regola

Non sono presenti regole per il nuovo dispositivo appena aggiunto. In questa sezione si aggiunge una regola che attiva un avviso quando la temperatura segnalata dal nuovo dispositivo supera i 47 gradi. Prima di iniziare, si noti che la cronologia della telemetria per il nuovo dispositivo nel dashboard indica che la temperatura del dispositivo non supera mai i 45 gradi.

1. Tornare all'elenco dei dispositivi.

1. Per aggiungere una regola per il dispositivo, selezionare il nuovo dispositivo nell'**elenco dei dispositivi** e quindi scegliere **Aggiungi regola**.

1. Creare una regola che usa **Temperature** come campo dati e **AlarmTemp** come output quando la temperatura supera i 47 gradi:

    ![Aggiungere una regola per il dispositivo][img-adddevicerule]

1. Per salvare le modifiche, scegliere **Salva e visualizza regole**.

1. Scegliere **Comandi** nel riquadro dei dettagli del nuovo dispositivo.

    ![Aggiungere una regola per il dispositivo][img-adddevicerule2]

1. Selezionare **ChangeSetPointTemp** nell'elenco dei comandi e impostare **SetPointTemp** su 45. Scegliere quindi **Invia comando**.

    ![Aggiungere una regola per il dispositivo][img-adddevicerule3]

1. Tornare al dashboard. Poco dopo verrà visualizzata una nuova voce nel riquadro **Cronologia avvisi** quando la temperatura segnalata dal nuovo dispositivo supera la soglia di 47 gradi:

    ![Aggiungere una regola per il dispositivo][img-adddevicerule4]

1. È possibile rivedere e modificare tutte le regole nella pagina **Regole** del dashboard:

    ![Elenco delle regole per il dispositivo][img-rules]

1. È possibile rivedere e modificare tutte le azioni che possono essere eseguite in risposta a una regola nella pagina **Azioni** del dashboard:

    ![Elenco delle azioni per il dispositivo][img-actions]

> [!NOTE]
> È possibile definire azioni che possono inviare un messaggio di posta elettronica o un SMS in risposta a una regola oppure eseguire un'integrazione con un sistema line-of-business tramite un'[app per la logica][lnk-logic-apps]. Per altre informazioni, vedere [Connettere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Azure IoT Suite][lnk-logicapptutorial].

### <a name="manage-filters"></a>Gestire i filtri

Nell'elenco dei dispositivi è possibile creare, salvare e ricaricare filtri per visualizzare un elenco personalizzato dei dispositivi connessi all'hub. Per creare un filtro:

1. Scegliere l'icona di modifica dei filtri sopra l'elenco dei dispositivi:

    ![Aprire l'editor dei filtri][img-editfiltericon]

1. In **Editor dei filtri** aggiungere i campi, gli operatori e i valori per filtrare l'elenco dei dispositivi. È possibile aggiungere più clausole per perfezionare il filtro. Scegliere **Filtra** per applicare il filtro:

    ![Creare un filtro][img-filtereditor]

1. In questo esempio, l'elenco è filtrato in base al produttore e al numero di modello:

    ![Elenco filtrato][img-filterelist]

1. Per salvare il filtro con un nome personalizzato, scegliere l'icona **Salva con nome**:

    ![Salvare un filtro][img-savefilter]

1. Per riapplicare un filtro salvato in precedenza, scegliere l'icona **Apri filtro salvato**:

    ![Aprire un filtro][img-openfilter]

È possibile creare filtri basati sull'ID dispositivo, sullo stato del dispositivo, sulle proprietà desiderate, sulle proprietà segnalate e sui tag. Per aggiungere tag personalizzati a un dispositivo è possibile usare la sezione **Tag** del pannello **Dettagli dispositivo** oppure eseguire un processo per l'aggiornamento dei tag in più dispositivi.

> [!NOTE]
> In **Editor dei filtri** è possibile usare la **visualizzazione avanzata** per modificare direttamente il testo della query.

### <a name="commands"></a>Comandi:

Dal pannello **Dettagli dispositivo** è possibile inviare comandi al dispositivo. Quando un dispositivo viene avviato per la prima volta, invia alla soluzione informazioni sui comandi che supporta. Per un approfondimento sulle differenze tra comandi e metodi, vedere [Opzioni da cloud a dispositivo di Azure IoT Hub][lnk-c2d-guidance].

1. Scegliere **Comandi** nel pannello **Dettagli dispositivo** del dispositivo selezionato:

   ![Comandi del dispositivo nel dashboard][img-devicecommands]

1. Selezionare **PingDevice** dall'elenco dei comandi.

1. Scegliere **Invia comando**.

1. È possibile visualizzare lo stato del comando nella cronologia dei comandi.

   ![Stato dei comandi nel dashboard][img-pingcommand]

La soluzione tiene traccia dello stato di ogni comando inviato. All'inizio il risultato è **In sospeso**. Quando il dispositivo segnala che il comando è stato eseguito, il risultato viene impostato su **Operazione riuscita**.

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

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito una soluzione preconfigurata è possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

* [Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto][lnk-rm-walkthrough]
* [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm]
* [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md