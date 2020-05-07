---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a5717f7bcb891f78054e3093c89f75622494aff
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837797"
---
|Nome |Descrizione |Effetto/i |Versione |GitHub |
|---|---|---|---|---|
|[La soluzione Backup di Azure deve essere abilitata per le macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Questo criterio consente di controllare se il servizio Backup di Azure è abilitato per tutte le macchine virtuali. Backup di Azure è una soluzione di backup con un clic economica che semplifica il ripristino dei dati ed è più facile da abilitare rispetto ad altri servizi di backup nel cloud. |AuditIfNotExists, Disabled |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configura il backup nelle macchine virtuali di una località in un insieme di credenziali centralizzato esistente nella stessa località](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Questo criterio consente di configurare la protezione di Backup di Azure nelle macchine virtuali in una determinata località in un insieme di credenziali centralizzato esistente nella stessa località. Si applica solo alle macchine virtuali che non sono già configurate per il backup. È consigliabile assegnare questo criterio a un massimo di 200 macchine virtuali. Se il criterio viene assegnato a più di 200 macchine virtuali, è possibile che il backup venga attivato alcune ore dopo la pianificazione definita. Questo criterio verrà migliorato per supportare più immagini di macchina virtuale. |deployIfNotExists, auditIfNotExists, disabled |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Distribuire le impostazioni di diagnostica per l'insieme di credenziali di Servizi di ripristino nell'area di lavoro Log Analytics per categorie specifiche delle risorse.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Distribuire le impostazioni di diagnostica per l'insieme di credenziali di Servizi di ripristino per lo streaming nell'area di lavoro Log Analytics per categorie specifiche delle risorse. Se una delle categorie specifiche delle risorse non è abilitata, verrà creata una nuova impostazione di diagnostica. |deployIfNotExists |1.0.0-preview |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
