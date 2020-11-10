---
title: Controllo di sicurezza di Azure-identità e controllo di accesso
description: Identità e controllo di accesso del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8d2901e71a3c638e25899803ff9b24e20fdf1969
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412664"
---
# <a name="security-control-identity-and-access-control"></a>Controllo di sicurezza: identità e controllo di accesso

I suggerimenti sulla gestione delle identità e dell'accesso si concentrano sull'indirizzamento di problemi correlati al controllo degli accessi in base all'identità, al blocco dell'accesso amministrativo, alla segnalazione di eventi correlati all'identità, al comportamento anomalo degli account e al controllo degli accessi in base

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD non è il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che varia a seconda del servizio. L'utente è responsabile per le applicazioni di terze parti e per i servizi del Marketplace che possono usare password predefinite.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.3 | 4.3 | Customer |

Creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

È anche possibile abilitare un accesso just-in-time/just-enough usando Azure AD Privileged Identity Management ruoli con privilegi per i servizi Microsoft e Azure Resource Manager. 

- [Altre informazioni su Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.4 | 4.4 | Customer |

Laddove possibile, utilizzare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Informazioni su SSO con Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Customer |

Abilitare l'autenticazione a più fattori Azure AD e seguire le indicazioni per la gestione delle identità e degli accessi nel centro

- [Come abilitare MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Customer |

Usare le workstation Paw (Privileged Access workstation) con multi-factor authentication configurato per accedere e configurare le risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3,7 | 4,8, 4,9 | Customer |

Usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.8 | 11,7 | Customer |

Usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Customer |

Usare Azure Active Directory come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.10 | 16,9, 16,10 | Customer |

Azure AD fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](../../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.11 | 16,12 | Customer |

È possibile accedere alle origini del registro eventi di Azure AD attività di accesso, controllo e rischio, che consentono di integrarsi con qualsiasi strumento SIEM/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.12 | 16,13 | Customer |

Usare Azure AD funzionalità di protezione delle identità e di rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.13 | 16 | Customer |

Negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox fornisce un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati del cliente.

- [Informazioni Customer Lockbox](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [protezione dei dati](security-control-data-protection.md)