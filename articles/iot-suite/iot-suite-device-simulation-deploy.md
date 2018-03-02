---
title: Distribuire la soluzione Simulazione dispositivi - Azure | Microsoft Docs
description: Questa esercitazione mostra come effettuare il provisioning della soluzione Simulazione dispositivi da azureiotsuite.com.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Distribuire la soluzione Simulazione dispositivi di Azure IoT

Questa esercitazione mostra come effettuare il provisioning di una soluzione Simulazione dispositivi. È necessario distribuire la soluzione dall'indirizzo azureiotsuite.com.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la soluzione Simulazione dispositivi
> * Distribuire la soluzione Simulazione dispositivi
> * Accedere alla soluzione Simulazione dispositivi

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Prima di distribuire la soluzione nella sottoscrizione di Azure, è necessario scegliere alcune opzioni di configurazione:

1. Accedere ad [azureiotsuite.com](https://www.azureiotsuite.com) usando le credenziali dell'account Azure e quindi fare clic su **+** per creare una nuova soluzione:

    ![Creare una nuova soluzione](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. Fare clic su **Seleziona** nel riquadro **Simulazione dispositivi**:

    ![Scegliere Simulazione dispositivi](media/iot-suite-device-simulation-deploy/select.png)

1. Nella pagina **Crea soluzione Simulazione dispositivi** immettere un **Nome della soluzione** per la soluzione Simulazione dispositivi.

1. Selezionare la **sottoscrizione** e l'**area** da usare per il provisioning della soluzione.

1. Specificare se si vuole distribuire un nuovo hub IoT con la soluzione Simulazione dispositivi. Se si seleziona questa casella, viene distribuito un nuovo hub IoT nella sottoscrizione. Indipendentemente dall'opzione selezionata, è sempre possibile associare la simulazione a qualsiasi hub IoT.

1. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti:

    ![Dettagli sulla soluzione Simulazione dispositivi](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Accedere alla soluzione

Al termine del processo di provisioning, è possibile accedere alla soluzione Simulazione dispositivi.

1. Nella pagina **Soluzioni di cui è stato effettuato il provisioning** fare clic su **Avvia** per la nuova soluzione Simulazione dispositivi:

    ![Scegliere nuova soluzione](media/iot-suite-device-simulation-deploy/newsolution.png)

1. È possibile esaminare le informazioni sulla soluzione Simulazione dispositivi nel pannello visualizzato. Scegliere **Dashboard soluzione** per connettersi alla soluzione Simulazione dispositivi.

    > [!NOTE]
    > È possibile eliminare la soluzione Simulazione dispositivi da questo pannello quando non la si usa più.

    ![Riquadro della soluzione](media/iot-suite-device-simulation-deploy/properties.png)

1. Il dashboard della soluzione Simulazione dispositivi viene visualizzato nel browser.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la soluzione
> * Distribuire la soluzione
> * Accedere alla soluzione

Ora che è stata distribuita la soluzione Simulazione dispositivi, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione Simulazione dispositivi](./iot-suite-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
