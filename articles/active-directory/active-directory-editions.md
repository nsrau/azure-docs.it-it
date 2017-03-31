---
title: Edizioni di Azure Active Directory | Documentazione Microsoft
description: Questo articolo illustra le varie edizioni gratuite e a pagamento di Azure Active Directory. Azure Active Directory Basic, Azure Active Directory Premium P1 e Azure Active Directory Premium P2 sono edizioni a pagamento.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: dcaf8939-7633-40a8-bd76-27dedbb6083a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e5a33f58dfb07b82d4acab533d68135d8ed6a446
ms.lasthandoff: 03/28/2017


---
# <a name="azure-active-directory-editions"></a>Edizioni di Azure Active Directory
Tutti i servizi aziendali di Microsoft Online si basano su Azure Active Directory (Azure AD) per le esigenze di gestione delle identità e degli accessi. Sottoscrivendo qualsiasi servizio aziendale di Microsoft Online, ad esempio Office 365 o Microsoft Azure, si ottiene un Azure AD con accesso a tutte le funzionalità gratuite descritte di seguito.  

Azure Active Directory è una soluzione cloud completa di gestione di identità e accessi ad alta disponibilità che riunisce servizi di directory di base, governance delle identità avanzata e gestione dell'accesso alle applicazioni. Azure Active Directory offre inoltre una piattaforma avanzata basata sugli standard che consente agli sviluppatori di fornire il controllo di accesso alle applicazioni in base a regole e criteri centralizzati. Con l'edizione Free di Azure Active Directory, è possibile gestire utenti e gruppi, eseguire la sincronizzazione con le directory locali, disporre dell'accesso Single Sign-On per Azure, Office 365 e numerose applicazioni SaaS note, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e molte altre. Per altre informazioni su Azure Active Directory, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).

Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante Azure Active Directory Basic Edition, Premium P1 Edition e Premium P2 Edition. Le edizioni a pagamento di Azure Active Directory, realizzate a partire dalla directory gratuita esistente, forniscono funzionalità aziendali avanzate che estendono alla forza lavoro mobile servizi come la gestione self-service, il monitoraggio avanzato, la creazione di report di sicurezza, Multi-Factor Authentication (MFA) e l'accesso sicuro.

Le sottoscrizioni di Office 365 includono funzionalità di Azure Active Directory aggiuntive, descritte nella tabella di confronto seguente.

> [!NOTE]
> Per le opzioni relative ai prezzi di queste edizioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Le edizioni Azure Active Directory Premium P1 e Premium P2 e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il forum di Azure Active Directory.
>
>

* **Azure Active Directory Basic** : progettata per gli utenti che svolgono attività basate sul cloud, questa edizione garantisce l'accesso alle applicazioni incentrate su cloud e soluzioni di gestione delle identità self-service. L'edizione Basic di Azure Active Directory offre funzionalità per il miglioramento della produttività e la riduzione dei costi, ad esempio la gestione degli accessi basata sui gruppi, la reimpostazione delle password self-service per le applicazioni cloud e il proxy dell'applicazione di Azure Active Directory (per pubblicare applicazioni Web locali usando Azure Active Directory), supportate da un contratto di servizio a livello aziendale che garantisce un tempo di attività del 99,9%.
* **Azure Active Directory Premium P1** : progettata per le organizzazioni con esigenze più complesse di gestione delle identità e degli accessi, Azure Active Directory Premium Edition include capacità aggiuntive di gestione delle identità a livello aziendale con numerose funzionalità e consente agli utenti ibridi di accedere facilmente a funzionalità locali e cloud. Questa edizione include tutte le funzionalità necessarie per gli Information Worker e gli amministratori di identità in ambienti ibridi, tra cui accesso alle applicazioni, gestione delle identità e degli accessi self-service, protezione e sicurezza dell'identità nel cloud. Supporta risorse avanzate di amministrazione e delega come i gruppi dinamici e la gestione self-service dei gruppi. Include Microsoft Identity Manager, una suite locale di gestione delle identità e degli accessi, e offre soluzioni con funzionalità cloud di writeback come la reimpostazione self-service delle password per gli utenti locali.
* **Azure Active Directory Premium P2** : progettata con protezione avanzata per tutti gli utenti e gli amministratori, questa nuova offerta include tutte le funzionalità di Azure AD Premium P1 e le nuove Identity Protection e Privileged Identity Management. Azure Active Directory Identity Protection sfrutta miliardi di segnali per fornire l'accesso condizionale basato sui rischi per le applicazioni e i dati aziendali critici. Consente anche di gestire e proteggere account privilegiati con Azure Active Directory Privileged Identity Management in modo che sia possibile individuare, limitare e monitorare gli amministratori e il relativo accesso alle risorse e fornire accesso just-in-time quando è necessario.  

