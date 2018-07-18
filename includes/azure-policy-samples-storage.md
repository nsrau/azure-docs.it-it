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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664630"
---
## <a name="storage"></a>Archiviazione

|  |  |
|---------|---------|
| [SKU consentiti per account di archiviazione e macchine virtuali](../articles/azure-policy/scripts/allowed-skus-storage.md) | Richiede che gli account di archiviazione e le macchine virtuali usino SKU approvati. Usa i criteri predefiniti per garantire SKU approvati. Si specifica una matrice di SKU di macchine virtuali approvati e una matrice di SKU di account di archiviazione approvati. |
| [SKU degli account di archiviazione consentiti](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Richiede che gli account di archiviazione usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [Rifiutare il livello di accesso sporadico per gli account di archiviazione](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Impedisce l'uso del livello di accesso sporadico per gli account di archiviazione BLOB.  |
| [Assicurare solo traffico HTTPS per un account di archiviazione](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Richiede che gli account di archiviazione usino il traffico HTTPS.  |
| [Assicurare la crittografia dei file di archiviazione](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Richiede che la crittografia dei file sia abilitata per gli account di archiviazione.  |
| [Richiedere la crittografia dell'account di archiviazione](../articles/azure-policy/scripts/req-store-acct-enc.md) | Richiede all'account di archiviazione di usare la crittografia BLOB.  |