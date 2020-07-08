---
title: includere file
description: includere file
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180581"
---
## <a name="view-device-telemetry"></a>Visualizzare la telemetria dei dispositivi

È possibile visualizzare i dati di telemetria inviati dal dispositivo nella pagina **Esplora dispositivi** della soluzione.

1. Selezionare il dispositivo del quale è stato effettuato il provisioning nell'elenco dei dispositivi della pagina **Esplora dispositivi**. Un pannello visualizza le informazioni sul dispositivo, incluso un tracciato dei dati di telemetria del dispositivo:

    ![Vedere i dettagli del dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Scegliere **Pressione** per modificare la visualizzazione dei dati di telemetria:

    ![Visualizzare i dati di telemetria per la pressione](media/iot-suite-visualize-connecting/devicespressure.png)

1. Per visualizzare le informazioni di diagnostica sul dispositivo, scorrere fino a **Diagnostica**:

    ![Visualizzazione della diagnostica del dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agire sul dispositivo

Per richiamare i metodi nei dispositivi, usare la pagina **Esplora dispositivi** della soluzione di monitoraggio remoto. Nella soluzione di monitoraggio remoto, i dispositivi **Chiller** implementano ad esempio un metodo **Reboot**.

1. Scegliere **Dispositivi** per passare alla pagina **Esplora dispositivi** della soluzione.

1. Selezionare il dispositivo del quale è stato effettuato il provisioning nell'elenco dei dispositivi della pagina **Esplora dispositivi**:

    ![Selezionare il dispositivo reale](media/iot-suite-visualize-connecting/devicesselect.png)

1. Per visualizzare un elenco dei metodi che è possibile chiamare per un dispositivo, scegliere **Jobs** (Processi), quindi **Metodi**. Per pianificare un processo in modo che venga eseguito su più dispositivi, è possibile selezionare più dispositivi nell'elenco. Il pannello **Jobs** (Processi) visualizza i tipi di metodi comuni a tutti i dispositivi selezionati.

1. Scegliere **Reboot**, impostare il nome processo su **RebootPhysicalChiller** e quindi fare clic su **Applica**:

    ![Pianificare l'aggiornamento del firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Nella console che esegue il codice del dispositivo mentre il dispositivo simulato gestisce il metodo viene visualizzata una sequenza di messaggi.

> [!NOTE]
> Per tenere traccia dello stato del processo nella soluzione, scegliere **View Job Status** (Visualizza stato del processo).

## <a name="next-steps"></a>Passaggi successivi

L'articolo [Personalizzare l'acceleratore di soluzioni Monitoraggio remoto](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descrive alcuni modi per personalizzare l'acceleratore di soluzioni.