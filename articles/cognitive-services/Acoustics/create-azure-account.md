---
title: Configurazione dell'Account Azure Batch acustica progetto
titlesuffix: Azure Cognitive Services
description: Questa procedura viene illustrata l'impostazione di un account Azure Batch per l'utilizzo con progetti acustica Unity e Unreal integrazioni di motore.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: db4f96ff7c355f3582966e4daa945f54a6e5b847
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616544"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configurazione dell'Account Azure Batch acustica progetto
Questa procedura viene illustrata l'impostazione di un account Azure Batch per l'utilizzo con progetti acustica Unity e Unreal integrazioni di motore.

## <a name="get-an-azure-subscription"></a>Ottenere una sottoscrizione di Azure
Per configurare gli account di archiviazione e Batch, è necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free/). Se si esegue l'iscrizione per la prima volta, Azure offre alcune risorse gratuite di durata limitata e 200 dollari di credito.

## <a name="create-azure-batch-and-storage-accounts"></a>Creare gli account di archiviazione e Azure Batch
Seguire ora [queste istruzioni](https://docs.microsoft.com/azure/batch/batch-account-create-portal) per configurare gli account Azure Batch e di archiviazione di Azure associato.

Selezionare le opzioni predefinite per entrambi gli account di archiviazione e Batch:
  
  ![Screenshot di Azure Batch nuovo account di opzioni che mostra le impostazioni predefinite](media/new-batch-account-create.png)

  ![Screenshot di Azure nuovo account di archiviazione di opzioni che mostra le impostazioni predefinite](media/batch-storage-account-create.png)

Per distribuire gli account, Azure impiega alcuni minuti. Cercare una notifica di completamento nell'angolo in alto a destra nel portale.
  
  ![Account di screenshot di Azure distribuito notifica](media/batch-accounts-deploy-notification.png)

Gli account dovrebbero essere ora visibili nel dashboard.
  
  ![Dashboard del portale di screenshot di Azure che mostra un account di archiviazione e Batch](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurare l'interfaccia utente del bake di acustica con le credenziali di Azure
Fare clic sul collegamento all'account Batch nel dashboard, quindi fare clic sul collegamento **Chiavi** nella pagina dell'account Batch per accedere alle credenziali.
  
  ![Account di screenshot di Azure Batch con collegamento alla pagina codici evidenziato](media/batch-access-keys.png)

  ![Pagina chiavi di account screenshot di Azure Batch con le chiavi di accesso](media/batch-keys-info.png)

Fare clic sul collegamento **Account di archiviazione** nella pagina per accedere alle credenziali dell'account di archiviazione di Azure.
  
  ![Pagina chiavi dell'account screenshot di archiviazione di Azure con chiavi di accesso](media/storage-keys-info.png)

Immettere queste credenziali nel [plug-in di Unity bake](unity-baking.md) o [plug-in bake Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipi di nodi e supporto di aree
Progetto acustica richiede che e H-macchine virtuali serie Fsv2 calcolo ottimizzato nodi macchina virtuale di Azure che potrebbero non essere supportati in tutte le aree di Azure. Controllare [questa tabella](https://azure.microsoft.com/global-infrastructure/services) per assicurarsi di selezionare la località corretta per l'account Batch.
![Screenshot delle macchine virtuali di Azure per area](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Aggiornamento della quota
Il provisioning degli account Azure Batch viene effettuato durante la creazione degli account con un limite di 20 core di calcolo. Si potrebbe essere necessario aumentare questo limite per accelerare bake, poiché è possibile parallelizzare il carico di lavoro acustica in molti nodi, fino al numero di punti di probe nella scena. Per richiedere un aumento di una quota, fare clic sul collegamento **Quota** nella pagina del portale di Azure Batch e quindi su **Richiedi aumento di quota**:

![Pagina della schermata di Quota di Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passaggi successivi
* Integrare il plug-in progetto acustica nel [Unity](unity-integration.md) oppure [Unreal](unreal-integration.md) progetto

