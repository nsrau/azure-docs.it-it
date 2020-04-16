---
title: Azure Security Control - Identity and Access Control
description: Controllo di sicurezza di Azure E controllo di accesso
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408522"
---
# <a name="security-control-identity-and-access-control"></a>Controllo di sicurezza: controllo di identità e accesso

I suggerimenti per la gestione delle identità e degli accessi si concentrano sulla risoluzione dei problemi relativi al controllo degli accessi basato sull'identità, al blocco dell'accesso amministrativo, agli avvisi relativi agli eventi correlati all'identità, al comportamento anomalo degli account e al controllo degli accessi in base al ruolo.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e sono queryable. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo di directory in Azure AD con PowerShellHow to get a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere membri di un ruolo di directory in Azure AD con PowerShellHow to get members of a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzala la creazione di una password con requisiti di complessità e una lunghezza minima della password, che varia a seconda del servizio. L'utente è responsabile delle applicazioni di terze parti e dei servizi del marketplace che possono utilizzare password predefinite.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.3 | 4.3 | Customer |

Creare procedure operative standard per quanto miri all'utilizzo di account amministrativi dedicati. Usare Gestione identità e accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

È anche possibile abilitare un just-in-time / Just-Enough-Access usando i ruoli privilegiati di Azure AD gestione delle identità per i servizi Microsoft e Azure Resource Manager.You can also enable a Just-In-Time / Just-Enough-Access by using Azure AD Privileged Identity Management Privileged Roles for Microsoft Services, and Azure Resource Manager. 

- [Ulteriori informazioni su Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.4 | 4.4 | Customer |

Ove possibile, usare SSO di Azure Active Directory anziché configurare singole credenziali autonome per servizio. Usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Use Azure Security Center Identity and Access Management recommendations.

- [Comprendere SSO con Azure ADUnderstand SSO with Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Customer |

Abilitare l'autenticazione a più fattori di Azure AD e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Enable Azure AD MFA and follow Azure Security Center Identity and Access Management recommendations.

- [Come abilitare l'autenticazione a più fattori in AzureHow to enable MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di AzureHow to monitor identity and access within Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Customer |

Usare PAW (workstation con accesso privilegiato) con l'autenticazione a più fattori configurata per accedere alle risorse di Azure e configurarle.

- [Informazioni sulle workstation con accesso privilegiato](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare l'autenticazione a più fattori in AzureHow to enable MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare sulle attività sospette da account amministrativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3,7 | 4.8, 4.9 | Customer |

Usare i report di sicurezza di Azure Active Directory per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso alle attività.

- [Come identificare gli utenti di Azure AD contrassegnati per l'attività rischiosaHow to identify Azure AD users flagged for risky activity](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Come monitorare l'identità e l'attività di accesso degli utenti nel Centro sicurezza di AzureHow to monitor users' identity and access activity in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.8 | 11.7 | Customer |

Utilizzare percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare percorsi denominati in AzureHow to configure Named Locations in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Customer |

Usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale. AAD protegge i dati utilizzando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre consente di, gli ishe e l'archiviazione sicura delle credenziali utente.

- [Come creare e configurare un'istanza di AADHow to create and configure an AAD instance](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.10 | 16.9, 16.10 | Customer |

Azure AD fornisce log che consentono di individuare gli account non aggiornati. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo. 

- [Informazioni sui report di Azure ADUnderstand Azure AD reporting](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Come usare le revisioni di Azure Identity AccessHow to use Azure Identity Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accedere alle credenziali disattivate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.11 | 16.12 | Customer |

È possibile accedere alle origini del log attività di accesso, di controllo e di rischio di Azure AD, che consentono l'integrazione con qualsiasi strumento SIEM/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics.You can streamline this process by creating Diagnostic Settings for Azure Active Directory user accounts and sending the audit logs and sign-in logs to a Log Analytics Workspace. È possibile configurare gli avvisi desiderati all'interno dell'area di lavoro di Log Analytics.You can configure desired Alerts within Log Analytics Workspace.

- [Come integrare i log attività di Azure in Monitoraggio di AzureHow to integrate Azure Activity Logs into Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.12 | 16.13 | Customer |

Usare le funzionalità di protezione dei rischi e delle identità di Azure AD per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. È anche possibile inserire dati in Azure Sentinel per ulteriori indagini.

- [Come visualizzare gli accessi rischiosi di Azure ADHow to view Azure AD risky sign-ins](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Come eseguire l'onboarding di Azure SentinelHow to onboard Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 3.13 | 16 | Customer |

Negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox fornisce un'interfaccia da esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

- [Informazioni su Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [protezione dei dati](security-control-data-protection.md)