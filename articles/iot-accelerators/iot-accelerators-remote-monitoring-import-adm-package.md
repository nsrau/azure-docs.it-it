---
title: Soluzione di monitoraggio remoto Importa pacchetto - Azure | Microsoft Docs
description: Questo articolo descrive come importare un pacchetto di gestione automatica dei dispositivi nell'acceleratore della soluzione di monitoraggio remoto
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443335"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importare un pacchetto di gestione automatica dei dispositivi nell'acceleratore della soluzione di monitoraggio remoto

Una configurazione di gestione automatica dei dispositivi definisce le modifiche di configurazione per la distribuzione in un gruppo di dispositivi. Questo articolo presuppone che uno sviluppatore dell'organizzazione abbia già creato una configurazione di gestione automatica dei dispositivi. Per informazioni sul modo in cui uno sviluppatore crea una configurazione, vedere una delle seguenti procedure dettagliate nell'hub IoT:

- [Configurare e monitorare i dispositivi IoT su larga scala tramite il portale di Azure](../iot-hub/iot-hub-auto-device-config.md)
- [Configurare e monitorare i dispositivi IoT su larga scala tramite l'interfaccia della riga di comando di Azure](../iot-hub/iot-hub-auto-device-config-cli.md)

Uno sviluppatore crea e testa una configurazione di gestione automatica dei dispositivi in un ambiente di sviluppo. Al termine, è possibile importare la configurazione nell'acceleratore della soluzione di monitoraggio remoto.

## <a name="export-a-configuration"></a>Esportare una configurazione

Usare il portale di Azure per esportare la configurazione di gestione automatica dei dispositivi dall'ambiente di sviluppo:

1. Nel portale di Azure passare all'hub IoT che si sta usando per sviluppare e testare i dispositivi IoT. Fare clic su **Configurazione del dispositivo IoT**:

    [![Configurazione del dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Fare clic sulla configurazione da usare. Viene visualizzata la pagina dei **dettagli di configurazione del dispositivo**:

    [![Dettagli della configurazione del dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Fare clic su **Scarica file di configurazione**:

    [![Scarica file di configurazione](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Salvare il file JSON come un file locale denominato **configuration.json**.

A questo punto è disponibile un file che contiene la configurazione di gestione automatica dei dispositivi. Nella sezione successiva si importerà la configurazione come pacchetto nella soluzione di monitoraggio remoto.

## <a name="import-a-package"></a>Importare un pacchetto

Attenersi alla procedura seguente per importare una configurazione di gestione automatica dei dispositivi come pacchetto in una soluzione:

1. Nell'interfaccia utente Web di monitoraggio remoto passare alla pagina **Pacchetti**:  ![Pagina Pacchetti](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Fare clic su **+ Nuovo pacchetto**, scegliere **Configurazione** come tipo di pacchetto e fare clic su **Sfoglia** per selezionare il file **configuration.json** salvato nella sezione precedente:

    ![Selezionare la configurazione](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Fare clic su **Carica** per aggiungere il pacchetto alla soluzione di monitoraggio remoto:

    ![Pacchetto caricato](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

È stata caricata una configurazione di gestione automatica dei dispositivi come pacchetto. Nella pagina **Distribuzioni** è possibile distribuire questo pacchetto ai dispositivi connessi.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare un pacchetto di configurazione e importarlo dalla soluzione di monitoraggio remoto, il passaggio successivo è apprendere come [gestire i dispositivi connessi al monitoraggio remoto in blocco](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
