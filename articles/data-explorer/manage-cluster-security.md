---
title: Proteggere il cluster in Azure Esplora dati
description: Questo articolo descrive come proteggere il cluster in Azure Esplora dati all'interno del portale di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172600"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Proteggere il cluster in Azure Esplora dati

[Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption-overview) consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Si integra anche con [Azure Key Vault](/azure/key-vault/) che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di assicurarsi che tutti i dati nei dischi delle macchine virtuali siano crittografati quando sono inattivi in archiviazione di Azure. 

Le impostazioni di sicurezza del cluster consentono di abilitare la crittografia del disco nel cluster.
  
## <a name="enable-encryption-at-rest"></a>Abilitare la crittografia inattiva
  
L'abilitazione della [crittografia](/azure/security/fundamentals/encryption-atrest) dei dati inattivi nel cluster garantisce la protezione dei dati archiviati (inattivi). 

1. Nella portale di Azure passare alla risorsa cluster di Azure Esplora dati. Sotto l'intestazione **Impostazioni** selezionare **sicurezza**. 

    ![Attivare la crittografia di tutti i reparti](media/manage-cluster-security/security-encryption-at-rest.png)

1. Nella finestra **sicurezza** selezionare **on** per l'impostazione di sicurezza **crittografia dischi** . 

1. Selezionare **Salva**.
 
> [!NOTE]
> Selezionare **disattivato** per disabilitare la crittografia dopo che è stata abilitata.

## <a name="next-steps"></a>Passaggi successivi

[Controllare l'integrità del cluster](/azure/data-explorer/check-cluster-health)
