---
title: Benchmark di sicurezza di Azure V2-accesso con privilegi
description: Accesso con privilegi di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059343"
---
# <a name="security-control-privileged-access"></a>Controllo di sicurezza: accesso con privilegi

Accesso con privilegi copre i controlli per proteggere l'accesso con privilegi al tenant e alle risorse di Azure. Questo include una gamma di controlli che consentono di proteggere il modello amministrativo, gli account amministrativi e le workstation con accesso con privilegi da rischi intenzionali e accidentali.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: proteggere e limitare gli amministratori globali

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Gli utenti assegnati al ruolo di amministratore globale possono leggere e modificare tutte le impostazioni amministrative nell'organizzazione Azure AD. Limitare il numero di account amministratore globale di Azure a non più di due per ogni sottoscrizione. I ruoli predefiniti più importanti in Azure AD sono amministratore globale e amministratore del ruolo con privilegi perché gli utenti assegnati a questi due ruoli possono delegare i ruoli di amministratore:
- Amministratore globale/amministratore della società: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative di Azure AD, nonché ai servizi che usano Azure AD identità.

- Amministratore del ruolo con privilegi: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure AD, oltre che all'interno di Azure AD Privileged Identity Management (PIM). Inoltre, questo ruolo consente la gestione di tutti gli aspetti di PIM e delle unità amministrative.

Nota: è possibile che si disponga di altri ruoli critici che devono essere regolati se si usano ruoli personalizzati con determinate autorizzazioni con privilegi assegnati. Inoltre, è possibile che si desideri applicare controlli simili all'account Administrator degli asset aziendali critici.  

È possibile abilitare l'accesso con privilegi JIT (just-in-Time) alle risorse di Azure e Azure AD usando Azure AD Privileged Identity Management (PIM). JIT concede le autorizzazioni temporanee per eseguire attività con privilegi solo quando gli utenti lo richiedono. PIM può inoltre generare avvisi di sicurezza in caso di attività sospette o non sicure nell'organizzazione Azure AD.

Nota: alcuni servizi di Azure come Azure SQL supportano l'autenticazione utente locale oltre all'autenticazione Azure AD. Questo tipo di autenticazione utente locale non è gestito tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

- [Autorizzazioni del ruolo amministratore in Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md)

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: rivedere e riconciliare regolarmente l'accesso utente

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Esaminare gli account utente e accedere regolarmente ai diritti per assicurarsi che gli account utente e i relativi accessi siano validi. 

Usare Azure AD le verifiche di identità e accesso per gestire le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

Per gli utenti amministratori a livello di servizio e di carico di lavoro di Azure, è necessario effettuare una verifica più frequente degli utenti e degli accessi. È anche possibile usare gli avvisi di sicurezza di Azure Privileged Identity Management per rilevare quando vengono creati gli account amministratore e per trovare gli account amministratore non aggiornati o non configurati correttamente. 

Nota: alcuni servizi di Azure come Azure SQL supportano gli utenti locali che non sono gestiti tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso](../../security-center/security-center-identity-access.md)

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Allinea le responsabilità amministrative tra i team](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare Azure AD le verifiche di identità e accesso](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management-verificare l'accesso ai ruoli Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Centro sicurezza di Azure-monitorare identità e accesso](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità alla sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: configurare un account di accesso di emergenza in Azure AD

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano mantenute sicure e note solo a singoli utenti autorizzati a utilizzarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità alla sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Centro operazioni di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: automatizzare il flusso di lavoro delle richieste di accesso e identità di Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-4 | N/D | AC-2, AC-5, PM-10 |

Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso di Azure, incluse le assegnazioni di accesso, le recensioni e la scadenza. È supportata anche l'approvazione con due o più fasi.  

- [Informazioni sulla gestione dei diritti Azure AD](../../active-directory/governance/entitlement-management-overview.md)

- [Come configurare la richiesta di accesso e il processo di approvazione](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità alla sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: utilizzare computer altamente protetti per le attività amministrative

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative:
- Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare la configurazione sicura, tra cui l'autenticazione avanzata, le linee di base software e hardware, l'accesso logico e di rete limitato. 

- Usare la funzionalità Azure Bastion per un percorso sicuro per accedere alle macchine virtuali tramite RDP o SSH. Azure Bastion è un servizio PaaS completamente gestito di cui è possibile eseguire il provisioning per ogni rete virtuale senza creare una nuova rete virtuale. 

- [Informazioni sulle workstation sicure gestite da Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuire una workstation protetta gestita da Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Usare l'host Bastion di Azure](../../bastion/bastion-create-host-portal.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Centro operazioni di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: assegnare privilegi alle risorse usando il controllo degli accessi in base al ruolo

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

Il controllo degli accessi in base al ruolo (RBAC) di Azure consente di gestire i privilegi per l'accesso alle risorse di Azure tramite le assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure. 

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md)

- [Come configurare RBAC in Azure](../../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità alla sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: scegliere il processo di approvazione per il supporto tecnico Microsoft

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox offre la possibilità di esaminare e approvare o rifiutare in modo esplicito le richieste di accesso ai dati dei clienti.

- [Informazioni Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità alla sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identità e chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

