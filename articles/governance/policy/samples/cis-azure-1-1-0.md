---
title: Dettagli della conformità con le normative per CIS Microsoft Azure Foundations Benchmark
description: Dettagli dell'iniziativa incorporata di conformità con le normative CIS Microsoft Azure Foundations Benchmark. Ogni controllo viene mappato a una o più definizioni di Criteri di Azure che assistono nella valutazione.
ms.date: 11/20/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 2ed1a40ba7589d25864cd5bdd90526635f189d6d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991316"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>Dettagli dell'iniziativa incorporata di conformità con le normative CIS Microsoft Azure Foundations Benchmark

L'articolo seguente definisce il mapping dell'iniziativa predefinita di conformità alle normative di Criteri di Azure ai **domini di conformità** e ai **controlli** di CIS Microsoft Azure Foundations Benchmark.
Per altre informazioni su questo standard di conformità, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/). Per informazioni sulle _Proprietà_, vedere [Struttura delle definizioni di criteri in Criteri di Azure](../concepts/definition-structure.md#type) e [Responsabilità condivisa nel cloud](../../../security/fundamentals/shared-responsibility.md).

I mapping seguenti riguardano i controlli di **CIS Microsoft Azure Foundations Benchmark**. Usare la barra di spostamento a destra per passare direttamente a un **dominio di conformità** specifico. Molti controlli vengono implementati con una definizione dell'iniziativa [Criteri di Azure](../overview.md). Per esaminare la definizione dell'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**.
Quindi, trovare e selezionare la definizione dell'iniziativa predefinita di conformità alle normative **CIS Microsoft Azure Foundations Benchmark 1.1.0**.

Questa iniziativa predefinita viene distribuita come parte dell'[esempio di progetto CIS Microsoft Azure Foundations Benchmark](../../blueprints/samples/cis-azure-1-1-0.md).

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../overview.md).
> Questi criteri possono aiutare a [valutare la conformità](../how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza uno-a-uno o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo alle definizioni dei criteri e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra domini di conformità, controlli e definizioni di Criteri di Azure per questo standard di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0_audit.json).

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti con privilegi

**ID**: CIS Azure 1.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di scrittura per evitare una violazione di account o risorse. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con autorizzazioni di proprietario per evitare una violazione di account o risorse. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti senza privilegi

**ID**: CIS Azure 1.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di lettura per evitare una violazione di account o risorse. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Assicurarsi che non siano presenti utenti guest

**ID**: CIS Azure 1.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di proprietario in modo da evitare l'accesso non monitorato. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |È necessario rimuovere dalla sottoscrizione gli account esterni con privilegi di lettura in modo da evitare l'accesso non monitorato. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |È necessario rimuovere dalla sottoscrizione gli account esterni con privilegi di scrittura in modo da evitare l'accesso non monitorato. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Assicurarsi che non siano stati creati ruoli di proprietario della sottoscrizione personalizzati

**ID**: CIS Azure 1.23 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Questo criterio garantisce che non esista alcun ruolo di proprietario della sottoscrizione personalizzato. |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Centro sicurezza

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Assicurarsi che "Provisioning automatico dell'agente di monitoraggio" sia impostata su "Sì"

**ID**: CIS Azure 2.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il provisioning automatico dell'agente di monitoraggio di Log Analytics nella sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Abilitare il provisioning automatico dell'agente di monitoraggio di Log Analytics per raccogliere i dati sulla sicurezza |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora aggiornamenti del sistema" non sia disabilitata

