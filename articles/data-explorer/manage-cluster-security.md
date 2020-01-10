---
title: Proteggere il cluster in Azure Esplora dati
description: Questo articolo descrive come proteggere il cluster in Azure Esplora dati all'interno del portale di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720345"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Proteggere il cluster in Azure Esplora dati-portale di Azure

[Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption-overview) consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Si integra anche con [Azure Key Vault](/azure/key-vault/), che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di assicurarsi che tutti i dati nei dischi delle macchine virtuali siano crittografati. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Abilitare la crittografia dei inattivi nella portale di Azure
  
Le impostazioni di sicurezza del cluster consentono di abilitare la crittografia del disco nel cluster. L'abilitazione della [crittografia](/azure/security/fundamentals/encryption-atrest) dei dati inattivi nel cluster garantisce la protezione dei dati archiviati (inattivi). 

1. Nella portale di Azure passare alla risorsa cluster di Azure Esplora dati. Sotto l'intestazione **Impostazioni** selezionare **sicurezza**. 

    ![Attivare la crittografia di tutti i reparti](media/manage-cluster-security/security-encryption-at-rest.png)

1. Nella finestra **sicurezza** selezionare **on** per l'impostazione di sicurezza **crittografia dischi** . 

1. Selezionare **Salva**.
 
> [!NOTE]
> Selezionare **disattivato** per disabilitare la crittografia dopo che è stata abilitata.

## <a name="next-steps"></a>Passaggi successivi

[Controllare l'integrità del cluster](/azure/data-explorer/check-cluster-health)
