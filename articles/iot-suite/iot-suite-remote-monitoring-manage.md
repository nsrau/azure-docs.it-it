---
title: Gestione dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come gestire i dispositivi connessi alla soluzione di monitoraggio remoto.
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
ms.openlocfilehash: 84c2eaaab2dfc09c93fbfeac3fe2bfcc7066a411
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="manage-and-configure-your-devices"></a>Gestire e configurare i dispositivi

Questa esercitazione mostra le funzionalità di gestione dei dispositivi della soluzione di monitoraggio remoto. Per presentare queste funzionalità, l'esercitazione usa uno scenario nell'applicazione IoT Contoso.

Contoso ha ordinato nuovi macchinari per espandere una delle strutture e aumentare la produzione. Mentre si attende la consegna dei nuovi macchinari, si vuole eseguire una simulazione per verificare il comportamento della soluzione. Un operatore vuole gestire e configurare i dispositivi nella soluzione di monitoraggio remoto.

Per fornire una modalità estendibile di gestione e configurazione dei dispositivi, la soluzione di monitoraggio remoto usa funzionalità dell'hub IoT come i [processi](../iot-hub/iot-hub-devguide-jobs.md) e i [metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md). Per informazioni su come uno sviluppatore di dispositivi implementa i metodi in un dispositivo fisico, vedere [Personalizzare la soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-customize.md).

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Effettuare il provisioning di un dispositivo simulato.
> * Testare il dispositivo simulato.
> * Chiamare i metodi dei dispositivi dalla soluzione.
> * Riconfigurare un dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, è necessario completare l'esercitazione [Distribuire la soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="provision-a-simulated-device"></a>Effettuare il provisioning di un dispositivo simulato

Passare alla pagina **Devices** (Dispositivi) nella soluzione e quindi scegliere **Provision** (Provisioning). Nel pannello **Provision** (Provisioning) scegliere **Simulated** (Simulato):

![Effettuare il provisioning di un dispositivo simulato](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

Lasciare il numero di dispositivi di cui effettuare il provisioning impostato su **1**. Scegliere **Engine** (Motore) per **Device model** (Modello del dispositivo) e quindi scegliere **Apply** (Applica) per creare il dispositivo simulato:

![Effettuare il provisioning di un dispositivo motore simulato](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

Per informazioni su come effettuare il provisioning di un dispositivo *fisico*, vedere [Connettere il dispositivo alla soluzione preconfigurata di monitoraggio remoto](iot-suite-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Testare il dispositivo simulato

Per visualizzare i dettagli del nuovo dispositivo simulato, selezionarlo nell'elenco nella pagina **Devices** (Dispositivi). Le informazioni sul dispositivo vengono visualizzate nel pannello **Device detail** (Dettagli dispositivo):

![Visualizzare il nuovo dispositivo motore simulato](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

In **Device detail** (Dettagli dispositivo) verificare che il nuovo dispositivo invii dati di telemetria. Per visualizzare i diversi flussi di telemetria inviati dal dispositivo, scegliere un tipo di telemetria, ad esempio **Vibration** (Vibrazione):

![Selezionare un flusso di telemetria da visualizzare](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

Il pannello **Device detail** (Dettagli dispositivo) mostra altre informazioni sul dispositivo, ad esempio valori dei tag, metodi supportati e proprietà segnalate dal dispositivo.

Per visualizzare i dati di diagnostica dettagliati, scorrere fino a **Diagnostics** (Diagnostica).

## <a name="act-on-a-device"></a>Eseguire operazioni su un dispositivo

Per eseguire operazioni su un dispositivo, selezionarlo nell'elenco dei dispositivi e quindi scegliere **Schedule** (Pianifica). Il modello di dispositivo **Engine** (Motore) specifica quattro metodi che un dispositivo deve supportare:

![Metodi per il motore](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

Scegliere **Restart** (Riavvia), impostare il nome del processo su **RestartEngine** e quindi scegliere **Apply** (Applica):

![Pianificare il metodo di riavvio](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

Per tenere traccia dello stato del processo nella pagina **Maintenance** (Manutenzione), scegliere **System Status** (Stato sistema):

![Monitorare il processo pianificato](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Metodi in altri dispositivi

Quando si esplorano i diversi tipi di dispositivi simulati, è possibile vedere che altri tipi di dispositivi supportano metodi diversi. In una distribuzione con dispositivi fisici, il modello di dispositivo specifica i metodi che il dispositivo deve supportare. In genere, lo sviluppatore di dispositivi è responsabile per lo sviluppo del codice che determina il comportamento del dispositivo in risposta a una chiamata a un metodo.

Per pianificare un metodo per l'esecuzione in più dispositivi, è possibile selezionare più dispositivi nell'elenco nella pagina **Devices** (Dispositivi). Il pannello **Schedule** (Pianifica) mostra i tipi di metodi comuni a tutti i dispositivi selezionati.

## <a name="reconfigure-a-device"></a>Riconfigurare un dispositivo

Per modificare la configurazione di un dispositivo, selezionarlo nell'elenco dei dispositivi nella pagina **Devices** (Dispositivi) e quindi scegliere **Reconfigure** (Riconfigura). Il pannello per la riconfigurazione mostra i valori delle proprietà per il dispositivo selezionato che è possibile modificare:

![Riconfigurare un dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

Per apportare una modifica, aggiungere un nome per il processo, aggiornare i valori delle proprietà e scegliere **Apply** (Applica):

![Aggiornare un valore di proprietà del dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

Per tenere traccia dello stato del processo nella pagina **Maintenance** (Manutenzione), scegliere **System Status** (Stato sistema).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Effettuare il provisioning di un dispositivo simulato.
> * Testare il dispositivo simulato.
> * Chiamare i metodi dei dispositivi dalla soluzione.
> * Riconfigurare un dispositivo.

Ora che si è appreso come gestire i dispositivi, i passaggi successivi consigliati consentono di apprendere come:

* [Risolvere e correggere i problemi dei dispositivi](iot-suite-remote-monitoring-maintain.md).
* [Testare la soluzione con dispositivi simulati](iot-suite-remote-monitoring-test.md).
* [Connettere il dispositivo alla soluzione preconfigurata di monitoraggio remoto](iot-suite-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->