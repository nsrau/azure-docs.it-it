---
title: Proteggere il cluster in Azure Data ExplorerSecure your cluster in Azure Data Explorer
description: Questo articolo descrive come proteggere il cluster in Azure Data Explorer all'interno del portale di Azure.This article describes how to secure your cluster in Azure Data Explorer within the Azure portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720345"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Proteggere il cluster in Azure Data Explorer - Portale di AzureSecure your cluster in Azure Data Explorer - Azure portal

[Crittografia disco](/azure/security/azure-security-disk-encryption-overview) di Azure consente di proteggere e proteggere i dati per soddisfare gli impegni di conformità e sicurezza dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Si integra anche con La chiave Vault di [Azure,](/azure/key-vault/)che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di garantire che tutti i dati nei dischi delle macchine virtuali siano crittografati. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Abilitare la crittografia inattivi nel portale di AzureEnable encryption at rest in the Azure portal
  
Le impostazioni di sicurezza del cluster consentono di abilitare la crittografia del disco nel cluster. L'abilitazione della [crittografia inattivi](/azure/security/fundamentals/encryption-atrest) nel cluster garantisce la protezione dei dati archiviati (inattivi). 

1. Nel portale di Azure passare alla risorsa cluster di Azure Data Explorer.In the Azure portal, go to your Azure Data Explorer cluster resource. Sotto l'intestazione **Impostazioni** selezionare **Sicurezza**. 

    ![Attivare la crittografia inattivi](media/manage-cluster-security/security-encryption-at-rest.png)

1. Nella finestra **Sicurezza,** selezionare **Attivato** per l'impostazione **Crittografia disco.** 

1. Selezionare **Salva**.
 
> [!NOTE]
> Selezionare **Disattivato** per disabilitare la crittografia dopo che è stata abilitata.

## <a name="next-steps"></a>Passaggi successivi

[Controllare l'integrità del cluster](/azure/data-explorer/check-cluster-health)
