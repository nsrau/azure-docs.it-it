---
title: Configurazione dell'account Azure Batch per l'acustica del progetto
titlesuffix: Azure Cognitive Services
description: Questa procedura descrive la configurazione di un account Azure Batch per l'uso con unit Acoustics di Project e con le integrazioni Unreal Engine.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855091"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configurazione dell'account Azure Batch per l'acustica del progetto
Questa procedura descrive la configurazione di un account Azure Batch per l'uso con unit Acoustics di Project e con le integrazioni Unreal Engine.

## <a name="get-an-azure-subscription"></a>Acquistare una sottoscrizione Azure
Per configurare gli account di archiviazione e Batch, è necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free/). Se si esegue l'iscrizione per la prima volta, Azure offre alcune risorse gratuite di durata limitata e 200 dollari di credito.

## <a name="create-azure-batch-and-storage-accounts"></a>Creare gli account di archiviazione e Azure Batch
Seguire ora [queste istruzioni](https://docs.microsoft.com/azure/batch/batch-account-create-portal) per configurare gli account Azure Batch e di archiviazione di Azure associato.

Selezionare le opzioni predefinite per entrambi gli account di archiviazione e Batch:
  
  ![Screenshot delle opzioni di Azure Batch nuovi account che mostrano le impostazioni predefinite](media/new-batch-account-create.png)

  ![Screenshot delle opzioni di nuovi account di archiviazione di Azure con le impostazioni predefinite](media/batch-storage-account-create.png)

Per distribuire gli account, Azure impiega alcuni minuti. Cercare una notifica di completamento nell'angolo in alto a destra nel portale.
  
  ![Screenshot della notifica distribuita degli account Azure](media/batch-accounts-deploy-notification.png)

Gli account dovrebbero essere ora visibili nel dashboard.
  
  ![Screenshot del dashboard portale di Azure che mostra un batch e un account di archiviazione](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurare l'interfaccia utente del bake di acustica con le credenziali di Azure
Fare clic sul collegamento all'account Batch nel dashboard, quindi fare clic sul collegamento **Chiavi** nella pagina dell'account Batch per accedere alle credenziali.
  
  ![Screenshot dell'account Azure Batch con collegamento alla pagina chiavi evidenziato](media/batch-access-keys.png)

  ![Screenshot della pagina delle chiavi dell'account Azure Batch con chiavi di accesso](media/batch-keys-info.png)

Fare clic sul collegamento **Account di archiviazione** nella pagina per accedere alle credenziali dell'account di archiviazione di Azure.
  
  ![Screenshot della pagina delle chiavi dell'account di archiviazione di Azure con chiavi di accesso](media/storage-keys-info.png)

Immettere queste credenziali nel plug-in [Unity Bake](unity-baking.md) o [Unreal Bake](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipi di nodi e supporto di aree
Il progetto Acoustics richiede nodi di macchine virtuali di Azure con ottimizzazione per il calcolo della serie Fsv2 e H che potrebbero non essere supportati in tutte le aree di Azure. Controllare [questa tabella](https://azure.microsoft.com/global-infrastructure/services) per assicurarsi di selezionare la località corretta per l'account Batch.
![Screenshot che mostra le macchine virtuali di Azure in base all'area](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Aggiornamento della quota
Il provisioning degli account Azure Batch viene effettuato durante la creazione degli account con un limite di 20 core di calcolo. Potrebbe essere necessario aumentare questo limite per tempi di cottura più rapidi, perché è possibile parallelizzare il carico di lavoro acustico in molti nodi, fino al numero di punti di probe nella scena. Per richiedere un aumento di una quota, fare clic sul collegamento **Quota** nella pagina del portale di Azure Batch e quindi su **Richiedi aumento di quota**:

![Screenshot della pagina di quota di Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passaggi successivi
* Integrare il plug-in Project Acoustics in [Unity](unity-integration.md) o [Unreal](unreal-integration.md) Project

