---
title: Introduzione alla soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione usa scenari simulati per introdurre la soluzione preconfigurata di monitoraggio remoto. Questi scenari vengono creati quando si distribuisce la soluzione preconfigurata di monitoraggio remoto per la prima volta.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f4461b5c10ea4e4cce4c13cb590dd398c37cb7b6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-preconfigured-solution"></a>Esplorare le funzionalità della soluzione preconfigurata di monitoraggio remoto

Questa esercitazione mostra le principali funzionalità della soluzione di monitoraggio remoto. Per presentare queste funzionalità, l'esercitazione mostra alcuni scenari di clienti comuni usando un'applicazione IoT simulata per una società denominata Contoso.

L'esercitazione aiuta a identificare i tipici scenario IoT predefiniti forniti dalla soluzione di monitoraggio remoto.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Visualizzare e filtrare dispositivi nel dashboard
> * Rispondere a un allarme
> * Aggiornare il firmware nei dispositivi
> * Organizzare gli asset

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, è necessario completare l'esercitazione [Distribuire la soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="the-contoso-sample-iot-deployment"></a>Distribuzione IoT di esempio Contoso

È possibile usare la distribuzione IoT di esempio Contoso per comprendere gli scenari di base predefiniti forniti dalla soluzione di monitoraggio remoto. Questi scenari sono basati su distribuzioni IoT reali. Molto presumibilmente, si sceglierà di personalizzare la soluzione di monitoraggio remoto in base a requisiti specifici, ma l'esempio Contoso consente di apprendere le nozioni di base.

> [!NOTE]
> Se è stata usata l'interfaccia della riga di comando per distribuire la soluzione preconfigurata, il file `deployment-{your deployment name}-output.json` contiene informazioni sulla distribuzione, tra cui l'URL per accedere all'esempio distribuito.

L'esempio Contoso effettua il provisioning di un set di dispositivi simulati e le regole per gestirli. Una volta compresi gli scenari di base, è possibile continuare a esplorare altre funzionalità della soluzione in [Eseguire il monitoraggio avanzato dei dispositivi tramite la soluzione di monitoraggio remoto](iot-suite-remote-monitoring-monitor.md).

Contoso è una società che gestisce un'ampia gamma di asset in ambienti diversi. Contoso prevede di sfruttare tutta la potenza delle applicazioni IoT basate sul cloud per monitorare e gestire in remoto più asset da un'applicazione centralizzata. Le sezioni seguenti offrono un riepilogo della configurazione iniziale dell'esempio Contoso:

