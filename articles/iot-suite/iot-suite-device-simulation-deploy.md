---
title: Distribuire la soluzione di simulazione del dispositivo - Azure | Documenti Microsoft
description: In questa esercitazione viene illustrato come eseguire il provisioning della soluzione di simulazione del dispositivo da azureiotsuite.com.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: da9fb95ed5d3387c98c3274a53769d3f5f945371
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Distribuire la soluzione di simulazione del dispositivo IoT Azure

In questa esercitazione viene illustrato come eseguire il provisioning di una soluzione di simulazione del dispositivo. È necessario distribuire la soluzione dall'indirizzo azureiotsuite.com.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la soluzione di simulazione del dispositivo
> * Distribuire la soluzione di simulazione del dispositivo
> * Accedi alla soluzione simulazione del dispositivo

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Prima di distribuire la soluzione per la sottoscrizione di Azure, è necessario scegliere alcune opzioni di configurazione:

1. Accedi a [azureiotsuite.com](https://www.azureiotsuite.com) di Azure utilizzando le credenziali dell'account e fare clic su  **+**  per creare una nuova soluzione:

    ![Creare una nuova soluzione](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. Fare clic su **selezionare** sul **simulazione del dispositivo** riquadro:

    ![Scegliere una simulazione del dispositivo](media/iot-suite-device-simulation-deploy/select.png)

1. Nel **soluzione simulazione crea dispositivo** pagina, immettere un **Nome soluzione** per la soluzione di simulazione del dispositivo.

1. Selezionare la **sottoscrizione** e l'**area** da usare per il provisioning della soluzione.

1. Specificare se si desidera che un nuovo IoT Hub distribuiti con la soluzione di simulazione del dispositivo. Se si seleziona questa casella, viene distribuito un nuovo IoT Hub nella sottoscrizione. Indipendentemente dall'opzione selezionata, è sempre possibile puntare la simulazione di un hub IoT.

1. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti:

    ![Dettagli sulla soluzione di simulazione di dispositivo](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Accedi alla soluzione

Una volta completato il processo di provisioning, è possibile accedere alla soluzione simulazione del dispositivo.

1. Nel **il provisioning delle soluzioni** pagina, fare clic su **avviare** sotto la nuova soluzione di simulazione del dispositivo:

    ![Scegliere nuova soluzione](media/iot-suite-device-simulation-deploy/newsolution.png)

1. È possibile visualizzare informazioni sulla soluzione simulazione del dispositivo nel pannello visualizzato. Scegliere **dashboard soluzione** per connettersi alla soluzione simulazione del dispositivo.

    > [!NOTE]
    > Dopo aver terminato con esso, è possibile eliminare la soluzione di simulazione del dispositivo da questo pannello.

    ![Riquadro della soluzione](media/iot-suite-device-simulation-deploy/properties.png)

1. Consente di visualizzare il dashboard di soluzioni di simulazione del dispositivo nel browser.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la soluzione
> * Distribuire la soluzione
> * Accedi alla soluzione

Ora che è stata distribuita la soluzione di simulazione del dispositivo, il passaggio successivo consiste nel [esplorare le funzionalità della soluzione simulazione del dispositivo](./iot-suite-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
