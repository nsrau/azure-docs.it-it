---
title: Benchmark di sicurezza di Azure V2-gestione delle identità
description: Azure Security benchmark V2 Identity Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2b21bea924ee8eaed37246ce55feaf8243e3e7d4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408738"
---
# <a name="security-control-v2-identity-management"></a>Controllo di sicurezza V2: gestione delle identità

Gestione delle identità riguarda i controlli per stabilire un'identità sicura e i controlli di accesso usando Azure Active Directory. Sono inclusi l'uso di Single Sign-On, le autenticazioni complesse, le identità gestite (e i principi di servizio) per le applicazioni, l'accesso condizionale e il monitoraggio delle anomalie dell'account.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizzare Azure Active Directory come sistema di autenticazione e identità centrale

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) è il servizio di gestione delle identità e degli accessi predefinito di Azure. È necessario standardizzare i Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:
- Risorse cloud Microsoft, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.

- Le risorse dell'organizzazione, ad esempio le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD deve essere una priorità elevata nella procedura di sicurezza del cloud dell'organizzazione. Azure AD fornisce un punteggio sicuro per l'identità che consente di valutare la postura di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare la precisione con cui la configurazione corrisponde ai consigli delle procedure consigliate e per apportare miglioramenti in termini di sicurezza.

Nota: Azure AD supporta provider di identità esterni, che consentono agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con l'identità esterna.

- [Locazione in Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Come creare e configurare un'istanza di Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definire Azure AD tenant](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Usare provider di identità esterni per un'applicazione](../../active-directory/external-identities/identity-providers.md)

