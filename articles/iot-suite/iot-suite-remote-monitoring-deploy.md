---
title: Distribuire la soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come effettuare il provisioning dell'acceleratore di soluzioni di monitoraggio remoto da azureiotsuite.com.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: eb78ce91755c43f1c6fedf62a70238df911b940f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33773633"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto

Questa esercitazione illustra come effettuare il provisioning dell'acceleratore di soluzioni di monitoraggio remoto. È necessario distribuire la soluzione dall'indirizzo azureiotsuite.com. È anche possibile distribuire la soluzione usando l'interfaccia della riga di comando. Per informazioni su questa opzione, vedere [Deploy a solution accelerator from the command line](iot-suite-remote-monitoring-deploy-cli.md) (Distribuire un acceleratore di soluzioni dalla riga di comando).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare l'acceleratore di soluzioni
> * Distribuire l'acceleratore di soluzioni
> * Accedere all'acceleratore di soluzioni

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Distribuire l'acceleratore di soluzioni

Prima di distribuire l'acceleratore di soluzioni nella sottoscrizione di Azure, è necessario scegliere alcune opzioni di configurazione:

1. Accedere a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) usando le credenziali dell'account Azure.

1. Fare clic su **Try Now** (Prova) nel riquadro **Monitoraggio remoto**.

    ![Scegliere il monitoraggio remoto](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. Nella pagina **Create Remote Monitoring solution** (Crea soluzione di monitoraggio remoto) immettere il nome desiderato in **Nome soluzione** per l'acceleratore di soluzioni di monitoraggio remoto.

1. Selezionare una distribuzione **Basic** o **Standard**. Se la soluzione viene distribuita per esplorarne il funzionamento o eseguire una dimostrazione, scegliere l'opzione **Basic** per ridurre al minimo i costi.

1. Scegliere **Java** o **.NET** come linguaggio. Tutti i microservizi sono disponibili come implementazione Java o .NET.

1. Esaminare il pannello **Dettagli soluzione** per altre informazioni sulle scelte di configurazione.

1. Selezionare la **sottoscrizione** e l'**area** da usare per il provisioning della soluzione.

1. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti:

    ![Informazioni sulla soluzione di monitoraggio remoto](media/iot-suite-remote-monitoring-deploy/createform.png)

Per informazioni sulla risoluzione dei problemi, vedere [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (Che cosa fare quando una distribuzione non riesce) nel repository GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Accedere all'acceleratore di soluzioni

Al termine del processo di provisioning, è possibile accedere all'acceleratore di soluzioni di monitoraggio remoto.

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
> * Configurare l'acceleratore di soluzioni
> * Distribuire l'acceleratore di soluzioni
> * Accedere all'acceleratore di soluzioni

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->