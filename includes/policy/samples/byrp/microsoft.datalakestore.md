---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22fc625b6da2e9c6320920274153a9962e0d910f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656264"
---
|Nome |Descrizione |Effetto/i |Versione |GitHub |
|---|---|---|---|---|
|[Distribuisci le impostazioni di diagnostica per Data Lake Storage Gen1 nell'hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8d096bc-85de-4c5f-8cfb-857bd1b9d62d) |Distribuisce le impostazioni di diagnostica per Data Lake Storage Gen1 per lo streaming in un hub eventi a livello di area quando viene creata o aggiornata un'istanza di Data Lake Storage Gen1 in cui manca questa impostazione di diagnostica. |DeployIfNotExists, Disabled |2.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Distribuisci le impostazioni di diagnostica per Data Lake Storage Gen1 nell'area di lavoro Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25763a0a-5783-4f14-969e-79d4933eb74b) |Distribuisce le impostazioni di diagnostica per Data Lake Storage Gen1 per lo streaming in un'area di lavoro Log Analytics a livello di area quando viene creata o aggiornata un'istanza di Data Lake Storage Gen1 in cui manca questa impostazione di diagnostica. |DeployIfNotExists, Disabled |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[È consigliabile abilitare i log di diagnostica in Azure Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |2.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Richiedi crittografia per gli account Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Questo criterio garantisce che la crittografia sia abilitata in tutti gli account Data Lake Store |deny |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
