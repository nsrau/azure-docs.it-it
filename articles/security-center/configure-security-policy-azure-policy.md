---
title: Creare e modificare i criteri di sicurezza dei criteri di Azure usando l'API REST | Microsoft Docs
description: Informazioni sulla gestione dei criteri di criteri di Azure tramite un'API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522021"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configurare i criteri di sicurezza in criteri di Azure usando l'API REST

Nell'ambito dell'integrazione nativa con Criteri di Azure, Centro sicurezza di Azure consente di sfruttare l'API REST di Criteri di Azure per creare le assegnazioni dei criteri. Le istruzioni seguenti descrivono la creazione delle assegnazioni dei criteri, nonché la personalizzazione delle assegnazioni esistenti. 

Concetti importanti in Criteri di Azure 

- Una **definizione dei criteri** è una regola 

- Un' **iniziativa** è una raccolta di definizioni di criteri (regole) 

- Un' **assegnazione** è un'applicazione di un'iniziativa o un criterio a un ambito specifico (gruppo di gestione, sottoscrizione e così via) 

In Centro sicurezza è presente un'iniziativa integrata che include tutti i criteri di sicurezza relativi. Per valutare i criteri del Centro sicurezza per le risorse di Azure, è necessario creare un'assegnazione per il gruppo di gestione o la sottoscrizione che si vuole valutare.

Nell'iniziativa integrata sono presenti tutti i criteri del Centro sicurezza abilitati per impostazione predefinita. È possibile scegliere di disabilitare determinati criteri dall'iniziativa incorporata. Ad esempio, per applicare tutti i criteri del Centro sicurezza ad eccezione di **Web Application Firewall**, modificare il valore del parametro Effect del criterio in **disabled**. 

## <a name="api-examples"></a>Esempi di API

Negli esempi seguenti sostituire queste variabili:

- **{scope}** immettere il nome del gruppo di gestione o della sottoscrizione a cui si sta applicando il criterio.
- **{policyAssignmentName}** : immettere il [nome dell'assegnazione di criteri pertinente](#policy-names).
- **{name}** : immettere il proprio nome o il nome dell'amministratore che ha approvato la modifica dei criteri.

Questo esempio illustra come assegnare l'iniziativa del Centro sicurezza integrata in una sottoscrizione oppure in un gruppo di gestione:
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Questo esempio illustra come assegnare l'iniziativa del Centro sicurezza integrata in una sottoscrizione, con i criteri seguenti disabilitati: 

- Aggiornamenti del sistema ("systemUpdatesMonitoringEffect") 

- Configurazione di sicurezza ("systemConfigurationsMonitoringEffect") 

- Protezione di endpoint ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Questo esempio illustra come rimuovere un'assegnazione:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## Riferimento per i  nomi dei criteri <a name="policy-names"></a>

|Nome del criterio in Centro sicurezza|Nome del criterio visualizzato in Criteri di Azure |Nome del parametro di effetto del criterio|
|----|----|----|
|Crittografia SQL |Monitora i database SQL non crittografati nel Centro sicurezza di Azure |sqlEncryptionMonitoringEffect| 
|Controllo SQL |Monitorare i database SQL non controllati nel Centro sicurezza di Azure |sqlAuditingMonitoringEffect|
|Aggiornamenti del sistema |Monitora gli aggiornamenti di sistema mancanti nel Centro sicurezza di Azure |systemUpdatesMonitoringEffect|
|Crittografia di archiviazione |Controlla crittografia BLOB mancante per gli account di archiviazione |storageEncryptionMonitoringEffect|
|Accesso alla rete JIT |Monitorare il possibile accesso JIT (just-in-Time) di rete nel centro sicurezza di Azure |jitNetworkAccessMonitoringEffect |
|Controlli delle applicazioni adattivi |Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure |adaptiveApplicationControlsMonitoringEffect|
|Gruppi di sicurezza di rete |Monitorare gli accessi di rete permissivi nel Centro sicurezza di Azure |networkSecurityGroupsMonitoringEffect| 
|Configurazioni di sicurezza |Monitora le vulnerabilità del sistema operativo nel Centro sicurezza di Azure |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Monitora server senza Endpoint Protection nel Centro sicurezza di Azure |endpointProtectionMonitoringEffect |
|Crittografia del disco |Monitora i dischi di macchine virtuali non crittografati nel Centro sicurezza di Azure |diskEncryptionMonitoringEffect|
|Valutazione della vulnerabilità |Monitora le vulnerabilità delle macchine virtuali nel Centro sicurezza di Azure |vulnerabilityAssessmentMonitoringEffect|
|Web application firewall |Monitora le applicazioni Web non protette nel Centro sicurezza di Azure |webApplicationFirewallMonitoringEffect |
|Firewall di nuova generazione |Monitorare gli endpoint di rete non protetti nel Centro sicurezza di Azure| |


## <a name="next-steps"></a>Passaggi successivi

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Criteri di sicurezza personalizzati](custom-security-policies.md)
- [Panoramica dei criteri di sicurezza](tutorial-security-policy.md)