> [!NOTE]
> La demo Contoso è solo uno dei modi in cui effettuare il provisioning di dispositivi simulati e creare regole. Altre opzioni di provisioning includono la creazione di dispositivi personalizzati. Per altre informazioni su come creare regole e dispositivi personalizzati, vedere [Gestire e configurare i dispositivi](iot-suite-remote-monitoring-manage.md) e [Rilevare i problemi usando regole basate su soglie](iot-suite-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Dispositivi Contoso

Contoso usa diversi tipi di dispositivi avanzati. Questi dispositivi svolgono ruoli diversi per la società, inviando vari flussi di telemetria. Inoltre, ogni tipo di dispositivo ha proprietà e metodi supportati diversi.

La tabella seguente mostra un riepilogo dei tipi di dispositivo di cui viene effettuato il provisioning:

| Tipo di dispositivo        | Telemetria                                  | Proprietà                                  | Tag                    | Metodi                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chiller (Refrigeratore)            | Temperature (Temperatura), Humidity (Umidità), Pressure (Pressione)            | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Floor (Piano), Campus (Complesso) | Reboot (Riavvia), Firmware Update (Aggiornamento firmware), Emergency Valve Release (Rilascio valvola di emergenza), Increase Pressure (Aumenta pressione)                          |
| Prototyping device (Dispositivo di prototipazione) | Temperature (Temperatura), Pressure (Pressione), Geo-location (Posizione geografica)        | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Mode (Modalità)          | Reboot (Riavvio), Firmware Update (Aggiornamento firmware), Move device (Sposta dispositivo), Stop device (Arresta dispositivo), Temperature release (Riduzione temperatura), Temperature increase (Aumento temperatura) |
| Engine (Motore)             | Tank fuel level (Livello carburante serbatoio), Coolant sensor (Sensore liquido di raffreddamento), Vibration (Vibrazione) | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Floor (Piano), Campus (Complesso) | Restart (Riavvia), Firmware Update (Aggiornamento firmware), Empty tank (Svuota serbatoio), Fill tank (Riempi serbatoio)                                              |
| Truck (Veicolo)              | Geo-location (Posizione geografica), Speed (Velocità), Cargo temperature (Temperatura carico)     | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Load (Carico)          | Lower cargo temperature (Riduci temperatura carico), Increase cargo temperature (Aumenta temperatura carico), Firmware update (Aggiornamento firmware)                         |
| Elevator (Montacarichi)           | Floor (Piano), Vibration (Vibrazione), Temperature (Temperatura)              | Type (Tipo), Firmware version (Versione firmware), Model (Modello), Geo-location (Posizione geografica) | Location (Località), Campus (Complesso)        | Stop elevator (Arresta montacarichi), Start elevator (Avvia montacarichi), Firmware update (Aggiornamento firmware)                                               |

> [!NOTE]
> L'esempio di demo Contoso effettua il provisioning di due dispositivi per tipo. Per ogni tipo, uno funziona correttamente entro i limiti definiti normali da Contoso, mentre l'altro ha problemi di funzionamento. Nella sezione seguente vengono descritte le regole definite da Contoso per i dispositivi. Queste regole definiscono i limiti del comportamento corretto.

### <a name="contoso-rules"></a>Regole Contoso

Gli operatori Contoso conoscono le soglie che determinano se un dispositivo funziona o meno correttamente. Ad esempio, un refrigeratore non funziona correttamente se la pressione segnalata è maggiore di 250 PSI. La tabella seguente mostra le regole basate su soglie definite da Contoso per ogni tipo di dispositivo:

| Nome regola | Descrizione | Soglia | Severity | Dispositivi interessati |
| --------- | ----------- | --------- | -------- | ---------------- |
| Chiller pressure too high (Pressione refrigeratore troppo alta) | Genera un allarme se i refrigeratori raggiungono livelli di pressione superiori a quelli normali   |P > 250 psi       | Critico | Refrigeratori            |
| Prototyping device temp too high (Temperatura dispositivo di prototipazione troppo alta)  | Genera un allarme se i dispositivi di prototipazione raggiungono livelli di temperatura superiori a quelli normali  |T > 80 &deg;F |Critico | Dispositivi di prototipazione |
| Engine tank empty (Serbatoio motore vuoto)  | Genera un allarme se il serbatoio di carburante si svuota                     | C < 5 galloni | Info     | Motori             |
| Higher than normal cargo temperature (Temperatura carico maggiore del normale) | Genera un allarme se la temperatura del carico del veicolo è maggiore di quella normale                 | T < 45 &deg;F |Avviso  | Veicoli              |
| Elevator vibration stopped (Vibrazione montacarichi arrestata)      | Genera un allarme se il montacarichi si arresta completamente (in base al livello di vibrazione)                     | V < 0,1 mm |Avviso  | Montacarichi           |

### <a name="operate-the-contoso-sample-deployment"></a>Eseguire la distribuzione di esempio Contoso

È stata mostrata la configurazione iniziale nell'esempio Contoso. Le sezioni seguenti descrivono tre scenari nell'esempio Contoso che mostrano il modo in cui un operatore può usare la soluzione preconfigurata.

## <a name="respond-to-a-pressure-alarm"></a>Rispondere a un allarme di pressione

Questo scenario mostra come identificare e gestire un allarme attivato da un dispositivo refrigeratore. Il refrigeratore si trova a Redmond, nell'edificio 43 al piano 2.

L'operatore può notare nel dashboard la presenza di un allarme relativo alla pressione di un refrigeratore. È possibile eseguire la panoramica o lo zoom sulla mappa per visualizzare altri dettagli.

1. Nella pagina **Dashboard** nella griglia **System Alarms** (Allarmi sistema) è possibile osservare l'allarme **Chiller pressure too high** (Pressione refrigeratore troppo alta). Il refrigeratore è anche visualizzato sulla mappa:

    ![Dashboard che mostra l'allarme di pressione e il dispositivo sulla mappa](media/iot-suite-remote-monitoring-explore/dashboardalarm.png)

1. Per visualizzare i dettagli del dispositivo e i dati di telemetria, fare clic sul refrigeratore evidenziato sulla mappa. I dati di telemetria indicano un picco di pressione:

    ![Scegliere il dispositivo sulla mappa per visualizzare i dettagli](media/iot-suite-remote-monitoring-explore/dashboarddetail.png)

1. Chiudere **Device detail** (Dettagli dispositivo).

1. Per passare alla pagina **Maintenance** (Manutenzione), fare clic su **...** nella colonna **Explore Alarm** (Esplora allarme) vicino all'allarme nella griglia degli allarmi.

Nella pagina **Maintenance** (Manutenzione) è possibile visualizzare i dettagli della regola che ha attivato l'allarme di pressione del refrigeratore.

1. È possibile esaminare il numero di volte in cui l'allarme è stato attivato, le conferme e gli allarmi aperti e chiusi:

    ![Pagina Maintenance (Manutenzione) che mostra l'elenco degli avvisi attivati](media/iot-suite-remote-monitoring-explore/maintenancealarmlist.png)

1. Il primo allarme nell'elenco è quello più recente. Fare clic sull'allarme **Chiller Pressure** (Pressione refrigeratore) per visualizzare i dati di telemetria e i dispositivi associati. I dati di telemetria indicano un picco di pressione per il refrigeratore:

    ![Pagina Maintenance (Manutenzione) che mostra i dati di telemetria per un allarme selezionato](media/iot-suite-remote-monitoring-explore/maintenancetelemetry.png)

Sono stati ora identificati il problema che ha attivato l'allarme e il dispositivo associato. Per un operatore, i passaggi successivi consistono nel confermare l'allarme e correggere il problema.

1. Per indicare che si sta intervenendo sull'allarme, modificare il valore di **Alarm status** (Stato allarme) in **Acknowledged** (Confermato):

    ![Selezionare e confermare l'allarme](media/iot-suite-remote-monitoring-explore/maintenanceacknowledge.png)

1. Per intervenire sul refrigeratore, selezionarlo e quindi scegliere **Schedule** (Pianifica). Selezionare **EmergencyValveRelease**, aggiungere il nome di processo **ChillerPressureRelease** e quindi scegliere **Apply** (Applica). Queste impostazioni creano un processo che viene eseguito immediatamente:

    ![Selezionare il dispositivo e pianificare un'azione](media/iot-suite-remote-monitoring-explore/maintenanceschedule.png)

1. Per visualizzare lo stato del processo, tornare alla pagina **Maintenance** (Manutenzione) e visualizzare l'elenco di processi nella visualizzazione **System Status** (Stato sistema). È possibile notare che il processo è stato eseguito per rilasciare la pressione della valvola sul refrigeratore:

    ![Stato dei processi in System Status (Stato sistema)](media/iot-suite-remote-monitoring-explore/maintenancerunningjob.png)

Infine, verificare che i valori di telemetria per il refrigeratore siano tornati normali.

1. Per visualizzare la griglia degli allarmi, passare alla pagina **Dashboard**.

1. Per visualizzare i dati di telemetria del dispositivo, selezionare il dispositivo per l'allarme originale sulla mappa e verificare che sia tornato al valore normale.

1. Per chiudere l'evento imprevisto, passare alla pagina **Maintenance** (Manutenzione), selezionare l'allarme e impostare lo stato su **Closed** (Chiuso):

    ![Selezionare e chiudere l'allarme](media/iot-suite-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Aggiornare il firmware del dispositivo

Contoso sta testando un nuovo tipo di dispositivo sul campo. Nell'ambito del ciclo di test è necessario verificare che gli aggiornamenti del firmware del dispositivo funzionino correttamente. I passaggi seguenti mostrano come usare la soluzione di monitoraggio remoto per aggiornare il firmware su più dispositivi.

Per eseguire le attività di gestione dei dispositivi necessarie, usare la pagina **Devices** (Dispositivi). Per iniziare, filtrare tutti i dispositivi di prototipazione:

1. Passare al pannello **Devices** (Dispositivi). Scegliere il filtro **Prototyping devices** (Dispositivi di prototipazione) nell'elenco a discesa **Filters** (Filtri):

    ![Usare un filtro nella pagina dei dispositivi](media/iot-suite-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Fare clic su **Manage** (Gestisci) per gestire i filtri disponibili.

1. Selezionare uno dei dispositivi di prototipazione:

    ![Selezionare un dispositivo nella pagina dei dispositivi](media/iot-suite-remote-monitoring-explore/devicesselect.png)

1. Fare clic sul pulsante **Schedule** (Pianifica) e quindi scegliere **Firmware update** (Aggiornamento firmware). Immettere i valori per **Job name** (Nome processo) e **Firmware URI** (URI firmware). Scegliere **Apply** (Applica) per pianificare l'esecuzione del processo a questo punto:

    ![Pianificare l'aggiornamento del firmware nel dispositivo](media/iot-suite-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Con i dispositivi simulati è possibile usare qualsiasi URI desiderato come valore di **Firmware URI** (URI firmware). I dispositivi simulati non accedono all'URL.

1. Notare quanti dispositivi sono interessati dal processo e scegliere **Apply** (Applica):

    ![Inviare il processo di aggiornamento del firmware](media/iot-suite-remote-monitoring-explore/devicessubmitupdate.png)

È possibile usare la pagina **Maintenance** (Manutenzione) per monitorare il processo durante la sua esecuzione.

1. Per visualizzare l'elenco dei processi, passare alla pagina **Maintenance** (Manutenzione) e fare clic su **System Status** (Stato sistema).

1. Individuare l'evento relativo al processo creato. Verificare che il processo di aggiornamento del firmware sia stato avviato correttamente.

È possibile creare un filtro per verificare che la versione del firmware sia stata aggiornata correttamente.

1. Per creare un filtro, passare alla pagina **Devices** (Dispositivi) e selezionare **Manage** (Gestisci):

    ![Gestire i filtri dei dispositivi](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Creare un filtro che includa solo i dispositivi con la nuova versione del firmware:

    ![Creare un filtro per i dispositivi](media/iot-suite-remote-monitoring-explore/devicescreatefilter.png)

1. Tornare alla pagina **Devices** (Dispositivi) e verificare che nel dispositivo sia presente la nuova versione del firmware.

## <a name="organize-your-assets"></a>Organizzare gli asset

In Contoso sono presenti due team diversi per le attività di manutenzione sul campo:

* Il team Smart Building gestisce i refrigeratori, i montacarichi e i motori.
* Il team Smart Vehicle gestisce i veicoli e i dispositivi di prototipazione.

Per semplificare l'organizzazione e la gestione dei dispositivi per un operatore, è possibile contrassegnarli con il nome del team appropriato.

È possibile creare nomi di tag da usare con i dispositivi.

1. Per visualizzare tutti i dispositivi, passare alla pagina **Devices** (Dispositivi) e scegliere il filtro **All devices** (Tutti i dispositivi):

    ![Visualizzazione di tutti i dispositivi](media/iot-suite-remote-monitoring-explore/devicesalldevices.png)

1. Selezionare i dispositivi **Trucks** (Veicoli) e **Prototyping devices** (Dispositivi di prototipazione). Scegliere quindi **Tag**:

    ![Selezionare i dispositivi veicolo e di prototipazione](media/iot-suite-remote-monitoring-explore/devicesmultiselect.png)

1. Scegliere **Tag** e quindi creare un nuovo tag di stringa denominato **FieldService**con valore **ConnectedVehicle**. Scegliere un nome per il processo. Fare quindi clic su **Apply**(Applica):

    ![Aggiungere tag ai dispositivi veicolo e di prototipazione](media/iot-suite-remote-monitoring-explore/devicesaddtag.png)

1. Selezionare i dispositivi **Chiller** (Refrigeratore), **Elevator** (Montacarichi) e **Engine** (Motore). Scegliere quindi **Tag**:

    ![Selezionare i dispositivi refrigeratore, montacarichi e motore](media/iot-suite-remote-monitoring-explore/devicesmultiselect2.png)

1. Scegliere **Tag** e quindi creare un nuovo tag denominato **FieldService**con valore **SmartBuilding**. Scegliere un nome per il processo. Fare quindi clic su **Save** (Salva):

    ![Aggiungere tag ai dispositivi refrigeratore, motore e montacarichi](media/iot-suite-remote-monitoring-explore/devicesaddtag2.png)

È possibile usare i valori dei tag per creare filtri.

1. Nella pagina **Devices** (Dispositivi) scegliere **Manage filters** (Gestisci filtri):

    ![Gestire i filtri dei dispositivi](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Creare un nuovo filtro che usi il nome di tag **FieldService** e il valore **SmartBuilding**. Salvare il filtro come **Smart Building**.

1. Creare un nuovo filtro che usi il nome di tag **FieldService** e il valore **ConnectedVehicle**. Salvare il filtro come **Connected Vehicle**.

A questo punto, l'operatore Contoso può eseguire query sui dispositivi in base al team operativo senza dover modificare alcun elemento nei dispositivi.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

>[!div class="checklist"]
> * Visualizzare e filtrare dispositivi nel dashboard
> * Rispondere a un allarme
> * Aggiornare il firmware nei dispositivi
> * Organizzare gli asset

Ora che è stata esplorata la soluzione di monitoraggio remoto, i passaggi successivi consigliati consistono nell'acquisire familiarità con le funzionalità avanzate della soluzione di monitoraggio remoto:

* [Monitorare i dispositivi](./iot-suite-remote-monitoring-monitor.md).
* [Gestire i dispositivi](./iot-suite-remote-monitoring-manage.md).
* [Automatizzare la soluzione con regole](./iot-suite-remote-monitoring-automate.md).
* [Eseguire la manutenzione della soluzione](./iot-suite-remote-monitoring-maintain.md).
