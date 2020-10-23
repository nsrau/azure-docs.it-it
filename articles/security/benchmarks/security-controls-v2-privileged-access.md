---
title: Benchmark di sicurezza di Azure V2-accesso con privilegi
description: Accesso con privilegi di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d6676187f87cecb7f876150d1582cde9051dd251
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367483"
---
# <a name="security-control-v2-privileged-access"></a>Controllo di sicurezza V2: accesso con privilegi

Accesso con privilegi copre i controlli per proteggere l'accesso con privilegi al tenant e alle risorse di Azure. Questo include una gamma di controlli che consentono di proteggere il modello amministrativo, gli account amministrativi e le workstation con accesso con privilegi da rischi intenzionali e accidentali.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: proteggere e limitare gli utenti con privilegi elevati

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Limitare il numero di account utente con privilegi elevati e proteggere questi account a un livello elevato. I ruoli predefiniti più importanti in Azure AD sono amministratore globale e amministratore del ruolo con privilegi, perché gli utenti assegnati a questi due ruoli possono delegare i ruoli di amministratore. Con questi privilegi, gli utenti possono leggere e modificare direttamente o indirettamente tutte le risorse nell'ambiente Azure:

- Amministratore globale/amministratore della società: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative di Azure AD, nonché ai servizi che usano Azure AD identità.

- Amministratore del ruolo con privilegi: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure AD, oltre che all'interno di Azure AD Privileged Identity Management (PIM). Inoltre, questo ruolo consente la gestione di tutti gli aspetti di PIM e delle unità amministrative.

Nota: è possibile che si disponga di altri ruoli critici che devono essere regolati se si usano ruoli personalizzati con determinate autorizzazioni con privilegi assegnati. Inoltre, è possibile che si desideri applicare controlli simili all'account Administrator degli asset aziendali critici.  

È possibile abilitare l'accesso con privilegi JIT (just-in-Time) alle risorse di Azure e Azure AD usando Azure AD Privileged Identity Management (PIM). JIT concede le autorizzazioni temporanee per eseguire attività con privilegi solo quando gli utenti lo richiedono. PIM può inoltre generare avvisi di sicurezza in caso di attività sospette o non sicure nell'organizzazione Azure AD.

- [Autorizzazioni del ruolo amministratore in Azure AD](../../active-directory/roles/permissions-reference.md)

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../../active-directory/roles/security-planning.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operazioni di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Isolare l'accesso ai sistemi aziendali critici limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione in cui si trovano. Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono dell'accesso amministrativo alle risorse aziendali critiche, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici. 

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente. 

Assicurarsi di assegnare account con privilegi distinti distinti dagli account utente standard usati per le attività di posta elettronica, esplorazione e produttività.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Accesso al gruppo di gestione](../../governance/management-groups/overview.md#management-group-access)

- [Amministratori della sottoscrizione di Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: rivedere e riconciliare regolarmente l'accesso utente

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC-2 |

Esaminare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il relativo livello di accesso siano validi. È possibile utilizzare le verifiche di accesso di Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. È anche possibile usare Azure AD Privileged Identity Management per creare un flusso di lavoro del report di verifica di accesso che facilita il processo di revisione.
Inoltre, è possibile configurare Privileged Identity Management di Azure in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente. 

Nota: alcuni servizi di Azure supportano gli utenti e i ruoli locali che non sono gestiti tramite Azure AD. È necessario gestire questi utenti separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Come usare Azure AD le verifiche di identità e accesso](../../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurare l'accesso di emergenza in Azure AD

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.
È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano mantenute sicure e note solo a singoli utenti autorizzati a utilizzarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../../active-directory/roles/security-emergency-access.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operazioni di sicurezza (secops)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi.
- [Informazioni sulle verifiche di accesso Azure AD](../../active-directory/governance/access-reviews-overview.md) 

- [Informazioni sulla gestione dei diritti Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usare workstation con accesso con privilegi

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

Le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare la configurazione protetta, tra cui l'autenticazione avanzata, le linee di base software e hardware e l'accesso logico e di rete limitato. 

- [Informazioni sulle workstation con accesso con privilegi](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuire una workstation con accesso con privilegi](../../active-directory/devices/howto-azure-managed-workstation.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Operazioni di sicurezza (secops)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguire l'amministrazione sufficiente (principio dei privilegi minimi)

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

Il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, entità servizio e identità gestite di gruppo. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell e la portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati agli elementi richiesti dai ruoli. I privilegi limitati completano l'approccio JIT (just-in-Time) di Azure AD Privileged Identity Management (PIM) e tali privilegi devono essere esaminati periodicamente.
Usare i ruoli predefiniti per allocare l'autorizzazione e creare solo un ruolo personalizzato quando richiesto. 

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md)

- [Come configurare RBAC di Azure](../../role-based-access-control/role-assignments-portal.md)

- [Come usare Azure AD le verifiche di identità e accesso](../../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: scegliere il processo di approvazione per il supporto tecnico Microsoft 

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

Negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox offre la possibilità di esaminare e approvare o rifiutare in modo esplicito ogni richiesta di accesso ai dati del cliente.

- [Informazioni Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

