---
title: Edizioni di Azure Active Directory | Microsoft Docs
description: Argomento che illustra la possibilità di scegliere tra edizioni gratuite e a pagamento di Azure Active Directory. Azure Active Directory Basic, Azure Active Directory Premium P1 e Azure Active Directory Premium P2 sono edizioni a pagamento.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: curtand

---
# Edizioni di Azure Active Directory
Tutti i servizi aziendali di Microsoft Online si basano su Azure Active Directory (Azure AD) per le esigenze di gestione delle identità e degli accessi. Sottoscrivendo qualsiasi servizio aziendale di Microsoft Online, ad esempio Office 365 o Microsoft Azure, si ottiene un Azure AD con accesso a tutte le funzionalità gratuite descritte di seguito.

Azure Active Directory è un servizio che offre funzionalità complete di gestione delle identità e degli accessi nel cloud per dipendenti, partner e clienti. Combina servizi directory, governance avanzata delle identità, una piattaforma avanzata basata su standard per gli sviluppatori e gestione degli accessi alle applicazioni per l'applicazione personalizzata o per una delle migliaia di applicazioni preintegrate. Con l'edizione Free di Azure Active Directory, è possibile gestire utenti e gruppi, eseguire la sincronizzazione con le directory locali, disporre dell'accesso Single Sign-On per Azure, Office 365 e numerose applicazioni SaaS note, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e molte altre. Per altre informazioni su Azure Active Directory, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).

Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante Azure Active Directory Basic Edition, Premium P1 Edition e Premium P2 Edition. Le edizioni a pagamento di Azure Active Directory, realizzate a partire dalla directory gratuita esistente, forniscono funzionalità aziendali avanzate che estendono alla forza lavoro mobile servizi come la gestione self-service, il monitoraggio avanzato, la creazione di report di sicurezza, Multi-Factor Authentication (MFA) e l'accesso sicuro.

Le sottoscrizioni di Office 365 includono funzionalità di Azure Active Directory aggiuntive, descritte nella tabella di confronto seguente.

> [!NOTE]
> Per le opzioni relative ai prezzi di queste edizioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Le edizioni Azure Active Directory Premium P1 e Premium P2 e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il forum di Azure Active Directory.
> 
> 

* **Azure Active Directory Basic**: progettata per gli utenti che svolgono attività basate sul cloud, questa edizione garantisce l'accesso alle applicazioni incentrate su cloud e soluzioni di gestione delle identità self-service. L'edizione Basic di Azure Active Directory offre funzionalità per il miglioramento della produttività e la riduzione dei costi, ad esempio la gestione degli accessi basata sui gruppi, la reimpostazione delle password self-service per le applicazioni cloud e il proxy dell'applicazione di Azure Active Directory (per pubblicare applicazioni Web locali usando Azure Active Directory), supportate da un contratto di servizio a livello aziendale che garantisce un tempo di attività del 99,9%.
* **Azure Active Directory Premium P1**: progettata per le organizzazioni con esigenze più complesse di gestione delle identità e degli accessi, Azure Active Directory Premium Edition include capacità aggiuntive di gestione delle identità a livello aziendale con numerose funzionalità e consente agli utenti ibridi di accedere facilmente a funzionalità locali e cloud. Questa edizione include tutte le funzionalità necessarie per gli Information Worker e gli amministratori di identità in ambienti ibridi, tra cui accesso alle applicazioni, gestione delle identità e degli accessi self-service, protezione e sicurezza dell'identità nel cloud. Supporta risorse avanzate di amministrazione e delega come i gruppi dinamici e la gestione self-service dei gruppi. Include Microsoft Identity Manager, una suite locale di gestione delle identità e degli accessi, e offre soluzioni con funzionalità cloud di writeback come la reimpostazione self-service delle password per gli utenti locali.
* **Azure Active Directory Premium P2**: progettata con protezione avanzata per tutti gli utenti e gli amministratori, questa nuova offerta include tutte le funzionalità di Azure AD Premium P1 e le nuove Identity Protection e Privileged Identity Management. Azure Active Directory Identity Protection sfrutta miliardi di segnali per fornire l'accesso condizionale basato sui rischi per le applicazioni e i dati aziendali critici. Consente anche di gestire e proteggere account privilegiati con Azure Active Directory Privileged Identity Management in modo che sia possibile individuare, limitare e monitorare gli amministratori e il relativo accesso alle risorse e fornire accesso just-in-time quando è necessario.

Per iscriversi e iniziare a usare subito Active Directory Premium, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).

