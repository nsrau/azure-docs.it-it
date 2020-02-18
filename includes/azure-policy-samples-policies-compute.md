---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170099"
---
|Nome |Descrizione |Effetto/i |Versione |
|---|---|---|---|
|[SKU di macchine virtuali consentiti](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Questo criterio consente di specificare un set di SKU di macchine virtuali che possono essere distribuiti dall'organizzazione. |Nega |1.0.0 |
|[Controlla macchine virtuali in cui non è configurato il ripristino di emergenza](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Controlla le macchine virtuali in cui non è configurato il ripristino di emergenza. Per altre informazioni sul ripristino di emergenza, vedere https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Controlla macchine virtuali che non usano dischi gestiti](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Questo criterio controlla le macchine virtuali che non usano dischi gestiti |controllo |1.0.0 |
|[Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Questo criterio distribuisce un'estensione IaaSAntimalware Microsoft con una configurazione predefinita quando in una macchina virtuale non è configurata l'estensione antimalware. |deployIfNotExists |1.0.0 |
|[È consigliabile abilitare i log di diagnostica nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |È consigliabile abilitare i log per poter ricreare la traccia delle attività quando sono necessarie indagini in caso di evento imprevisto o compromissione. |AuditIfNotExists, Disabled |1.0.0 |
|[È consigliabile configurare Microsoft Antimalware per Azure per aggiornare automaticamente le firme di protezione](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Questo criterio controlla le macchine virtuali Windows non configurate con l'aggiornamento automatico delle firme di protezione di Microsoft Antimalware. |AuditIfNotExists, Disabled |1.0.0 |
|[È consigliabile distribuire l'estensione Microsoft IaaSAntimalware nei server Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Questo criterio controlla le macchine virtuali Windows Server in cui non è distribuita l'estensione Microsoft IaaSAntimalware. |AuditIfNotExists, Disabled |1.0.0 |
|[Devono essere installate solo le estensioni macchina virtuale approvate](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Questo criterio regolamenta le estensioni macchina virtuale non approvate. |Audit, Deny, Disabled |1.0.0 |
|[Richiedi l'applicazione automatica di patch alle immagini del sistema operativo nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Questo criterio impone l'abilitazione dell'applicazione automatica di patch alle immagini del sistema operativo nei set di scalabilità di macchine virtuali per mantenere sempre protette le macchine virtuali, applicando le patch di sicurezza più recenti ogni mese. |deny |1.0.0 |
|[È consigliabile crittografare i dischi non collegati](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Questo criterio controlla i dischi non collegati in cui non è abilitata la crittografia. |Audit, Disabled |1.0.0 |
|[È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |È possibile usare la nuova versione di ARM per le macchine virtuali per fornire funzionalità di sicurezza migliorate, ad esempio controllo di accesso (controllo degli accessi in base al ruolo) più avanzato, controllo ottimizzato, distribuzione e governance basate su ARM, accesso alle identità gestite, accesso all'insieme di credenziali delle chiavi per i segreti, autenticazione basata su Azure AD e supporto di tag e gruppi di risorse per una gestione della sicurezza semplificata |Audit, Deny, Disabled |1.0.0 |
