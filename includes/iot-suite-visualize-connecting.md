---
title: File di inclusione
description: File di inclusione
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180581"
---
## <a name="view-device-telemetry"></a>Visualizzare la telemetria dei dispositivi

È possibile visualizzare i dati di telemetria inviati dal dispositivo nella pagina **Esplora dispositivi** della soluzione.

1. Selezionare il dispositivo di cui è stato eseguito il provisioning nell'elenco dei dispositivi nella pagina **Esplora dispositivi.** Un pannello visualizza le informazioni sul dispositivo, incluso un tracciato dei dati di telemetria del dispositivo:

    ![Vedere i dettagli del dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Scegliere **Pressione** per modificare la visualizzazione dei dati di telemetria:

    ![Visualizzare i dati di telemetria per la pressione](media/iot-suite-visualize-connecting/devicespressure.png)

1. Per visualizzare le informazioni di diagnostica sul dispositivo, scorrere fino a **Diagnostica**:

    ![Visualizzazione della diagnostica del dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agire sul dispositivo

Per richiamare metodi nei dispositivi, usare la pagina **Esplora dispositivi** nella soluzione Monitoraggio remoto. Ad esempio, nella soluzione di monitoraggio remoto I dispositivi Chiller implementano un metodo **Reboot.For** example, in the Remote Monitoring solution **Chiller** devices implement a Reboot method.

1. Scegliere **Dispositivi** per passare alla pagina **Esplora dispositivi** nella soluzione.

1. Selezionare il dispositivo di cui è stato eseguito il provisioning nell'elenco dei dispositivi nella pagina **Esplora dispositivi:**

    ![Selezionare il dispositivo reale](media/iot-suite-visualize-connecting/devicesselect.png)

1. Per visualizzare un elenco dei metodi che è possibile chiamare sul dispositivo, scegliere **Processi**, quindi **Metodi**. Per pianificare un processo in modo che venga eseguito su più dispositivi, è possibile selezionare più dispositivi nell'elenco. Il pannello **Jobs** (Processi) visualizza i tipi di metodi comuni a tutti i dispositivi selezionati.

1. Scegliere **Riavvia**, impostare il nome del processo su **RebootPhysicalChiller,** quindi scegliere **Applica**:

    ![Pianificare l'aggiornamento del firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Nella console che esegue il codice del dispositivo mentre il dispositivo simulato gestisce il metodo viene visualizzata una sequenza di messaggi.

> [!NOTE]
> Per tenere traccia dello stato del processo nella soluzione, scegliere **Visualizza stato processo**.

## <a name="next-steps"></a>Passaggi successivi

Nell'articolo [Customize the Remote Monitoring solution accelerator](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) vengono descritti alcuni modi per personalizzare l'acceleratore di soluzioni.