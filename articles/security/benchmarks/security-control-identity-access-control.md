---
title: Controllo di sicurezza di Azure-identità e controllo di accesso
description: Identità e controllo di accesso del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 9081f74aee7ff503c7fe29cef6ca76e6d6b46a4f
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564270"
---
# <a name="security-control-identity-and-access-control"></a>Controllo di sicurezza: identità e controllo di accesso

I suggerimenti sulla gestione delle identità e dell'accesso si concentrano sull'indirizzamento di problemi correlati al controllo degli accessi in base all'identità, al blocco dell'accesso amministrativo, alla segnalazione di eventi correlati all'identità, al comportamento anomalo degli account e al controllo degli accessi in base

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: gestire un inventario degli account amministrativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Come ottenere un ruolo della directory in Azure AD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD non è il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che varia a seconda del servizio. L'utente è responsabile per le applicazioni di terze parti e per i servizi del Marketplace che possono usare password predefinite.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.3 | 4.3 | Customer |

Creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

È anche possibile abilitare un accesso just-in-time/just-enough usando Azure AD Privileged Identity Management ruoli con privilegi per i servizi Microsoft e Azure Resource Manager. 

Altre informazioni: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usare Single Sign-On (SSO) con Azure Active Directory

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.4 | 4.4 | Customer |

Laddove possibile, utilizzare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Informazioni su SSO con Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory.

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Customer |

Abilitare l'autenticazione a più fattori Azure AD e seguire le indicazioni per la gestione delle identità e degli accessi nel centro

Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare identità e accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.6 | 4,6, 11,6, 12,12 | Customer |

Usare le workstation Paw (Privileged Access workstation) con multi-factor authentication configurato per accedere e configurare le risorse di Azure.

Informazioni sulle workstation con accesso con privilegi: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3,7 | 4,8, 4,9 | Customer |

Usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'attività di identità e accesso.

Come identificare Azure AD utenti contrassegnati per le attività rischiose:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'attività di accesso e identità degli utenti nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.8 | 11,7 | Customer |

Usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare le località denominate in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Customer |

Usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale. AAD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre sale, hash e archivia in modo sicuro le credenziali utente.

Come creare e configurare un'istanza di AAD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.1 | 16,9, 16,10 | Customer |

Azure AD fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/ di Azure AD Reporting

Come usare le verifiche di accesso alle identità di Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.11 | 16,12 | Customer |

È possibile accedere alle origini del registro eventi di Azure AD attività di accesso, controllo e rischio, che consentono di integrarsi con qualsiasi strumento SIEM/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure Active Directory account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

Come integrare i log attività di Azure in monitoraggio di Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.12 | 16,13 | Customer |

Usare Azure AD funzionalità di protezione delle identità e di rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. È anche possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

Come visualizzare Azure AD accessi a rischio:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 3.13 | 16 | Customer |

Negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox fornisce un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati del cliente.

Informazioni Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [protezione dei dati](security-control-data-protection.md)
