---
title: Introduzione alla soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione usa scenari simulati per introdurre l'acceleratore di soluzioni di monitoraggio remoto. Questi scenari vengono creati quando si distribuisce l'acceleratore di soluzioni di monitoraggio remoto per la prima volta.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c8a055dd0dc8e2d9a9a64b492fb134caaacda489
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-solution-accelerator"></a>Esplorare le funzionalità dell'acceleratore di soluzioni di monitoraggio remoto

Questa esercitazione mostra le principali funzionalità della soluzione di monitoraggio remoto. Per presentare queste funzionalità, l'esercitazione mostra alcuni scenari di clienti comuni usando un'applicazione IoT simulata per una società denominata Contoso.

L'esercitazione aiuta a identificare i tipici scenario IoT predefiniti forniti dalla soluzione di monitoraggio remoto.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Visualizzare e filtrare dispositivi nel dashboard
> * Rispondere a un avviso
> * Aggiornare il firmware nei dispositivi
> * Organizzare gli asset
> * Arrestare e avviare dispositivi simulati

Il video seguente illustra in dettaglio la soluzione di monitoraggio remoto:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, completare l'esercitazione [Distribuire l'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="the-contoso-sample-iot-deployment"></a>Distribuzione IoT di esempio Contoso

È possibile usare la distribuzione IoT di esempio Contoso per comprendere gli scenari di base predefiniti forniti dalla soluzione di monitoraggio remoto. Questi scenari sono basati su distribuzioni IoT reali. Molto presumibilmente, si sceglierà di personalizzare la soluzione di monitoraggio remoto in base a requisiti specifici, ma l'esempio Contoso consente di apprendere le nozioni di base.

> [!NOTE]
> Se l'acceleratore di soluzioni è stato distribuito tramite l'interfaccia della riga di comando, il file `deployment-{your deployment name}-output.json` contiene informazioni sulla distribuzione, tra cui l'URL per accedere all'esempio distribuito.

L'esempio Contoso effettua il provisioning di un set di dispositivi simulati e le regole per gestirli. Una volta compresi gli scenari di base, è possibile continuare a esplorare altre funzionalità della soluzione in [Eseguire il monitoraggio avanzato dei dispositivi tramite la soluzione di monitoraggio remoto](iot-accelerators-remote-monitoring-monitor.md).

Contoso è una società che gestisce un'ampia gamma di asset in ambienti diversi. Contoso prevede di sfruttare tutta la potenza delle applicazioni IoT basate sul cloud per monitorare e gestire in remoto più asset da un'applicazione centralizzata. Le sezioni seguenti offrono un riepilogo della configurazione iniziale dell'esempio Contoso:

