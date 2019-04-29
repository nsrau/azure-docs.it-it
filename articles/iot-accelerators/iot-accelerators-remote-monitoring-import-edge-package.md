---
title: Pacchetto Edge per l'importazione della soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive come importare un pacchetto IoT Edge nell'acceleratore della soluzione di monitoraggio remoto
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/10/2018
ms.date: 12/17/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442940"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importare un pacchetto IoT Edge nell'acceleratore della soluzione di monitoraggio remoto

Un manifesto di distribuzione definisce i moduli da distribuire in un dispositivo IoT Edge. Questo articolo presuppone che uno sviluppatore all'interno dell'organizzazione abbia già creato il manifesto di distribuzione. Per informazioni sul modo in cui uno sviluppatore crea un manifesto, vedere una delle seguenti procedure dettagliate su IoT Edge:

- [Distribuire i moduli di Azure IoT Edge dal portale di Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando di Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Distribuire i moduli di Azure IoT Edge da Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Uno sviluppatore crea e testa un manifesto di distribuzione in un ambiente di sviluppo. Al termine, è possibile importare il manifesto nell'acceleratore della soluzione di monitoraggio remoto.

## <a name="export-a-manifest"></a>Esportare un manifesto

Usare il portale di Azure per esportare il manifesto di distribuzione dall'ambiente di sviluppo:

1. Nel portale di Azure passare all'hub IoT che si sta usando per sviluppare e testare i dispositivi IoT Edge. Fare clic su **IoT Edge** e quindi **distribuzioni IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Fare clic sulla distribuzione contenente la configurazione di distribuzione da usare. Il **dettagli di distribuzione** pagina consente di visualizzare: ![Informazioni dettagliate sulla distribuzione di IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Fare clic su **manifesto di IoT Edge scaricare**:  ![Scaricare il manifesto di distribuzione](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Salvare il file JSON come un file locale denominato **deploymentmanifest.json**.

Si dispone ora di un file contenente il manifesto di distribuzione. Nella sezione successiva si importerà il manifesto come pacchetto nella soluzione di monitoraggio remoto.

## <a name="import-a-package"></a>Importare un pacchetto

Seguire la procedura seguente per importare un manifesto della distribuzione di Edge come pacchetto nella soluzione:

1. Nell'interfaccia utente Web di monitoraggio remoto passare alla pagina **Pacchetti**:  ![Pagina Pacchetti](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Fare clic su **+ nuovo pacchetto**, scegliere **Edge manifesto** come tipo di pacchetto, fare clic su **Sfoglia** per selezionare il **deploymentmanifest.json** file è stato salvato nella sezione precedente:  ![Seleziona manifesto](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Fare clic su **Carica** per aggiungere il pacchetto alla soluzione di monitoraggio remoto:  ![Pacchetto caricato](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

È stato ora caricato un manifesto di distribuzione di IoT Edge come pacchetto. Nella pagina **Distribuzioni** è possibile distribuire questo pacchetto ai dispositivi IoT Edge connessi.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come distribuire moduli in un dispositivo IoT Edge dalla soluzione di monitoraggio remoto, è possibile acquisire informazioni più approfondire su [IoT Edge](../iot-edge/about-iot-edge.md).
