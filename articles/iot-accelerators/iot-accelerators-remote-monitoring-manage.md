---
title: 'Esercitazione: Gestire i dispositivi in una soluzione di monitoraggio remoto basata su Azure | Microsoft Docs'
description: Questa esercitazione mostra come gestire i dispositivi connessi all'acceleratore della soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: b1e12ad837def690e55fc501c67d8b2ed5e3cd4d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284612"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Esercitazione: configurare e gestire i dispositivi connessi alla soluzione di monitoraggio

In questa esercitazione si usa l'acceleratore della soluzione di monitoraggio remoto per configurare e gestire i dispositivi IoT connessi. Aggiungere un nuovo dispositivo all'acceleratore della soluzione, configurare il dispositivo e aggiornare il firmware del dispositivo.

Contoso ha ordinato nuovi macchinari per espandere una delle sue strutture. Mentre si attende la consegna dei nuovi macchinari, si vuole eseguire una simulazione per verificare il comportamento della soluzione. Per eseguire la simulazione, si aggiunge un nuovo dispositivo motore simulato all'acceleratore della soluzione di monitoraggio remoto e si verifica che questo dispositivo simulato risponda correttamente alle azioni e agli aggiornamenti della configurazione.

Per offrire una modalità estendibile di configurazione e gestione dei dispositivi, l'acceleratore della soluzione di monitoraggio remoto usa le funzionalità dell'hub IoT come i [processi](../iot-hub/iot-hub-devguide-jobs.md) e i [metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md). Mentre questa esercitazione usa dispositivi simulati, uno sviluppatore di dispositivi può implementare metodi diretti su un [dispositivo fisico collegato all'acceleratore della soluzione di monitoraggio remoto](iot-accelerators-connecting-devices.md).

In questa esercitazione:

>[!div class="checklist"]
> * Effettuare il provisioning di un dispositivo simulato.
> * Sottoporre a test un dispositivo simulato.
> * Aggiornare il firmware di un dispositivo.
> * Riconfigurare un dispositivo.
> * Organizzare i propri dispositivi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Aggiungere un dispositivo simulato

Passare alla pagina **Dispositivi** nella soluzione e fare clic su **+ Nuovo dispositivo**:

[![Effettuare il provisioning di un dispositivo simulato](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Nel pannello **Nuovo dispositivo**, scegliere **Simulato**, lasciare **1** come numero di dispositivi su cui eseguire il provisioning, scegliere il modello di dispositivo **Motore difettoso**, quindi scegliere **Applica** per creare il dispositivo simulato:

[![Effettuare il provisioning di un dispositivo motore simulato](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testare il dispositivo simulato

Per verificare che il dispositivo motore simulato stia inviando telemetria e riportando i valori delle proprietà, selezionarlo nell'elenco dei dispositivi nella pagina **Dispositivi**. Le informazioni in tempo reale sul motore vengono visualizzate nel pannello **Dettagli dispositivo**:

[![Visualizzare il nuovo dispositivo motore simulato](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

In **Dettagli dispositivo** verificare che il nuovo dispositivo invii dati di telemetria. Per visualizzare il flusso di telemetria delle vibrazioni dal dispositivo, fare clic su **Vibrazione**:

[![Selezionare un flusso di telemetria da visualizzare](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Il pannello **Dettagli dispositivo** mostra altre informazioni sul dispositivo, ad esempio valori dei tag, metodi supportati e proprietà segnalate dal dispositivo.

Per visualizzare i dati di diagnostica dettagliati, scorrere verso il basso nel pannello **Dettagli dispositivo** per visualizzare la sezione **Diagnostica**.

## <a name="act-on-a-device"></a>Eseguire operazioni su un dispositivo

Per verificare che il dispositivo motore simulato risponda correttamente alle azioni avviate dal dashboard, eseguire il metodo **FirmwareUpdate**. Per agire su un dispositivo eseguendo un metodo, selezionare il dispositivo nell'elenco dei dispositivi, quindi fare clic su **Processi**. È possibile selezionare più di un dispositivo se si desidera agire su più dispositivi. Nel pannello **Processi** (job), selezionare **Run method** (Esegui metodo). Il modello di dispositivo **Motore** specifica tre metodi: **FirmwareUpdate**, **FillTank** ed **EmptyTank**:

[![Metodi per il motore](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Scegliere **FirmwareUpdate**, impostare il nome del processo su **UpdateEngineFirmware**, impostare la versione del firmware su **2.0.0**, impostare l'URI del firmware su **http://contoso.com/engine.bin**, quindi fare clic su **Applica**:

[![Pianificare il metodo di aggiornamento del firmware](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Per tenere traccia dello stato del processo, fare clic su **View job status** (Visualizza stato del processo):

[![Monitorare il processo di aggiornamento del firmware programmato](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Dopo il completamento del processo, passare alla pagina **Dispositivi**. Viene visualizzata la nuova versione del firmware per il dispositivo motore.

Se si selezionano più dispositivi di tipi diversi nella pagina **Dispositivi**, è comunque possibile creare un processo per eseguire un metodo su tali dispositivi multipli. Il pannello **Processi** mostra solo i metodi comuni a tutti i dispositivi selezionati.

## <a name="reconfigure-a-device"></a>Riconfigurare un dispositivo

Per verificare che sia possibile aggiornare le proprietà di configurazione del motore, selezionarlo nell'elenco dispositivi nella pagina **Dispositivi**. Quindi fare clic su **Processi** e scegliere **Riconfigura**. Il pannello dei processi mostra i valori delle proprietà per il dispositivo selezionato che è possibile aggiornare:

[![Riconfigurare un dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Per aggiornare il percorso del motore, impostare il nome del processo su **UpdateEngineLocation**, impostare la longitudine **-122.15**, impostare il percorso su **Factory 2**, impostare la latitudine su **47.62** e fare clic su **Applica**:

[![Aggiornare un valore di proprietà del dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Per tenere traccia dello stato del processo, fare clic su **View job status** (Visualizza stato del processo):

[![Aggiornare un valore di proprietà del dispositivo](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Dopo il completamento del processo, passare alla pagina **Dashboard**. Il dispositivo motore viene visualizzato sulla mappa nella nuova posizione:

[![Visualizzare il percorso del motore](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizzare i propri dispositivi

Per semplificare l'organizzazione e la gestione dei dispositivi per un operatore, è possibile contrassegnarli con il nome del team. In Contoso sono presenti due team diversi per le attività di manutenzione sul campo:

* Il team Smart Vehicle gestisce i veicoli e i dispositivi di prototipazione.
* Il team Smart Building gestisce i refrigeratori, i montacarichi e i motori.

Per visualizzare tutti i dispositivi, passare alla pagina **Dispositivi** e scegliere il filtro **All devices** (Tutti i dispositivi):

[![Visualizzare tutti i dispositivi](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Aggiungi tag

Selezionare tutti i dispositivi **Trucks** (Veicoli) e **Prototyping devices** (Dispositivi di prototipazione). Quindi fare clic su **Processi**.

Nella pannello **Processi** selezionare **Tag**, impostare il nome del processo su **AddConnectedVehicleTag**, quindi aggiungere un tag di testo denominato **FieldService** con il valore **ConnectedVehicle**. Fare quindi clic su **Applica**:

[![Aggiungere tag ai dispositivi veicolo e di prototipazione](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Nella pagina dei dispositivi, selezionare tutti i dispositivi **Refrigerante**, **Ascensore** e **Motore**. Quindi fare clic su **Processi**.

Nella pannello **Processi** selezionare **Tag**, impostare il nome del processo su **AddSmartBuildingTag**, quindi aggiungere un tag di testo denominato **FieldService** con il valore **SmartBuilding**. Fare quindi clic su **Applica**:

[![Aggiungere tag ai dispositivi refrigeratore, ascensore e motore](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Creare filtri

È possibile usare i valori dei tag per creare filtri. Nella pagina **Dispositivi** fare clic su **Manage device groups** (Gestisci gruppi di dispositivi):

[![Gestire i gruppi di dispositivi](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Creare un filtro di testo che usi il nome di tag **FieldService** e il valore **SmartBuilding** nella condizione. Salvare il filtro come **Smart Building**:

[![Creare un filtro smart building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Creare un filtro di testo che usi il nome di tag **FieldService** e il valore **ConnectedVehicle** nella condizione. Salvare il filtro come **Connected Vehicle**.

[![Creare il filtro di veicoli connessi](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Ora l'operatore Contoso può richiedere i dispositivi in base al team operativo:

[![Creare il filtro di veicoli connessi](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come configurare e gestire i dispositivi connessi all'acceleratore della soluzione di monitoraggio remoto. Per informazioni su come usare l'acceleratore della soluzione per individuare e correggere i problemi con i dispositivi collegati, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Usare gli avvisi dei dispositivi per individuare e correggere i problemi con i dispositivi connessi alla soluzione di monitoraggio](iot-accelerators-remote-monitoring-maintain.md)