**ID**: CIS Azure 2.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli aggiornamenti di sistema devono essere installati nelle macchine](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora vulnerabilità del sistema operativo" non sia disabilitata

**ID**: CIS Azure 2.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |I server che non soddisfano la baseline configurata verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora protezione endpoint" non sia disabilitata

**ID**: CIS Azure 2.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |I server in cui non è installato un agente di Endpoint Protection verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora crittografia disco" non sia disabilitata

**ID**: CIS Azure 2.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile applicare la crittografia del disco nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Le macchine virtuali per cui non è abilitata la crittografia dei dischi verranno monitorate dal Centro sicurezza di Azure che invierà raccomandazioni. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora gruppi di sicurezza di rete" non sia disabilitata

**ID**: CIS Azure 2.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali con connessione Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Centro sicurezza di Azure analizza i modelli di traffico delle macchine virtuali con connessione Internet e fornisce raccomandazioni sulle regole del gruppo di sicurezza di rete al fine di ridurre la superficie di attacco potenziale |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Abilita monitoraggio firewall di nuova generazione" non sia disabilitata

**ID**: CIS Azure 2.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Protegge le macchine virtuali da potenziali minacce limitandone l'accesso con i gruppi di sicurezza di rete. Per altre informazioni sul controllo del traffico con i gruppi di sicurezza di rete, vedere [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Le subnet devono essere associate a un gruppo di sicurezza di rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |È possibile proteggere la subnet dalle minacce potenziali limitando l'accesso alla subnet con un gruppo di sicurezza di rete. I gruppi di sicurezza di rete contengono un elenco di regole ACL (elenco di controllo di accesso) che consentono o negano il traffico di rete alla subnet. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora la valutazione della vulnerabilità" non sia disabilitata

**ID**: CIS Azure 2.10 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitora le vulnerabilità rilevate dalla soluzione Valutazione della vulnerabilità e le macchine virtuali senza una soluzione Valutazione della vulnerabilità nel Centro sicurezza di Azure che invierà i consigli corrispondenti. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora accesso JIT alla rete" non sia disabilitata

**ID**: CIS Azure 2.12 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le porte di gestione delle macchine virtuali devono essere protette tramite un controllo di accesso alla rete JIT](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |I possibili accessi JIT alla rete verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora l'inserimento delle applicazioni adattive nell'elenco elementi consentiti" non sia disabilitata

**ID**: CIS Azure 2.13 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I controlli applicazioni adattivi per la definizione delle applicazioni sicure devono essere abilitati nei computer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Abilita i controlli applicazioni per definire l'elenco delle applicazioni sicure note in esecuzione nei computer e avvisare l'utente quando vengono eseguite altre applicazioni. In questo modo si rafforza la protezione dei computer dal malware. Per semplificare il processo di configurazione e gestione delle regole, il Centro sicurezza usa Machine Learning per analizzare le applicazioni in esecuzione in ogni computer e suggerire l'elenco di applicazioni sicure note. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora controllo SQL" non sia disabilitata

**ID**: CIS Azure 2.14 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il controllo in SQL Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Abilitare il controllo in SQL Server per verificare le attività del database in tutti i database nel server, ad eccezione di Synapse, e salvarle in un log di controllo. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora crittografia SQL" non sia disabilitata

**ID**: CIS Azure 2.15 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare Transparent Data Encryption nei database SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti relativi alla conformità |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Assicurarsi che il criterio "Indirizzi di posta elettronica dei contatti di sicurezza" sia configurato

**ID**: CIS Azure 2.16 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile fornire un indirizzo di posta elettronica dei contatti di sicurezza per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Immettere un indirizzo di posta elettronica per ricevere le notifiche quando il Centro sicurezza di Azure rileva risorse compromesse |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-security-contact-phone-number-is-set"></a>Assicurarsi che il criterio "Numero di telefono" del contatto di sicurezza sia configurato

**ID**: CIS Azure 2.17 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile fornire il numero di telefono dei contatti di sicurezza per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |Immettere un numero di telefono per ricevere le notifiche quando il Centro sicurezza di Azure rileva risorse compromesse |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Assicurarsi che il criterio "Invia una notifica tramite posta elettronica per avvisi con gravità elevata" sia impostato su "Sì"

**ID**: CIS Azure 2.18 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare le notifiche di posta elettronica per gli avvisi con gravità alta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Abilitare l'invio di avvisi di sicurezza tramite posta elettronica al contatto di sicurezza, per consentirgli di ricevere i messaggi relativi agli avvisi di sicurezza da Microsoft. Questo garantisce che le persone corrette siano sempre informate sugli eventuali problemi di sicurezza e che possano attenuare tempestivamente i rischi |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Assicurarsi che "Invia un messaggio di posta elettronica anche ai proprietari della sottoscrizione" sia impostata su 'Sì'

**ID**: CIS Azure 2.19 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare le notifiche di posta elettronica al proprietario della sottoscrizione per gli avvisi con gravità alta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Abilitare l'invio di avvisi di sicurezza tramite posta elettronica al proprietario della sottoscrizione, per consentirgli di ricevere i messaggi relativi agli avvisi di sicurezza da Microsoft e di essere sempre informato sugli eventuali problemi di sicurezza e attenuare tempestivamente il rischio |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Account di archiviazione

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Assicurarsi che l'opzione "Trasferimento sicuro obbligatorio" sia impostata su "Abilitato".

**ID**: CIS Azure 3.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il trasferimento sicuro agli account di archiviazione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito di controllo del trasferimento sicuro nell'account di archiviazione. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Assicurarsi che la regola di accesso alla rete predefinita per gli account di archiviazione sia impostata su Nega

**ID**: CIS Azure 3.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account di archiviazione devono limitare l'accesso alla rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |L'accesso di rete agli account di archiviazione deve essere limitato. Configurare regole di rete in modo che l'account di archiviazione sia accessibile solo alle applicazioni provenienti da reti consentite. Per consentire le connessioni da client Internet o locali specifici, è possibile concedere l'accesso al traffico da reti virtuali di Azure specifiche o a intervalli di indirizzi IP Internet pubblici |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Assicurarsi che il criterio "Servizi Microsoft attendibili" sia abilitato per l'accesso all'account di archiviazione

**ID**: CIS Azure 3.8 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account di archiviazione devono consentire l'accesso da servizi Microsoft attendibili](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Alcuni servizi Microsoft che interagiscono con gli account di archiviazione vengono eseguiti da reti alle quali non è possibile concedere l'accesso tramite le regole di rete. Per far sì che questo tipo di servizi funzioni come previsto, consentire al set di servizi Microsoft attendibili di ignorare le regole di rete. Questi servizi usano quindi l'autenticazione avanzata per accedere all'account di archiviazione. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Servizi di database

### <a name="ensure-that-auditing-is-set-to-on"></a>Assicurarsi che "Controllo" sia impostato su "Attivato"

**ID**: CIS Azure 4.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il controllo in SQL Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Abilitare il controllo in SQL Server per verificare le attività del database in tutti i database nel server, ad eccezione di Synapse, e salvarle in un log di controllo. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Assicurarsi che "AuditActionGroups" nel criterio "controllo" per un server SQL sia impostato correttamente

**ID**: CIS Azure 4.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le impostazioni di controllo SQL devono avere gruppi di azione configurati per acquisire attività critiche](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |La proprietà AuditActionsAndGroups deve contenere almeno SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP per garantire una registrazione di controllo accurata |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Assicurarsi che il periodo di conservazione dei dati di controllo sia "superiore a 90 giorni"

**ID**: CIS Azure 4.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I server SQL devono essere configurati con un periodo di conservazione di controllo maggiore di 90 giorni.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Controllare i server SQL configurati con un periodo di conservazione di controllo minore di 90 giorni. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Assicurarsi che "Sicurezza dei dati avanzata" in un server SQL sia impostata su "Attivata"

**ID**: CIS Azure 4.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La soluzione Sicurezza dei dati avanzata deve essere abilitata nell'istanza gestita di SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Controlla ogni istanza gestita di SQL senza Sicurezza dei dati avanzata. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[È consigliabile abilitare la sicurezza avanzata dei dati nei server SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Controlla server SQL senza Sicurezza dei dati avanzata |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Assicurarsi che l'amministratore di Azure Active Directory sia configurato

**ID**: CIS Azure 4.8 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per i server SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Controlla il provisioning di un amministratore di Azure Active Directory per il server SQL per abilitare l'autenticazione di Azure AD. L'autenticazione di Azure AD consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità di utenti di database e di altri servizi Microsoft |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Assicurarsi che "Crittografia dati" sia impostato su "Attivato" in un database SQL

**ID**: CIS Azure 4.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare Transparent Data Encryption nei database SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti relativi alla conformità |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Assicurarsi che la protezione TDE di SQL Server sia crittografata con BYOK (Bring Your Own Key)

**ID**: CIS Azure 4.10 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[TDE Protector per l'istanza gestita di SQL deve essere crittografato con la chiave personale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Transparent Data Encryption con il supporto della chiave personale offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[TDE Protector per il server SQL deve essere crittografato con la chiave personale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Transparent Data Encryption con il supporto della chiave personale offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Assicurarsi che il criterio "Imponi connessione SSL" sia abilitato per il server di database MySQL

**ID**: CIS Azure 4.11 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Database di Azure per il server MySQL supporta la connessione alle applicazioni client tramite Secure Sockets Layer (SSL). L'imposizione di connessioni SSL tra il server di database e le applicazioni client garantisce la protezione da attacchi 'man in the middle' tramite la crittografia del flusso di dati tra il server e l'applicazione. Questa configurazione impone che SSL sia sempre abilitato per l'accesso al server di database. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "log_checkpoints" sia impostato su "Sì" per il server di database PostgreSQL

**ID**: CIS Azure 4.12 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'impostazione di registrazione dei punti di controllo deve essere abilitata per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Questo criterio consente di controllare i database PostgreSQL nell'ambiente corrente in cui l'impostazione log_checkpoints non è abilitata. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Assicurarsi che il criterio "Imponi connessione SSL" sia abilitato per il server di database PostgreSQL

**ID**: CIS Azure 4.13 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Il database di Azure per PostgreSQL supporta la connessione del server di database di Azure per PostgreSQL alle applicazioni client tramite SSL (Secure Sockets Layer). L'imposizione di connessioni SSL tra il server di database e le applicazioni client garantisce la protezione da attacchi 'man in the middle' tramite la crittografia del flusso di dati tra il server e l'applicazione. Questa configurazione impone che SSL sia sempre abilitato per l'accesso al server di database. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "log_connections" sia impostato su "Sì" per il server di database PostgreSQL

**ID**: CIS Azure 4.14 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'impostazione di registrazione delle connessioni deve essere abilitata per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Questo criterio consente di controllare i database PostgreSQL nell'ambiente corrente in cui l'impostazione log_connections non è abilitata. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "log_disconnections" sia impostato su "Sì" per il server di database PostgreSQL

**ID**: CIS Azure 4.15 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'impostazione di registrazione delle disconnessioni deve essere abilitata per i server di database PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Questo criterio consente di controllare i database PostgreSQL nell'ambiente corrente in cui l'impostazione log_disconnections non è abilitata. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "connection_throttling" sia impostato su "Sì" per il server di database PostgreSQL

**ID**: CIS Azure 4.17 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La limitazione delle connessioni per i server di database PostgreSQL deve essere abilitata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Questo criterio consente di controllare gli eventuali database PostgreSQL per cui non è abilitata la limitazione delle connessioni nell'ambiente corrente. Questa impostazione abilita la limitazione delle connessioni temporanea per indirizzo IP nel caso di un numero eccessivo di errori di accesso con password non valida. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

### <a name="ensure-that-a-log-profile-exists"></a>Assicurarsi che esista un profilo di log

**ID**: CIS Azure 5.1.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le sottoscrizioni di Azure devono avere un profilo di log per il log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Questo criterio verifica se è abilitato un profilo di log per l'esportazione dei log attività. Controlla se non è stato creato un profilo di log per esportare i log in un account di archiviazione o in un hub eventi. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Assicurarsi che la conservazione del log attività sia impostata su un valore di 365 giorni o superiore

**ID**: CIS Azure 5.1.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il log attività deve essere conservato per almeno un anno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Questo criterio controlla il log attività per verificare se la conservazione è impostata o meno su 365 giorni o per sempre (giorni di conservazione impostati su 0). |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Assicurarsi che il profilo di controllo acquisisca tutte le attività

**ID**: CIS Azure 5.1.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il profilo di log di Monitoraggio di Azure deve raccogliere i log per le categorie 'scrittura', 'eliminazione' e 'azione'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Questo criterio garantisce che un profilo di log raccolga i log per le categorie 'scrittura, 'eliminazione' e 'azione' |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Assicurarsi che il profilo di log acquisisca i log attività per tutte le aree, incluse quelle globali

**ID**: CIS Azure 5.1.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitoraggio di Azure deve raccogliere i log attività da tutte le aree](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Questo criterio controlla il profilo del log di Monitoraggio di Azure che non esporta le attività da tutte le aree supportate da Azure, incluse quelle globali. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Assicurarsi che l'account di archiviazione contenente il contenitore con i log attività sia crittografato con BYOK (Bring Your Own Key)

**ID**: CIS Azure 5.1.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'account di archiviazione che include il contenitore con i log attività deve essere crittografato tramite BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Questo criterio verifica se l'account di archiviazione che include il contenitore con i log attività è crittografato tramite BYOK. Il criterio funziona solo se l'account di archiviazione risiede nella stessa sottoscrizione dei log attività per impostazione predefinita. Per altre informazioni sulla crittografia dei dati inattivi in Archiviazione di Azure, vedere [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok).  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Assicurarsi che la registrazione per l'insieme di credenziali delle credenziali di Azure sia abilitata

**ID**: CIS Azure 5.1.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I log di diagnostica in Key Vault devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Assicurarsi che esista un avviso del log attività per l'assegnazione di criteri di creazione

**ID**: CIS Azure 5.2.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni dei criteri specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Questo criterio controlla operazioni dei criteri specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Assicurarsi che esista un avviso del log attività per l'eliminazione del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento di una regola del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Assicurarsi che esista un avviso del log attività per l'eliminazione di una regola del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento di una soluzione di sicurezza

**ID**: CIS Azure 5.2.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni di sicurezza specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Questo criterio controlla operazioni di sicurezza specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Assicurarsi che esista un avviso del log attività per l'eliminazione di una soluzione di sicurezza

**ID**: CIS Azure 5.2.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni di sicurezza specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Questo criterio controlla operazioni di sicurezza specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Assicurarsi che esista un avviso del log attività per la creazione, l'aggiornamento o l'eliminazione di una regola del firewall di SQL Server

**ID**: CIS Azure 5.2.8 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Assicurarsi che esista un avviso del log attività per l'aggiornamento dei criteri di sicurezza

**ID**: CIS Azure 5.2.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni di sicurezza specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Questo criterio controlla operazioni di sicurezza specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Rete

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Assicurarsi che l'accesso RDP sia limitato da Internet

**ID**: CIS Azure 6.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'accesso RDP da Internet deve essere bloccato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Questo criterio controlla le regole di sicurezza di rete che consentono l'accesso RDP da Internet |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Assicurarsi che l'accesso SSH sia limitato da Internet

**ID**: CIS Azure 6.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'accesso SSH da Internet deve essere bloccato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Questo criterio controlla le regole di sicurezza di rete che consentono l'accesso SSH da Internet |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Assicurarsi che Network Watcher sia abilitato

**ID**: CIS Azure 6.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare Network Watcher](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Il monitoraggio a livello di scenario permette di diagnosticare i problemi in una visualizzazione completa a livello di rete. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="ensure-that-os-disk-are-encrypted"></a>Assicurarsi che il "Disco del sistema operativo" sia crittografato

**ID**: CIS Azure 7.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile applicare la crittografia del disco nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Le macchine virtuali per cui non è abilitata la crittografia dei dischi verranno monitorate dal Centro sicurezza di Azure che invierà raccomandazioni. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Assicurarsi che i "Dischi dati" siano crittografati

**ID**: CIS Azure 7.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile applicare la crittografia del disco nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Le macchine virtuali per cui non è abilitata la crittografia dei dischi verranno monitorate dal Centro sicurezza di Azure che invierà raccomandazioni. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Assicurarsi che i "Dischi non collegati" siano crittografati

**ID**: CIS Azure 7.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile crittografare i dischi non collegati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Questo criterio controlla i dischi non collegati in cui non è abilitata la crittografia. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Assicurarsi che siano installate solo le estensioni approvate

**ID**: CIS Azure 7.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Devono essere installate solo le estensioni macchina virtuale approvate](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Questo criterio regolamenta le estensioni macchina virtuale non approvate. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Assicurarsi che vengano applicate le patch più recenti del sistema operativo per tutte le macchine virtuali

**ID**: CIS Azure 7.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli aggiornamenti di sistema devono essere installati nelle macchine](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Assicurarsi che venga installata la protezione endpoint per tutte le Macchine virtuali di Microsoft Azure

**ID**: CIS Azure 7.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |I server in cui non è installato un agente di Endpoint Protection verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Altre considerazioni sulla sicurezza

### <a name="ensure-the-key-vault-is-recoverable"></a>Assicurarsi che l'insieme di credenziali delle chiavi sia recuperabile

**ID**: CIS Azure 8.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nell'insieme di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |L'eliminazione dolosa di un insieme di credenziali delle chiavi può causare la perdita permanente di dati. Un utente malintenzionato interno all'organizzazione potrebbe in teoria essere in grado di accedere agli insiemi di credenziali delle chiavi per eliminarli e rimuoverli definitivamente. La protezione dalla rimozione definitiva consente di rispondere a questi tipi di attacco imponendo un periodo di conservazione obbligatorio per gli insiemi di credenziali delle chiavi eliminati temporaneamente. Nessuno all'interno dell'organizzazione né Microsoft sarà in grado di rimuovere definitivamente gli insiemi di credenziali delle chiavi durante il periodo di conservazione associato all'eliminazione temporanea. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Abilitare il controllo degli accessi in base al ruolo con il servizio Azure Kubernetes

**ID**: CIS Azure 8.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nei servizi Kubernetes deve essere usato il controllo degli accessi in base al ruolo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Per garantire un filtro granulare per le azioni che gli utenti possono eseguire, usare il controllo degli accessi in base al ruolo per gestire le autorizzazioni nei cluster del servizio Kubernetes e configurare i criteri di autorizzazione pertinenti. |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Assicurarsi che l'autenticazione del servizio app sia impostata nel servizio app di Azure

**ID**: CIS Azure 9.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'autenticazione deve essere abilitata nell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |L'autenticazione del Servizio app di Azure è una funzionalità che può impedire alle richieste HTTP anonime di raggiungere l'app per le API o autenticare coloro che dispongono dei token prima che raggiungano l'app per le API |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[L'autenticazione deve essere abilitata nell'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |L'autenticazione del Servizio app di Azure è una funzionalità che può impedire alle richieste HTTP anonime di raggiungere l'app per le funzioni o autenticare coloro che dispongono dei token prima che raggiungano l'app per le funzioni |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[L'autenticazione deve essere abilitata nell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |L'autenticazione del Servizio app di Azure è una funzionalità che può impedire alle richieste HTTP anonime di raggiungere l'app Web o autenticare coloro che dispongono dei token prima che raggiungano l'app Web |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Assicurarsi che l'app Web reindirizzi tutto il traffico HTTP a HTTPS nel servizio app Azure

**ID**: CIS Azure 9.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'applicazione Web deve essere accessibile solo tramite HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |L'uso di HTTPS assicura l'autenticazione di server/servizi e protegge i dati in transito da attacchi di intercettazione a livello rete. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Assicurarsi che l'app Web usi la versione più recente della crittografia TLS

**ID**: CIS Azure 9.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nell'app per le API è necessario usare la versione più recente di TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Esegui l'aggiornamento alla versione più recente di TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Nell'app per le funzioni è necessario usare la versione più recente di TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Esegui l'aggiornamento alla versione più recente di TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Nell'app Web è necessario usare la versione più recente di TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Esegui l'aggiornamento alla versione più recente di TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su "Sì"

**ID**: CIS Azure 9.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app per le API sia impostata su 'Sì'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |I certificati client consentono all'app di richiedere un certificato per le richieste in ingresso. Solo i client con un certificato valido potranno raggiungere l'app. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Assicurarsi che l'opzione 'Certificati client (certificati client in ingresso)' dell'app per le funzioni sia impostata su 'Sì'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |I certificati client consentono all'app di richiedere un certificato per le richieste in ingresso. Solo i client con un certificato valido potranno raggiungere l'app. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su 'Sì'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |I certificati client consentono all'app di richiedere un certificato per le richieste in ingresso. Solo i client con un certificato valido potranno raggiungere l'app. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nel Servizio app

**ID**: CIS Azure 9.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nell'app per le API è necessario usare un'identità gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Usare un'identità gestita per la protezione avanzata dell'autenticazione |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Nell'app per le funzioni è necessario usare un'identità gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Usare un'identità gestita per la protezione avanzata dell'autenticazione |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Nell'app Web è necessario usare un'identità gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Usare un'identità gestita per la protezione avanzata dell'autenticazione |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di PHP" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Periodicamente, per il software PHP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di PHP più recente per le app per le API per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Periodicamente, per il software PHP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di PHP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di Python" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.8 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Periodicamente, per il software Python vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app per le API per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, per il software Python vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app per le funzioni per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, per il software Python vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di Java" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Periodicamente, per Java vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app per le API per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Periodicamente, per il software Java vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Java più recente per le app per le funzioni per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Periodicamente, per il software Java vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Java più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di HTTP" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.10 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Periodicamente, per HTTP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di HTTP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Periodicamente, per HTTP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di HTTP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Periodicamente, per HTTP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di HTTP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> La disponibilità di specifiche definizioni di Criteri di Azure può variare in Azure per enti pubblici e in altri cloud nazionali.

## <a name="next-steps"></a>Passaggi successivi

Articoli aggiuntivi su Criteri di Azure:

- Panoramica della [Conformità con le normative](../concepts/regulatory-compliance.md).
- Vedere la [struttura della definizione dell'iniziativa](../concepts/initiative-definition-structure.md).
- Vedere altri esempi in [Esempi di Criteri di Azure](./index.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
