---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318351"
---
## <a name="storage"></a>Archiviazione

|  |  |
|---------|---------|
| [SKU consentiti per account di archiviazione e macchine virtuali](../articles/governance/policy/samples/allowed-skus-storage.md) | Richiede che gli account di archiviazione e le macchine virtuali usino SKU approvati. Usa i criteri predefiniti per garantire SKU approvati. Si specifica una matrice di SKU di macchine virtuali approvati e una matrice di SKU di account di archiviazione approvati. |
| [SKU degli account di archiviazione consentiti](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Richiede che gli account di archiviazione usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [Rifiutare il livello di accesso sporadico per gli account di archiviazione](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Impedisce l'uso del livello di accesso sporadico per gli account di archiviazione BLOB.  |
| [Assicurare solo traffico HTTPS per un account di archiviazione](../articles/governance/policy/samples/ensure-https-storage-account.md) | Richiede che gli account di archiviazione usino il traffico HTTPS.  |
| [Assicurare la crittografia dei file di archiviazione](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Richiede che la crittografia dei file sia abilitata per gli account di archiviazione.  |
| [Richiedere la crittografia dell'account di archiviazione](../articles/governance/policy/samples/require-storage-account-encryption.md) | Richiede all'account di archiviazione di usare la crittografia BLOB.  |