Per iscriversi e iniziare a usare subito Active Directory Premium, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).

> [!NOTE]
> Tramite le edizioni con pagamento a consumo sono disponibili diverse funzionalità di Azure Active Directory:
>
> * Active Directory B2C è la soluzione di gestione delle identità e degli accessi per le applicazioni rivolte ai consumatori. Per altre informazioni, vedere la pagina relativa ad [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Il servizio Azure Multi-Factor Authentication può essere usato tramite provider per utente o provider per autenticazione. Per altre informazioni, vedere la pagina relativa ad [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
>
>

## <a name="comparing-generally-available-features"></a>Confronto tra le funzionalità disponibili a livello generale
> [!NOTE]
> Per una visualizzazione diversa dei dati, vedere [Funzionalità di Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).
>
>

**Funzionalità comuni**

* [Oggetti directory](#directory-objects)
* [Gestione (aggiunta, aggiornamento o eliminazione) di utenti o gruppi, provisioning basato sull'utente, registrazione di dispositivi](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)
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

* [Gestione self-service dei gruppi e delle app, aggiunta self-service di applicazioni, gruppi dinamici](#self-service-group)
* [Reimpostazione, modifica o sblocco con writeback in locale delle password in modalità self-service](#self-service-password-resetchangeunlock-with-on-premises-write-back)
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
* [Iscrizione automatica MDM, ripristino Bitlocker self-service, amministratori locali aggiuntivi per dispositivi Windows 10 tramite aggiunta ad Azure AD](#mdm-auto-enrollment)

## <a name="common-features"></a>Funzionalità comuni
#### <a name="directory-objects"></a>Oggetti directory
**Tipo:** funzionalità comuni

La quota di utilizzo predefinita è 150.000 oggetti. Un oggetto è una voce del servizio directory ed è rappresentato dal relativo nome distinto univoco. Un esempio di oggetto è un'immissione utente a scopi di autenticazione. Se è necessario superare questa quota predefinita, contattare il supporto tecnico. Il limite di 500.000 oggetti non si applica a Office 365, Microsoft Intune o ad altri servizi online Microsoft a pagamento che usano Azure Active Directory per i servizi di directory.

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| Fino a 500.000 oggetti |Nessun limite di oggetti |Nessun limite di oggetti |Nessun limite di oggetti per gli account utente di Office 365 |

#### <a name="usergroup-management-addupdatedelete-user-based-provisioning-device-registration"></a>Gestione (aggiunta, aggiornamento o eliminazione), di utenti o gruppi, provisioning basato sull'utente, registrazione di dispositivi
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

**Altre informazioni:**

* [Amministrare la directory di Azure AD](active-directory-administer.md)
* [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)

#### <a name="single-sign-on-sso"></a>Accesso Single Sign-On (SSO)
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| 10 app per utente (1) |10 app per utente (1) |Nessun limite (2) |10 app per utente (1) |

1. Con i livelli Gratuito e Basic di Azure AD, gli utenti finali hanno diritto ad accesso Single Sign-On per un massimo di 10 applicazioni.
2. Integrazione self-service di qualsiasi applicazione che supporta SAML, SCIM o l'autenticazione basata su moduli mediante i modelli forniti nel menu della raccolta di applicazioni. Per altre informazioni, vedere [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md).

**Altre informazioni:**

* [Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)

#### <a name="self-service-password-change-for-cloud-users"></a>Modifica delle password self-service per gli utenti cloud
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

**Altre informazioni:**

* [Come aggiornare la password](active-directory-passwords-update-your-own-password.md#reset-your-password)

#### <a name="connect--sync-engine-that-extends-on-premises-directories-to-azure-active-directory"></a>Connect (motore di sincronizzazione che estende le directory locali ad Azure Active Directory)
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |

**Altre informazioni:**

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

#### <a name="securityusage-reports"></a>Report su sicurezza/utilizzo
**Tipo:** funzionalità comuni

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| 3 report di base |3 report di base |Report avanzati |3 report di base |

**Altre informazioni:**

* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

## <a name="premium-and-basic-features"></a>Funzionalità Premium e Basic
#### <a name="group-based-access-managementprovisioning"></a>Provisioning/gestione dell'accesso basato sul gruppo
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Controllo][12] | ![Controllo][12] | &nbsp; |

**Altre informazioni:**

* [Utilizzare un gruppo per gestire l'accesso alle applicazioni SaaS](active-directory-accessmanagement-group-saasapps.md)

#### <a name="self-service-password-reset-for-cloud-users"></a>Reimpostazione delle password self-service per gli utenti cloud
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Controllo][12] |![Controllo][12] | ![Controllo][12] |

**Altre informazioni:**

* [Reimpostazione della password di Azure AD per utenti e amministratori](active-directory-passwords.md)

#### <a name="company-branding-logon-pagesaccess-panel-customization"></a>Personalizzazione aziendale (pagine di accesso/personalizzazione del panello di accesso)
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Controllo][12] |![Controllo][12] | ![Controllo][12] |

**Altre informazioni:**

* [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

#### <a name="application-proxy"></a>Proxy dell'applicazione
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Controllo][12] | ![Controllo][12] | &nbsp; |

**Altre informazioni:**

* [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md)

#### <a name="sla-999"></a>Contratti di servizio del 99,9%
**Tipo:** funzionalità Basic

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Controllo][12] |![Controllo][12] | ![Controllo][12] |

**Altre informazioni:**

* [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/)

## <a name="premium-features"></a>Funzionalità Premium


#### <a name="self-service-group"></a>Gestione self-service dei gruppi e delle app, aggiunta self-service di applicazioni, gruppi dinamici
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12]| &nbsp; |

#### <a name="self-service-password-resetchangeunlock-with-on-premises-write-back"></a>Reimpostazione, modifica, sblocco con writeback in locale delle password in modalità self-service
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

#### <a name="multi-factor-authentication-cloud-and-on-premises-mfa-server"></a>Multi-Factor Authentication, cloud e in locale (server MFA)
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![Controllo][12] |Limitato al cloud solo per le app di Office 365 |

**Altre informazioni:**

* [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)


#### <a name="mim-cal-mim-server"></a>Licenze CAL MIM e server MIM
I diritti software di Microsoft Identity Manager Server vengono concessi attraverso licenze di Windows Server (qualsiasi edizione). Poiché Microsoft Identity Manager viene eseguito nel sistema operativo Windows Server, purché il server esegua una copia con licenza valida di Windows Server, allora Microsoft Identity Manager può essere installato e utilizzato su tale server. Nessuna altra licenza separata è necessaria per il Server di gestione identità Microsoft.

**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![Controllo][12] | &nbsp; |

#### <a name="cloud-app-discovery"></a>Cloud App Discovery
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

**Altre informazioni:**

* [Ricerca di applicazioni cloud non gestite con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)

#### <a name="azure-ad-connect-health"></a>Azure AD Connect Health
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

**Altre informazioni:**

* [Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](active-directory-aadconnect-health.md)

#### <a name="automatic-password-rollover-for-group-accounts"></a>Rollover automatico delle password per account di gruppo
**Tipo:** funzionalità Premium

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

#### <a name="identity-protection"></a>Identity Protection
**Tipo:** funzionalità Premium

| Edizione gratuita | Edizione Basic | Edizione Premium P2 | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

#### <a name="privileged-identity-management"></a>Privileged Identity Management
**Tipo:** funzionalità Premium

| Edizione gratuita | Edizione Basic | Edizione Premium P2 | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

## <a name="azure-active-directory-join--windows-10-only--related-features"></a>Aggiunta ad Azure Active Directory: solo funzionalità correlate a Windows 10
#### <a name="join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery"></a>Aggiunta di un dispositivo ad Azure AD, Desktop SSO, Microsoft Passport per Azure AD, con ripristino Bitlocker per amministratori
**Tipo:** Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| ![Controllo][12] |![Controllo][12] |![Controllo][12] |![Controllo][12] |


#### <a name="mdm-auto-enrollment"></a>Iscrizione automatica MDM, ripristino Bitlocker self-service, amministratori locali aggiuntivi per dispositivi Windows 10 tramite aggiunta ad Azure AD
**Tipo:** Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

#### <a name="enterprise-state-roaming"></a>Enterprise State Roaming
**Tipo:** Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10

**Disponibilità:**

| Edizione gratuita | Edizione Basic | Edizioni Premium (P1 e P2) | Solo app di Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Controllo][12] | &nbsp; |

**Altre informazioni:**

* [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)

## <a name="azure-ad-preview-features"></a>Funzionalità in anteprima di Azure AD
Oltre alle funzionalità disponibili a livello generale delle edizioni Free, Basic e Premium (P1 e P2), Azure AD offre anche una raccolta di funzionalità in anteprima. È possibile usare le funzionalità in anteprima per scoprire le prossime novità e determinare se queste funzionalità possono essere utili per migliorare il proprio ambiente.

**Funzionalità in anteprima disponibili:**

* [Collaborazione B2B](active-directory-b2b-collaboration-overview.md)
* [Unità amministrative](active-directory-administrative-units-management.md)
* [Integrazione delle applicazioni HR](active-directory-saas-workday-inbound-tutorial.md)
* [Autenticazione basata su certificati in iOS](active-directory-certificate-based-authentication-ios.md)
* [Autenticazione basata su certificati in Android](active-directory-certificate-based-authentication-android.md)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)
* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