> [!NOTE]
> La demo Contoso è solo uno dei modi in cui effettuare il provisioning di dispositivi simulati e creare regole. Altre opzioni di provisioning includono la creazione di dispositivi personalizzati. Per altre informazioni su come creare regole e dispositivi personalizzati, vedere [Gestire e configurare i dispositivi](iot-accelerators-remote-monitoring-manage.md) e [Rilevare i problemi usando regole basate su soglie](iot-accelerators-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Dispositivi Contoso

Contoso usa diversi tipi di dispositivi avanzati. Questi dispositivi svolgono ruoli diversi per la società, inviando vari flussi di telemetria. Inoltre, ogni tipo di dispositivo ha proprietà e metodi supportati diversi.

La tabella seguente mostra un riepilogo dei tipi di dispositivo di cui viene effettuato il provisioning:

| Tipo di dispositivo        | Telemetria                                  | Properties                                  | Tag                    | Metodi                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chiller (Refrigeratore)            | Temperature (Temperatura), Humidity (Umidità), Pressure (Pressione)            | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Floor (Piano), Campus (Complesso) | Reboot (Riavvia), Firmware Update (Aggiornamento firmware), Emergency Valve Release (Rilascio valvola di emergenza), Increase Pressure (Aumenta pressione)                          |
| Prototyping device (Dispositivo di prototipazione) | Temperature (Temperatura), Pressure (Pressione), Geo-location (Posizione geografica)        | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Mode (Modalità)          | Reboot (Riavvio), Firmware Update (Aggiornamento firmware), Move device (Sposta dispositivo), Stop device (Arresta dispositivo), Temperature release (Riduzione temperatura), Temperature increase (Aumento temperatura) |
| Engine (Motore)             | Tank fuel level (Livello carburante serbatoio), Coolant sensor (Sensore liquido di raffreddamento), Vibration (Vibrazione) | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Floor (Piano), Campus (Complesso) | Firmware Update (Aggiornamento firmware), Empty tank (Svuota serbatoio), Fill tank (Riempi serbatoio)                                              |
| Truck (Veicolo)              | Geo-location (Posizione geografica), Speed (Velocità), Cargo temperature (Temperatura carico)     | Type (Tipo), Firmware version (Versione firmware), Model (Modello)               | Location (Località), Load (Carico)          | Lower cargo temperature (Riduci temperatura carico), Increase cargo temperature (Aumenta temperatura carico), Firmware update (Aggiornamento firmware)                         |
| Elevator (Montacarichi)           | Floor (Piano), Vibration (Vibrazione), Temperature (Temperatura)              | Type (Tipo), Firmware version (Versione firmware), Model (Modello), Geo-location (Posizione geografica) | Location (Località), Campus (Complesso)        | Stop elevator (Arresta montacarichi), Start elevator (Avvia montacarichi), Firmware update (Aggiornamento firmware)                                               |

> [!NOTE]
> L'esempio di demo Contoso effettua il provisioning di due dispositivi per tipo. Per ogni tipo, uno funziona correttamente entro i limiti definiti normali da Contoso, mentre l'altro ha problemi di funzionamento. Nella sezione seguente vengono descritte le regole definite da Contoso per i dispositivi. Queste regole definiscono i limiti del comportamento corretto.

### <a name="contoso-rules"></a>Regole Contoso

Gli operatori Contoso conoscono le soglie che determinano se un dispositivo funziona o meno correttamente. Ad esempio, un refrigeratore non funziona correttamente se la pressione segnalata è maggiore di 250 PSI. La tabella seguente mostra le regole basate su soglie definite da Contoso per ogni tipo di dispositivo:

| Nome regola | DESCRIZIONE | Soglia | Gravità | Dispositivi interessati |
| --------- | ----------- | --------- | -------- | ---------------- |
| Chiller pressure too high (Pressione refrigeratore troppo alta) | Genera un allarme se i refrigeratori raggiungono livelli di pressione superiori a quelli normali   |P > 250 psi       | Critico | Refrigeratori            |
| Prototyping device temp too high (Temperatura dispositivo di prototipazione troppo alta)  | Genera un allarme se i dispositivi di prototipazione raggiungono livelli di temperatura superiori a quelli normali  |T > 80 &deg;F |Critico | Dispositivi di prototipazione |
| Engine tank empty (Serbatoio motore vuoto)  | Genera un allarme se il serbatoio di carburante si svuota                     | C < 5 galloni | Info     | Motori             |
| Higher than normal cargo temperature (Temperatura carico maggiore del normale) | Genera un allarme se la temperatura del carico del veicolo è maggiore di quella normale                 | T < 45 &deg;F |Avviso  | Veicoli              |
| Elevator vibration stopped (Vibrazione montacarichi arrestata)      | Genera un allarme se il montacarichi si arresta completamente (in base al livello di vibrazione)                     | V < 0,1 mm |Avviso  | Montacarichi           |

### <a name="operate-the-contoso-sample-deployment"></a>Eseguire la distribuzione di esempio Contoso

È stata mostrata la configurazione iniziale nell'esempio Contoso. Le sezioni seguenti descrivono tre scenari nell'esempio Contoso che mostrano il modo in cui un operatore può usare l'acceleratore di soluzioni.

## <a name="respond-to-a-pressure-alert"></a>Rispondere a un avviso di pressione

Questo scenario mostra come identificare e rispondere a un avviso attivato da un dispositivo refrigeratore. Il refrigeratore si trova a Redmond, nell'edificio 43 al piano 2.

L'operatore può notare nel dashboard la presenza di un avviso relativo alla pressione di un refrigeratore. È possibile eseguire la panoramica o lo zoom sulla mappa per visualizzare altri dettagli.

1. Nella pagina **Dashboard** nella griglia **Alerts** (Avvisi) è possibile osservare l'avviso **Chiller pressure too high** (Pressione refrigeratore troppo alta). Il refrigeratore è anche visualizzato sulla mappa:

    ![Dashboard che mostra l'avviso di pressione e il dispositivo sulla mappa](./media/iot-accelerators-remote-monitoring-explore/dashboardalarm.png)

1. Per passare alla pagina **Maintenance** (Manutenzione), scegliere **Maintenance** (Manutenzione) dal menu di spostamento. Nella pagina **Maintenance** (Manutenzione) è possibile visualizzare i dettagli della regola che ha attivato l'avviso di pressione del refrigeratore.

1. L'elenco degli avvisi mostra il numero di volte in cui l'avviso è stato attivato, le conferme e gli avvisi aperti e chiusi:

    ![Pagina Maintenance (Manutenzione) che mostra l'elenco degli avvisi attivati](./media/iot-accelerators-remote-monitoring-explore/maintenancealarmlist.png)

1. L'ultimo allarme nell'elenco è quello più recente. Fare clic sull'avviso **Chiller Pressure Too High** (Pressione refrigeratore troppo alta) per visualizzare i dati di telemetria e i dispositivi associati. I dati di telemetria indicano picchi di pressione per il refrigeratore:

    ![Pagina Maintenance (Manutenzione) che mostra i dati di telemetria per un avviso selezionato](./media/iot-accelerators-remote-monitoring-explore/maintenancetelemetry.png)

Sono stati ora identificati il problema che ha attivato l'avviso e il dispositivo associato. Per un operatore, i passaggi successivi consistono nel confermare l'avviso e correggere il problema.

1. Per indicare che si sta intervenendo sull'avviso, modificare il valore di **Alert status** (Stato avviso) in **Acknowledged** (Confermato):

    ![Selezionare e confermare l'avviso](./media/iot-accelerators-remote-monitoring-explore/maintenanceacknowledge.png)

1. Per intervenire sul refrigeratore, selezionarlo e quindi scegliere **Jobs** (Processi). Selezionare **Run method** (Esegui metodo), quindi **EmergencyValveRelease**, aggiungere il nome di processo **ChillerPressureRelease** e quindi scegliere **Apply** (Applica). Queste impostazioni creano un processo che viene eseguito immediatamente:

    ![Selezionare il dispositivo e pianificare un'azione](./media/iot-accelerators-remote-monitoring-explore/maintenanceschedule.png)

1. Per visualizzare lo stato del processo, tornare alla pagina **Maintenance** (Manutenzione) e visualizzare l'elenco di processi nella visualizzazione **Jobs** (Processi). È possibile notare che il processo è stato eseguito per rilasciare la pressione della valvola sul refrigeratore:

    ![Stato dei processi nella visualizzazione Jobs (Processi)](./media/iot-accelerators-remote-monitoring-explore/maintenancerunningjob.png)

Infine, verificare che i valori di telemetria per il refrigeratore siano tornati normali.

1. Per visualizzare la griglia degli avvisi, passare alla pagina **Dashboard**.

1. Per visualizzare i dati di telemetria del dispositivo, selezionare il dispositivo per l'avviso originale sulla mappa e verificare che sia tornato al valore normale.

1. Per chiudere l'evento imprevisto, passare alla pagina **Maintenance** (Manutenzione), selezionare l'avviso e impostare lo stato su **Closed** (Chiuso):

    ![Selezionare e chiudere l'avviso](./media/iot-accelerators-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Aggiornare il firmware del dispositivo

Contoso sta testando un nuovo tipo di dispositivo sul campo. Nell'ambito del ciclo di test è necessario verificare che gli aggiornamenti del firmware del dispositivo funzionino correttamente. I passaggi seguenti mostrano come usare la soluzione di monitoraggio remoto per aggiornare il firmware su più dispositivi.

Per eseguire le attività di gestione dei dispositivi necessarie, usare la pagina **Devices** (Dispositivi). Per iniziare, filtrare tutti i dispositivi di prototipazione:

1. Passare al pannello **Devices** (Dispositivi). Scegliere il filtro **Prototyping devices** (Dispositivi di prototipazione) nell'elenco a discesa **Filters** (Filtri):

    ![Usare un filtro nella pagina dei dispositivi](./media/iot-accelerators-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Fare clic su **Manage** (Gestisci) per gestire i filtri disponibili.

1. Selezionare uno dei dispositivi di prototipazione:

    ![Selezionare un dispositivo nella pagina dei dispositivi](./media/iot-accelerators-remote-monitoring-explore/devicesselect.png)

1. Fare clic sul pulsante **Jobs** (Processi), selezionare **Run method** (Esegui metodo), quindi scegliere **Firmware update** (aggiornamento Firmware). Immettere i valori per **Job name** (Nome processo), **Firmware Version** (Versione firmware) e **Firmware URI** (URI firmware). Scegliere **Apply** (Applica) per pianificare l'esecuzione del processo a questo punto:

    ![Pianificare l'aggiornamento del firmware nel dispositivo](./media/iot-accelerators-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Con i dispositivi simulati è possibile usare qualsiasi URL come valore di **Firmware URI** (URI firmware) e qualsiasi valore per **Firmware Version** (Versione firmware). I dispositivi simulati non accedono all'URL.

1. Notare quanti dispositivi sono interessati dal processo e scegliere **Apply** (Applica):

    ![Inviare il processo di aggiornamento del firmware](./media/iot-accelerators-remote-monitoring-explore/devicessubmitupdate.png)

È possibile usare la pagina **Maintenance** (Manutenzione) per monitorare il processo durante la sua esecuzione.

1. Per visualizzare l'elenco dei processi, passare alla pagina **Maintenance** (Manutenzione) e fare clic su **Jobs** (Processi).

1. Individuare l'evento relativo al processo creato. Verificare che il processo di aggiornamento del firmware sia stato avviato correttamente.

<!-- 05/01 broken 
You can create a filter to verify the firmware version updated correctly.

1. To create a filter, navigate to the **Devices** page and select **Manage device groups**:

    ![Manage device groups](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Create a filter that includes only devices with the new firmware version:

    ![Create device filter](./media/iot-accelerators-remote-monitoring-explore/devicescreatefilter.png)

1. Return to the **Devices** page and verify that the device has the new firmware version. -->

## <a name="organize-your-assets"></a>Organizzare gli asset

In Contoso sono presenti due team diversi per le attività di manutenzione sul campo:

* Il team Smart Building gestisce i refrigeratori, i montacarichi e i motori.
* Il team Smart Vehicle gestisce i veicoli e i dispositivi di prototipazione.

Per semplificare l'organizzazione e la gestione dei dispositivi per un operatore, è possibile contrassegnarli con il nome del team appropriato.

È possibile creare nomi di tag da usare con i dispositivi.

1. Per visualizzare tutti i dispositivi, passare alla pagina **Devices** (Dispositivi) e scegliere il filtro **All devices** (Tutti i dispositivi):

    ![Visualizzazione di tutti i dispositivi](./media/iot-accelerators-remote-monitoring-explore/devicesalldevices.png)

1. Selezionare i dispositivi **Trucks** (Veicoli) e **Prototyping devices** (Dispositivi di prototipazione). Scegliere quindi **Jobs** (Processi):

    ![Selezionare i dispositivi veicolo e di prototipazione](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect.png)

1. Scegliere **Tag** e quindi creare un nuovo tag di testo denominato **FieldService**con valore **ConnectedVehicle**. Scegliere un nome per il processo. Fare quindi clic su **Apply**(Applica):

    ![Aggiungere tag ai dispositivi veicolo e di prototipazione](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag.png)

1. Selezionare i dispositivi **Chiller** (Refrigeratore), **Elevator** (Montacarichi) e **Engine** (Motore). Scegliere quindi **Jobs** (Processi):

    ![Selezionare i dispositivi refrigeratore, montacarichi e motore](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect2.png)

1. Scegliere **Tag** e quindi creare un nuovo tag di testo denominato **FieldService**con valore **SmartBuilding**. Scegliere un nome per il processo. Fare quindi clic su **Apply**(Applica):

    ![Aggiungere tag ai dispositivi refrigeratore, motore e montacarichi](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag2.png)

È possibile usare i valori dei tag per creare filtri.

1. Nella pagina **Devices** (Dispositivi) scegliere **Manage device groups** (Gestisci gruppi di dispositivi):

    ![Gestire i gruppi di dispositivi](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Creare un nuovo filtro che usi il nome di tag **FieldService** e il valore **SmartBuilding**. Salvare il filtro come **Smart Building**.

1. Creare un nuovo filtro che usi il nome di tag **FieldService** e il valore **ConnectedVehicle**. Salvare il filtro come **Connected Vehicle**.

A questo punto, l'operatore Contoso può eseguire query sui dispositivi in base al team operativo senza dover modificare alcun elemento nei dispositivi.

## <a name="stop-simulated-devices"></a>Arrestare i dispositivi simulati

Per arrestare i dispositivi simulati, si può usare il menu delle impostazioni. Ciò consente di ridurre i costi di test ed esplorazione della soluzione. Per avviare o arrestare i dispositivi simulati:

1. Scegliere l'icona **Impostazioni**.

1. Quindi attivare o disattivare **Flowing** (Flusso):

    ![Menu Impostazioni](./media/iot-accelerators-remote-monitoring-explore/settings.png)

## <a name="customize-the-ui"></a>Personalizzare l'interfaccia utente

Dal menu delle impostazioni è possibile applicare personalizzazioni di base all'acceleratore di soluzioni di monitoraggio remoto. È possibile:

- Passare tra vari temi chiaro e scuro.
- Modificare il nome della soluzione.
- Caricare un logo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

>[!div class="checklist"]
> * Visualizzare e filtrare dispositivi nel dashboard
> * Rispondere a un avviso
> * Aggiornare il firmware nei dispositivi
> * Organizzare gli asset
> * Arrestare e avviare dispositivi simulati

Ora che è stata esplorata la soluzione di monitoraggio remoto, i passaggi successivi consigliati consistono nell'acquisire familiarità con le funzionalità avanzate della soluzione di monitoraggio remoto:

* [Monitorare i dispositivi](./iot-accelerators-remote-monitoring-monitor.md).
* [Gestire i dispositivi](./iot-accelerators-remote-monitoring-manage.md).
* [Automatizzare la soluzione con regole](./iot-accelerators-remote-monitoring-automate.md).
* [Eseguire la manutenzione della soluzione](iot-accelerators-remote-monitoring-maintain.md).
