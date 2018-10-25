---
title: Configurare gli account di Azure per Project Acoustics
titlesuffix: Azure Cognitive Services
description: Seguire questa guida per configurare gli account di archiviazione e Azure Batch necessari per gestire l'acustica.
services: cognitive-services
author: ashtat
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 05f7c435ca61930da6270c54e71c784f1b7aebcc
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900625"
---
# <a name="create-an-azure-batch-account"></a>Creare un account Azure Batch
Seguire questa guida per configurare gli account di archiviazione e Azure Batch necessari per gestire l'acustica. Per informazioni sul plug-in Unity sviluppato come parte di Project Acoustics, vedere [Informazioni sull'acustica](what-is-acoustics.md). Per informazioni su come incorporare l'acustica nel progetto Unity, vedere [Introduzione](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Ottenere una sottoscrizione di Azure
Per configurare gli account di archiviazione e Batch, è necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free/). Se si esegue l'iscrizione per la prima volta, Azure offre alcune risorse gratuite di durata limitata e 200 dollari di credito.

## <a name="create-azure-batch-and-storage-accounts"></a>Creare gli account di archiviazione e Azure Batch
Seguire ora [queste istruzioni](https://docs.microsoft.com/azure/batch/batch-account-create-portal) per configurare gli account Azure Batch e di archiviazione di Azure associato.

Selezionare le opzioni predefinite per entrambi gli account di archiviazione e Batch:
  
  ![Nuovo account Batch](media/NewBatchAccountCreate.png)

  ![Nuovo account di archiviazione](media/BatchStorageAccountCreate.png)

Per distribuire gli account, Azure impiega alcuni minuti. Cercare una notifica di completamento nell'angolo in alto a destra nel portale.
  
  ![Account distribuiti](media/BatchAccountsDeployNotification.png)

Gli account dovrebbero essere ora visibili nel dashboard.
  
  ![Dashboard del portale](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurare l'interfaccia utente del bake di acustica con le credenziali di Azure
Fare clic sul collegamento all'account Batch nel dashboard, quindi fare clic sul collegamento **Chiavi** nella pagina dell'account Batch per accedere alle credenziali.
  
  ![Collegamento Chiavi di Batch](media/BatchAccessKeys.png)

  ![Credenziali dell'account Batch](media/BatchKeysInfo.png)

Fare clic sul collegamento **Account di archiviazione** nella pagina per accedere alle credenziali dell'account di archiviazione di Azure.
  
  ![Credenziali dell'account di archiviazione](media/StorageKeysInfo.png)

Immettere queste credenziali nella scheda Bake, come illustrato nella [procedura dettagliata sull'interfaccia utente del bake](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Tipi di nodi e supporto di aree
Project Acoustics richiede nodi di VM di Azure con ottimizzazione per il calcolo delle serie F e H, che potrebbero non essere supportati in tutte le aree di Azure. Controllare [questa tabella](https://azure.microsoft.com/global-infrastructure/services) per assicurarsi di selezionare la località corretta per l'account Batch. Le macchine virtuali della serie H sono attualmente supportate nelle aree Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale e Giappone occidentale.

## <a name="upgrading-your-quota"></a>Aggiornamento della quota
Il provisioning degli account Azure Batch viene effettuato durante la creazione degli account con un limite di 20 core di calcolo. Potrebbe essere necessario aumentare questo limite per accelerare i tempi di bake, perché è possibile parallelizzare il carico di lavoro dell'acustica tra più nodi, fino al numero di punti di probe nella scena. Per richiedere un aumento di una quota, fare clic sul collegamento **Quota** nella pagina del portale di Azure Batch e quindi su **Richiedi aumento di quota**:

![Aumento di quota di Azure](media/azurequotas.png)

## <a name="next-steps"></a>Passaggi successivi
* Iniziare a [integrare l'acustica nei progetti Unity](getting-started.md)
* Esplorare la [scena di esempio](sample-walkthrough.md)

