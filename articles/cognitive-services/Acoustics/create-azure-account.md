---
title: Configurazione dell'account Batch di Azure acustica del progettoProject Acoustics Azure Batch Account Setup
titlesuffix: Azure Cognitive Services
description: Questa procedura descrive la configurazione di un account Batch di Azure per l'uso con le integrazioni del motore Project Acoustics Unity e Unreal.This how-to describes setting up an Azure Batch account for use with Project Acoustics Unity and Unreal engine integrations.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855091"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configurazione dell'account Batch di Azure acustica del progettoProject Acoustics Azure Batch Account Setup
Questa procedura descrive la configurazione di un account Batch di Azure per l'uso con le integrazioni del motore Project Acoustics Unity e Unreal.This how-to describes setting up an Azure Batch account for use with Project Acoustics Unity and Unreal engine integrations.

## <a name="get-an-azure-subscription"></a>Ottenere una sottoscrizione di Azure
Per configurare gli account di archiviazione e Batch, è necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free/). Se si esegue l'iscrizione per la prima volta, Azure offre alcune risorse gratuite di durata limitata e 200 dollari di credito.

## <a name="create-azure-batch-and-storage-accounts"></a>Creare gli account di archiviazione e Azure Batch
Seguire ora [queste istruzioni](https://docs.microsoft.com/azure/batch/batch-account-create-portal) per configurare gli account Azure Batch e di archiviazione di Azure associato.

Selezionare le opzioni predefinite per entrambi gli account di archiviazione e Batch:
  
  ![Screenshot delle opzioni dei nuovi account batch di Azure che mostrano le impostazioni predefinite](media/new-batch-account-create.png)

  ![Screenshot delle opzioni dei nuovi account di Archiviazione di Azure che mostrano le impostazioni predefinite](media/batch-storage-account-create.png)

Per distribuire gli account, Azure impiega alcuni minuti. Cercare una notifica di completamento nell'angolo in alto a destra nel portale.
  
  ![Screenshot della notifica distribuita degli account di Azure](media/batch-accounts-deploy-notification.png)

Gli account dovrebbero essere ora visibili nel dashboard.
  
  ![Screenshot del dashboard del portale di Azure che mostra un account Batch e archiviazione](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurare l'interfaccia utente del bake di acustica con le credenziali di Azure
Fare clic sul collegamento all'account Batch nel dashboard, quindi fare clic sul collegamento **Chiavi** nella pagina dell'account Batch per accedere alle credenziali.
  
  ![Screenshot dell'account Batch di Azure con collegamento alla pagina Chiavi evidenziato](media/batch-access-keys.png)

  ![Screenshot della pagina delle chiavi dell'account Batch di Azure con chiavi di accesso](media/batch-keys-info.png)

Fare clic sul collegamento **Account di archiviazione** nella pagina per accedere alle credenziali dell'account di archiviazione di Azure.
  
  ![Screenshot della pagina delle chiavi dell'account di Archiviazione di Azure con chiavi di accesso](media/storage-keys-info.png)

Inserisci queste credenziali nel [plugin Unity bake](unity-baking.md) o [Unreal bake plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipi di nodi e supporto di aree
L'acustica del progetto richiede nodi di vm di Azure ottimizzati per il calcolo delle serie Fsv2 e H che potrebbero non essere supportati in tutte le aree di Azure.Project Acoustics requires Fsv2-- and H-series compute optimized Azure VM nodes which may not be supported in all Azure regions. Controllare [questa tabella](https://azure.microsoft.com/global-infrastructure/services) per assicurarsi di selezionare la località corretta per l'account Batch.
![Screenshot che mostra macchine virtuali di Azure per area](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Aggiornamento della quota
Il provisioning degli account Azure Batch viene effettuato durante la creazione degli account con un limite di 20 core di calcolo. Potremmo aumentare questo limite per tempi di cottura più veloci, perché è possibile parallelizzare il carico di lavoro acustico su molti nodi, fino al numero di punti di sonda nella scena. Per richiedere un aumento di una quota, fare clic sul collegamento **Quota** nella pagina del portale di Azure Batch e quindi su **Richiedi aumento di quota**:

![Screenshot della pagina Quota di Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passaggi successivi
* Integrazione del plug-in Proiettiva progetto nel progetto [Unity](unity-integration.md) o [Unreal](unreal-integration.md)

