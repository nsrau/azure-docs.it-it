---
title: Distribuire la soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come effettuare il provisioning dell'acceleratore di soluzioni di monitoraggio remoto da azureiotsuite.com.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 42f6afcd3cb6880ba6c9cdd2a51e2a3e9ff2c2d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626854"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto

Questa esercitazione illustra come effettuare il provisioning dell'acceleratore di soluzioni di monitoraggio remoto. È necessario distribuire la soluzione dall'indirizzo azureiotsuite.com. È anche possibile distribuire la soluzione usando l'interfaccia della riga di comando. Per informazioni su questa opzione, vedere [Deploy a solution accelerator from the command line](iot-accelerators-remote-monitoring-deploy-cli.md) (Distribuire un acceleratore di soluzioni dalla riga di comando).

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

    ![Scegliere il monitoraggio remoto](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. Nella pagina **Create Remote Monitoring solution** (Crea soluzione di monitoraggio remoto) immettere il nome desiderato in **Nome soluzione** per l'acceleratore di soluzioni di monitoraggio remoto.

1. Selezionare una distribuzione **Basic** o **Standard**. Se la soluzione viene distribuita per esplorarne il funzionamento o eseguire una dimostrazione, scegliere l'opzione **Basic** per ridurre al minimo i costi.

1. Scegliere **Java** o **.NET** come linguaggio. Tutti i microservizi sono disponibili come implementazione Java o .NET.

1. Esaminare il pannello **Dettagli soluzione** per altre informazioni sulle scelte di configurazione.

1. Selezionare la **sottoscrizione** e l'**area** da usare per il provisioning della soluzione.

1. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti:

    ![Informazioni sulla soluzione di monitoraggio remoto](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Per informazioni sulla risoluzione dei problemi, vedere [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (Che cosa fare quando una distribuzione non riesce) nel repository GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Accedere all'acceleratore di soluzioni

Al termine del processo di provisioning, è possibile accedere all'acceleratore di soluzioni di monitoraggio remoto.

1. Nella pagina **Soluzioni di cui è stato effettuato il provisioning** scegliere la soluzione di monitoraggio remoto:

    ![Scegliere nuova soluzione](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. È possibile esaminare le informazioni sulla soluzione di monitoraggio remoto nel pannello visualizzato. Scegliere **Dashboard soluzione** per connettersi alla soluzione di monitoraggio remoto.

    > [!NOTE]
    > È possibile eliminare la soluzione di monitoraggio remoto da questo pannello quando non la si usa più.

    ![Riquadro della soluzione](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. La dashboard della soluzione di monitoraggio remoto viene visualizzata nel browser.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare l'acceleratore di soluzioni
> * Distribuire l'acceleratore di soluzioni
> * Accedere all'acceleratore di soluzioni

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità della dashboard della soluzione](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->