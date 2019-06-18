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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147724"
---
## <a name="view-device-telemetry"></a>Visualizzare la telemetria dei dispositivi

È possibile visualizzare i dati di telemetria inviati dal dispositivo **Device Explorer** pagina nella soluzione.

1. Selezionare il dispositivo è stato effettuato il provisioning nell'elenco dei dispositivi nel **Device Explorer** pagina. Un pannello visualizza le informazioni sul dispositivo, incluso un tracciato dei dati di telemetria del dispositivo:

    ![Vedere i dettagli del dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Scegliere **Pressione** per modificare la visualizzazione dei dati di telemetria:

    ![Visualizzare i dati di telemetria per la pressione](media/iot-suite-visualize-connecting/devicespressure.png)

1. Per visualizzare le informazioni di diagnostica sul dispositivo, scorrere fino a **Diagnostica**:

    ![Visualizzazione della diagnostica del dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agire sul dispositivo

Per richiamare metodi nei dispositivi, usare il **Device Explorer** pagina nella soluzione di monitoraggio remoto. Ad esempio, nella soluzione monitoraggio remoto **Chiller** dispositivi di implementano un **riavviare** (metodo).

1. Scegliere **dispositivi** a cui passare le **Device Explorer** pagina nella soluzione.

1. Selezionare il dispositivo è stato effettuato il provisioning nell'elenco dei dispositivi nel **Device Explorer** pagina:

    ![Selezionare il dispositivo reale](media/iot-suite-visualize-connecting/devicesselect.png)

1. Per visualizzare un elenco di metodi che è possibile chiamare sul dispositivo, scegliere **processi**, quindi **metodi**. Per pianificare un processo in modo che venga eseguito su più dispositivi, è possibile selezionare più dispositivi nell'elenco. Il pannello **Jobs** (Processi) visualizza i tipi di metodi comuni a tutti i dispositivi selezionati.

1. Scegliere **riavvio**, impostare il nome del processo **RebootPhysicalChiller** e quindi scegliere **Apply**:

    ![Pianificare l'aggiornamento del firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Nella console che esegue il codice del dispositivo mentre il dispositivo simulato gestisce il metodo viene visualizzata una sequenza di messaggi.

> [!NOTE]
> Per tenere traccia dello stato del processo nella soluzione, scegliere **lo stato del processo Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

L'articolo [Personalizzare l'acceleratore di soluzioni Monitoraggio remoto](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descrive alcuni modi per personalizzare l'acceleratore di soluzioni.