---
title: Distribuire la soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come effettuare il provisioning della soluzione preconfigurata di monitoraggio remoto da azureiotsuite.com.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 768c32e30509c74a292b2355a249ec010af2cd1b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>Distribuire la soluzione preconfigurata di monitoraggio remoto

Questa esercitazione illustra come effettuare il provisioning della soluzione preconfigurata per il monitoraggio remoto. È necessario distribuire la soluzione dall'indirizzo azureiotsuite.com. È anche possibile distribuire la soluzione usando l'interfaccia della riga di comando. Per informazioni su questa opzione, vedere [Deploy a preconfigured solution from the command line](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line) (Distribuire una soluzione preconfigurata dalla riga di comando).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la soluzione preconfigurata
> * Distribuire la soluzione preconfigurata
> * Accedere alla soluzione preconfigurata

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Distribuire la soluzione preconfigurata

Prima di poter distribuire la soluzione preconfigurata nella sottoscrizione di Azure, è necessario scegliere alcune opzioni di configurazione:

1. Accedere ad [azureiotsuite.com](https://www.azureiotsuite.com) usando le credenziali dell'account Azure e quindi fare clic su **+** per creare una nuova soluzione:

    ![Creare una nuova soluzione](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. Fare clic su **Seleziona** nel riquadro **Remote monitoring preview** (Monitoraggio remoto in anteprima).

    ![Scegliere il monitoraggio remoto](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. Nella pagina **Create Remote Monitoring solution** (Crea soluzione di monitoraggio remoto) immettere il nome desiderato in **Nome soluzione** per la soluzione preconfigurata di monitoraggio remoto.

1. Selezionare una distribuzione **Basic** o **Standard**. Se la soluzione viene distribuita per esplorarne il funzionamento o eseguire una dimostrazione, scegliere l'opzione **Basic** per ridurre al minimo i costi.

1. Scegliere **Java** o **.NET** come linguaggio. Tutti i microservizi sono disponibili come implementazione Java o .NET.

1. Esaminare il pannello **Dettagli soluzione** per altre informazioni sulle scelte di configurazione.

1. Selezionare la **sottoscrizione** e l'**area** da usare per il provisioning della soluzione.

1. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti:

    ![Informazioni sulla soluzione di monitoraggio remoto](media/iot-suite-remote-monitoring-deploy/createform.png)

Per informazioni sulla risoluzione dei problemi, vedere [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (Che cosa fare quando una distribuzione non riesce) nel repository GitHub.

## <a name="sign-in-to-the-preconfigured-solution"></a>Accedere alla soluzione preconfigurata

Al termine del processo di provisioning, è possibile accedere alla soluzione preconfigurata di monitoraggio remoto.

1. Nella pagina **Soluzioni di cui è stato effettuato il provisioning** scegliere la soluzione di monitoraggio remoto:

    ![Scegliere nuova soluzione](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. È possibile esaminare le informazioni sulla soluzione di monitoraggio remoto nel pannello visualizzato. Scegliere **Dashboard soluzione** per connettersi alla soluzione di monitoraggio remoto.

    > [!NOTE]
    > È possibile eliminare la soluzione di monitoraggio remoto da questo pannello quando non la si usa più.

    ![Riquadro della soluzione](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. Il dashboard della soluzione di monitoraggio remoto viene visualizzato nel browser.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la soluzione preconfigurata
> * Distribuire la soluzione preconfigurata
> * Accedere alla soluzione preconfigurata

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->