---
title: 'Esercitazione: Configurare i dispositivi in una soluzione di monitoraggio remoto - Azure | Microsoft Docs'
description: Questa esercitazione mostra come configurare i dispositivi connessi all'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/15/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 869f6d2391632c77e01e4383c1457f88b9171c8b
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651193"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Esercitazione: Configurare i dispositivi connessi alla soluzione di monitoraggio

In questa esercitazione si usa l'acceleratore della soluzione di monitoraggio remoto per configurare e gestire i dispositivi IoT connessi. Aggiungere un nuovo dispositivo all'acceleratore di soluzione e configurare il dispositivo.

Contoso ha ordinato nuovi macchinari per espandere una delle sue strutture. Mentre si attende la consegna dei nuovi macchinari, si vuole eseguire una simulazione per verificare il comportamento della soluzione. Per eseguire la simulazione, si aggiunge un nuovo dispositivo motore simulato all'acceleratore di soluzione Monitoraggio remoto e si verifica che questo dispositivo simulato risponda correttamente agli aggiornamenti della configurazione. Mentre questa esercitazione usa dispositivi simulati, uno sviluppatore di dispositivi può implementare metodi diretti su un [dispositivo reale collegato all'acceleratore della soluzione di monitoraggio remoto](iot-accelerators-connecting-devices.md).

In questa esercitazione:

>[!div class="checklist"]
> * Effettuare il provisioning di un dispositivo simulato.
> * Sottoporre a test un dispositivo simulato.
> * Riconfigurare un dispositivo.
> * Organizzare i propri dispositivi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Aggiungere un dispositivo simulato

Passare alla pagina **Device Explorer** nella soluzione, quindi fare clic su **+ New device** (+ Nuovo dispositivo):

[![Effettuare il provisioning di un dispositivo simulato](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Nel pannello **Nuovo dispositivo**, scegliere **Simulato**, lasciare **1** come numero di dispositivi su cui eseguire il provisioning, scegliere il modello di dispositivo **Motore difettoso**, quindi scegliere **Applica** per creare il dispositivo simulato:

[![Effettuare il provisioning di un dispositivo motore simulato](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testare il dispositivo simulato

Per verificare che il dispositivo motore simulato stia inviando telemetria e riportando i valori delle proprietà, selezionarlo nell'elenco dei dispositivi nella pagina **Device Explorer**. Le informazioni in tempo reale sul motore vengono visualizzate nel pannello **Dettagli dispositivo**:

[![Visualizzare il nuovo dispositivo motore simulato](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

In **Dettagli dispositivo** verificare che il nuovo dispositivo invii dati di telemetria. Per visualizzare il flusso di telemetria delle vibrazioni dal dispositivo, fare clic su **Vibrazione**:

[![Selezionare un flusso di telemetria da visualizzare](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Il pannello **Dettagli dispositivo** mostra altre informazioni sul dispositivo, ad esempio valori dei tag, metodi supportati e proprietà segnalate dal dispositivo.

Per visualizzare i dati di diagnostica dettagliati, scorrere verso il basso nel pannello **Dettagli dispositivo** per visualizzare la sezione **Diagnostica**.

## <a name="reconfigure-a-device"></a>Riconfigurare un dispositivo

Per verificare che sia possibile aggiornare le proprietà di configurazione del motore, selezionarlo nell'elenco dispositivi nella pagina **Device Explorer**. Fare quindi clic su **Processi** e scegliere **Proprietà**. Il pannello dei processi mostra i valori delle proprietà per il dispositivo selezionato che è possibile aggiornare:

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

Per visualizzare tutti i dispositivi, passare alla pagina **Device Explorer** e scegliere il filtro **All devices** (Tutti i dispositivi):

[![Visualizzare tutti i dispositivi](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Aggiungi tag

Selezionare tutti i dispositivi **Trucks** (Veicoli) e **Prototyping devices** (Dispositivi di prototipazione). Quindi fare clic su **Processi**.

Nella pannello **Processi** selezionare **Tag**, impostare il nome del processo su **AddConnectedVehicleTag**, quindi aggiungere un tag di testo denominato **FieldService** con il valore **ConnectedVehicle**. Fare quindi clic su **Applica**:

[![Aggiungere tag ai dispositivi veicolo e di prototipazione](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Nella pagina dei dispositivi, selezionare tutti i dispositivi **Refrigerante**, **Ascensore** e **Motore**. Quindi fare clic su **Processi**.

Nella pannello **Processi** selezionare **Tag**, impostare il nome del processo su **AddSmartBuildingTag**, quindi aggiungere un tag di testo denominato **FieldService** con il valore **SmartBuilding**. Fare quindi clic su **Applica**:

[![Aggiungere tag ai dispositivi refrigeratore, ascensore e motore](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Creare filtri

È possibile usare i valori dei tag per creare filtri. Nella pagina **Device Explorer** fare clic su **Manage device groups** (Gestisci gruppi di dispositivi):

[![Gestire i gruppi di dispositivi](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Creare un filtro di testo che usi il nome di tag **FieldService** e il valore **SmartBuilding** nella condizione. Salvare il filtro come **Smart Building**:

[![Creare un filtro smart building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Creare un filtro di testo che usi il nome di tag **FieldService** e il valore **ConnectedVehicle** nella condizione. Salvare il filtro come **Connected Vehicle**.

[![Creare il filtro di veicoli connessi](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Ora l'operatore Contoso può richiedere i dispositivi in base al team operativo:

[![Creare il filtro di veicoli connessi](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come configurare e gestire i dispositivi connessi all'acceleratore della soluzione di monitoraggio remoto. Per informazioni su come usare un acceleratore di soluzioni per eseguire un'analisi della causa radice di un avviso imprevisto, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Conduct a root cause analysis on an alert](iot-accelerators-remote-monitoring-root-cause-analysis.md) (Eseguire un'analisi della causa radice di un avviso)