> [!NOTE]
> Tramite le edizioni con pagamento a consumo sono disponibili diverse funzionalità di Azure Active Directory:
> 
> * Active Directory B2C è la soluzione di gestione delle identità e degli accessi per le applicazioni rivolte ai consumatori. Per altre informazioni, vedere la pagina relativa ad [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Il servizio Azure Multi-Factor Authentication può essere usato tramite provider per utente o provider per autenticazione. Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
> 
> 

## Confronto tra le funzionalità disponibili a livello generale
> [!NOTE]
> Per una visualizzazione diversa dei dati, vedere [Funzionalità di Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).
> 
> 

**Funzionalità comuni**

* [Oggetti directory](#directory-objects)
* [Gestione di utenti/gruppi (aggiunta/aggiornamento/eliminazione) / Provisioning basato sull'utente, registrazione dei dispositivi](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)
* [Accesso Single Sign-On (SSO)](#single-sign-on-sso)
* [Modifica delle password self-service per gli utenti cloud](#self-service-password-change-for-cloud-users)
* [Connect (motore di sincronizzazione che estende le directory locali ad Azure Active Directory)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)
* [Report sull'utilizzo e la sicurezza](#securityusage-reports)

**Funzionalità Basic**

* [Provisioning/gestione dell'accesso basato sul gruppo](#group-based-access-managementprovisioning)
* [Reimpostazione delle password self-service per gli utenti cloud](#self-service-password-reset-for-cloud-users)
* [Personalizzazione aziendale (pagine di accesso/personalizzazione del panello di accesso)](#company-branding-logon-pagesaccess-panel-customization)
* [Proxy dell'applicazione](#application-proxy)
* [Contratti di servizio del 99,9%](#sla-999)

**Funzionalità Premium P1**

* [Gestione self-service dei gruppi e delle app/aggiunta self-service di applicazioni/gruppi dinamici](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)
* [Reimpostazione, modifica, sblocco con writeback in locale delle password in modalità self-service](#self-service-password-resetchangeunlock-with-on-premises-write-back)
* [Multi-Factor Authentication, cloud e in locale (server MFA)](#multi-factor-authentication-cloud-and-on-premises-mfa-server)
* [Licenze CAL MIM e server MIM](#mim-cal-mim-server)
* [Cloud App Discovery](#cloud-app-discovery)
* [Connect Health](#connect-health)
* [Rollover automatico delle password per account di gruppo](#automatic-password-rollover-for-group-accounts)

**Funzionalità Premium P2**

* [Identity Protection](active-directory-identityprotection.md)
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

**Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10**

* [Aggiunta di un dispositivo ad Azure AD, Desktop SSO, Microsoft Passport per Azure AD, con ripristino Bitlocker per amministratori](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)
* [Iscrizione automatica MDM, ripristino Bitlocker self-service, amministratori locali aggiuntivi per dispositivi Windows 10 tramite aggiunta ad Azure AD](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)

## Funzionalità comuni
#### Oggetti directory
**Tipo:** funzionalità comuni

La quota di utilizzo predefinita è 150.000 oggetti. Un oggetto è una voce del servizio directory ed è rappresentato dal relativo nome distinto univoco. Un esempio di oggetto è un'immissione utente a scopi di autenticazione. Se è necessario superare questa quota predefinita, contattare il supporto tecnico. Il limite di 500.000 oggetti non si applica a Office 365, Microsoft Intune o ad altri servizi online Microsoft a pagamento che usano Azure Active Directory per i servizi di directory.

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| Fino a 500.000 oggetti |Nessun limite di oggetti |Nessun limite di oggetti |Nessun limite di oggetti per gli account utente di Office 365 |

#### Gestione di utenti/gruppi (aggiunta/aggiornamento/eliminazione) / Provisioning basato sull'utente, registrazione dei dispositivi
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

**Altre informazioni:**

* [Amministrare la directory di Azure AD](active-directory-administer.md)
* [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)

#### Accesso Single Sign-On (SSO)
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| 10 app per utente (1) |10 app per utente (1) |Nessun limite (2) |10 app per utente (1) |

1. Con Azure AD Free e Azure AD Basic, gli utenti finali a cui è stato assegnato l'accesso alle app SaaS possono visualizzare fino a 10 app nel proprio pannello di accesso e ottenere l'accesso SSO a tali app. Con le sottoscrizioni Free e Basic gli amministratori possono configurare l'accesso SSO e assegnare agli utenti l'accesso a qualsiasi numero di app SaaS, ma gli utenti finali possono vedere un massimo di 10 app alla volta nel proprio pannello di accesso.
2. Integrazione self-service di qualsiasi applicazione che supporta SAML, SCIM o l'autenticazione basata su moduli mediante i modelli forniti nel menu della raccolta di applicazioni. Per altre informazioni, vedere [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md).

**Altre informazioni:**

* [Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)

#### Modifica delle password self-service per gli utenti cloud
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

**Altre informazioni:**

* [Come aggiornare la password](active-directory-passwords-update-your-own-password.md)

#### Connect (motore di sincronizzazione che estende le directory locali ad Azure Active Directory)
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

**Altre informazioni:**

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

#### Report su sicurezza/utilizzo
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| 3 report di base |3 report di base |Report avanzati |3 report di base |

**Altre informazioni:**

* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

## Funzionalità Premium e Basic
#### Provisioning/gestione dell'accesso basato sul gruppo
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] | | |

**Altre informazioni:**

* [Utilizzare un gruppo per gestire l'accesso alle applicazioni SaaS](active-directory-accessmanagement-group-saasapps.md)

#### Reimpostazione delle password self-service per gli utenti cloud
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] | |

**Altre informazioni:**

* [Reimpostazione della password di Azure AD per utenti e amministratori](active-directory-passwords.md)

#### Personalizzazione aziendale (pagine di accesso/personalizzazione del panello di accesso)
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] | |

**Altre informazioni:**

* [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

#### Proxy dell'applicazione
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] | | |

**Altre informazioni:**

* [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md)

#### Contratti di servizio del 99,9%
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] | |

**Altre informazioni:**

* [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/)

## Funzionalità Premium
#### Gestione self-service dei gruppi e delle app/aggiunta self-service di applicazioni/gruppi dinamici
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

#### Reimpostazione, modifica, sblocco con writeback in locale delle password in modalità self-service
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

#### Multi-Factor Authentication, cloud e in locale (server MFA)
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] |Limitato al cloud solo per le app di Office 365 | |

**Altre informazioni:**

* [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)

#### Licenze CAL MIM e server MIM
I diritti software di Microsoft Identity Manager Server vengono concessi attraverso licenze di Windows Server (qualsiasi edizione). Microsoft Identity Manager viene eseguito sul sistema operativo Windows Server. Di conseguenza, purché il server esegua una copia con licenza valida di Windows Server, Microsoft Identity Manager può essere installato e usato su tale server. Nessuna altra licenza separata è necessaria per il Server di gestione identità Microsoft.

**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

#### Cloud App Discovery
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

**Altre informazioni:**

* [Ricerca di applicazioni cloud non gestite con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)

#### Azure AD Connect Health
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

**Altre informazioni:**

* [Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](active-directory-aadconnect-health.md)

#### Rollover automatico delle password per account di gruppo
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

#### Identity Protection
**Tipo:** funzionalità Premium

| Edizione gratuita | Edizione Basic | Edizione Premium P2 | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

#### Privileged Identity Management
**Tipo:** funzionalità Premium

| Edizione gratuita | Edizione Basic | Edizione Premium P2 | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

## Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10
#### Aggiunta di un dispositivo ad Azure AD, Desktop SSO, Microsoft Passport per Azure AD, con ripristino Bitlocker per amministratori
**Tipo:** Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

#### Iscrizione automatica MDM, ripristino Bitlocker self-service, amministratori locali aggiuntivi per dispositivi Windows 10 tramite aggiunta ad Azure AD
**Tipo:** Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

#### Enterprise State Roaming
**Tipo:** Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
|  |![Controllo][12] | | |

**Altre informazioni:**

* [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)

## Funzionalità in anteprima di Azure AD
Oltre alle funzionalità disponibili a livello generale delle edizioni Free, Basic e Premium (P1 e P2), Azure AD offre anche una raccolta di funzionalità in anteprima. È possibile usare le funzionalità in anteprima per scoprire le prossime novità e determinare se queste funzionalità possono essere utili per migliorare il proprio ambiente.

**Funzionalità in anteprima disponibili:**

* [Collaborazione B2B](active-directory-b2b-collaboration-overview.md)
* [Unità amministrative](active-directory-administrative-units-management.md)
* [Integrazione delle applicazioni HR](active-directory-saas-workday-inbound-tutorial.md)
* [Autenticazione basata su certificati in iOS](active-directory-certificate-based-authentication-ios.md)
* [Autenticazione basata su certificati in Android](active-directory-certificate-based-authentication-android.md)

## Passaggi successivi
* [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)
* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0914_2016-->