- [Qual è il Punteggio di sicurezza identità in Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gestire le identità dell'applicazione in modo sicuro e automatico

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-2 | N/D | AC-2, AC-3, IA-2, IA-4, IA-9 |

Per gli account non umani, ad esempio servizi o automazione, è possibile usare le identità gestite di Azure, anziché creare un account umano più potente per accedere alle risorse o eseguire codice. Le identità gestite di Azure possono eseguire l'autenticazione ai servizi e alle risorse di Azure che supportano l'autenticazione Azure AD. L'autenticazione viene abilitata tramite regole di concessione dell'accesso predefinite, evitando le credenziali hardcoded nel codice sorgente o nei file di configurazione. 

Per i servizi che non supportano le identità gestite, usare Azure AD per creare un'entità servizio con autorizzazioni limitate a livello di risorsa.  Si consiglia di configurare le entità servizio con le credenziali del certificato e di eseguire il fallback ai segreti client. In entrambi i casi, è possibile usare Azure Key Vault insieme alle identità gestite di Azure, in modo che l'ambiente di runtime, ad esempio una funzione di Azure, possa recuperare le credenziali dall'insieme di credenziali delle chiavi.

- [Identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Servizi che supportano le identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Creare un'entità servizio con certificati](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Usare Azure Key Vault per la registrazione dell'entità di sicurezza: Authentication # autorizzi-a-Security-Principal-to-Access-Key-Vault

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usare Azure AD Single Sign-On (SSO) per l'accesso alle applicazioni

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-3 | 4.4 | IA-2, IA-4 |

Azure AD offre la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. La gestione delle identità e dell'accesso si applica alle identità aziendali, ad esempio i dipendenti, nonché a identità esterne come partner, fornitori e fornitori.

Usare Azure AD Single Sign-On (SSO) per gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connetti tutti i tuoi utenti, le tue applicazioni e i tuoi dispositivi a Azure AD per un accesso facile e sicuro e una maggiore visibilità e controllo. 

- [Informazioni sull'accesso Single Sign-on dell'applicazione con Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usare controlli di autenticazione avanzata per tutti gli accessi in base al Azure Active Directory

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD supporta i controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.  
- Multi-factor authentication: abilitare l'autenticazione a più fattori di Azure AD e seguire le indicazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure per la configurazione L'autenticazione a più fattori può essere applicata a tutti gli utenti, selezionare gli utenti o a livello di utente, in base alle condizioni di accesso e ai fattori di rischio. 

- Autenticazione con password: sono disponibili tre opzioni di autenticazione con password: Windows Hello for business, app Microsoft Authenticator e metodi di autenticazione locali come le smart card. 

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

Se l'autenticazione basata su password legacy viene ancora usata per l'autenticazione Azure AD, tenere presente che gli account solo cloud (account utente creati direttamente in Azure) hanno un criterio predefinito per le password di base. E gli account ibridi, ovvero gli account utente che provengono da Active Directory locali, seguono i criteri di password locali. Quando si usa l'autenticazione basata su password, Azure AD fornisce una funzionalità di protezione delle password che impedisce agli utenti di impostare password facili da indovinare. Microsoft fornisce un elenco globale delle password escluse che vengono aggiornate in base ai dati di telemetria e i clienti possono ampliare l'elenco in base alle esigenze, ad esempio personalizzazione, riferimenti culturali e così via. Questa protezione con password può essere usata solo per gli account cloud e ibridi. 

Nota: l'autenticazione basata solo sulle credenziali password è soggetta ai metodi di attacco più diffusi. Per una maggiore sicurezza, usare l'autenticazione avanzata, ad esempio l'autenticazione a più fattori e un criterio di password complessa. Per le applicazioni di terze parti e i servizi del Marketplace che possono avere password predefinite, è necessario modificarli durante la configurazione iniziale del servizio. 

- [Come abilitare MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Introduzione alle opzioni di autenticazione con password per Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD criteri password predefiniti](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminare le password non valide usando la protezione Azure AD password](../../active-directory/authentication/concept-password-ban-bad.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitoraggio e avviso per le anomalie degli account

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD fornisce le origini dati seguenti: 
-   Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

-   Log di controllo: consente la tracciabilità tramite i log per tutte le modifiche apportate tramite diverse funzionalità di Azure AD. Esempi di log di controllo delle modifiche registrate includono l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

-   Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

-   Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Il Centro sicurezza di Azure può anche inviare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti e gli account deprecati nella sottoscrizione. 

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare segnali Active Directory locali per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni Insider dannose.

- [Report delle attività di controllo in Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

- [Avvisi nel modulo di protezione dalle minacce per il Centro sicurezza di Azure](../../security-center/alerts-reference.md)

- [Come integrare i log attività di Azure in monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Connettere i dati da Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: limitare l'accesso alle risorse di Azure in base alle condizioni

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-6 | N/D | AC-2, AC-3 |

Usare Azure AD accesso condizionale per un controllo di accesso più granulare in base alle condizioni definite dall'utente, ad esempio richiedere gli accessi utente da determinati intervalli IP per usare l'autenticazione a più fattori. Una gestione delle sessioni di autenticazione granulare può essere usata anche tramite Azure AD criteri di accesso condizionale per diversi casi d'uso. 

- [Panoramica dell'accesso condizionale di Azure](../../active-directory/conditional-access/overview.md)

- [Criteri comuni di accesso condizionale](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [È possibile configurare la gestione della sessione di autenticazione con l'Accesso condizionale](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione delle identità e delle chiavi](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminare l'esposizione delle credenziali non intenzionali

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-7 | 18,1, 18,7 | IA-5 |

Implementare Azure DevOps Credential scanner per identificare le credenziali all'interno del codice. Credential scanner consiglia inoltre di trasferire le credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault.

Per GitHub, è possibile usare la funzionalità di analisi dei segreti nativi per identificare le credenziali o altre forme di segreti all'interno del codice.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Analisi dei segreti di GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: proteggere l'accesso degli utenti alle applicazioni legacy

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| IM-8 | 14,6 | AC-2, AC-3, SC-11 |

Assicurarsi di disporre di controlli di accesso e monitoraggio della sessione moderni per le applicazioni legacy e i dati archiviati ed elaborati. Mentre le VPN sono comunemente usate per accedere alle applicazioni legacy, spesso hanno solo il controllo di accesso di base e il monitoraggio limitato della sessione.

Azure AD proxy di applicazione consente di pubblicare applicazioni locali legacy per gli utenti remoti con Single Sign-On (SSO) e di convalidare in modo esplicito l'attendibilità di utenti e dispositivi remoti con Azure AD l'accesso condizionale. 

In alternativa, Microsoft Cloud App Security è un servizio CASB (cloud Access Security Broker) che può fornire controlli per il monitoraggio delle sessioni dell'applicazione di un utente e delle azioni di blocco (per le applicazioni locali legacy e le applicazioni SaaS (cloud Software as a Service). 

- [Proxy dell'applicazione di Azure AD](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Procedure consigliate Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Sicurezza delle applicazioni e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)