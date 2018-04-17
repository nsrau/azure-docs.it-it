---
title: Novità Note sulla versione per Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory (Azure AD), come le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9a947de3ad821d4cd65e579357e3c779e8623435
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory


> Per restare sempre aggiornati sulle novità di Azure Active Directory (Azure AD), sottoscrivere il [![feed](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [RSS](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

-   Versioni più recenti
-   Problemi noti
-   Correzioni di bug
-   Funzionalità deprecate
-   Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente.

## <a name="march-2018"></a>Marzo 2018
 

### <a name="certificate-expire-notification"></a>Notifica della scadenza dei certificati

**Tipo:** Correzione  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Azure AD invia una notifica quando un certificato per un'applicazione inclusa o non inclusa nella raccolta sta per scadere. 

Alcuni utenti non ricevevano notifiche per le applicazioni aziendali configurate per l'accesso Single Sign-On basato su SAML. Questo problema è stato risolto. Azure AD invia una notifica per i certificati in scadenza entro 7, 30 e 60 giorni. È possibile verificare questo evento nei log di controllo. 

Per altre informazioni, vedere:

- [Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Report delle attività di controllo nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

 
---
 

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provider di identità Twitter e GitHub in Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
È ora possibile aggiungere Twitter o GitHub come provider di identità in Azure AD B2C. Twitter passerà dall'anteprima pubblica alla disponibilità generale. GitHub verrà rilasciato in anteprima pubblica.


Per altre informazioni, vedere [Informazioni su Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---


### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Limitare l'accesso al browser usando Intune Managed Browser con l'accesso condizionale basato su applicazioni di Azure AD per iOS e Android

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

**Ora in anteprima pubblica.**

**SSO di Intune Managed Browser:** i dipendenti possono usare Single Sign-On nei client nativi (ad esempio, Microsoft Outlook) e in Intune Managed Browser per tutte le app connesse ad Azure AD.

**Supporto dell'accesso condizionale di Intune Managed Browser:** è ora possibile fare in modo che i dipendenti usino Intune Managed Browser con i criteri di accesso condizionale basato su applicazioni.

Per altre informazioni, vedere il [post di blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Per altre informazioni, vedere:

- [Configurare l'accesso condizionale basato sulle applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

- [Configurare i criteri di Managed Browser](https://aka.ms/managedbrowser)  



---
 

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlet di Application Proxy nel modulo di PowerShell di disponibilità generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Nel modulo di PowerShell di disponibilità generale è ora incluso il supporto per i cmdlet di Application Proxy. Si noti che per usufruire di questo supporto è necessario mantenere aggiornati i moduli di PowerShell. Se si ritarda di oltre un anno, alcuni cmdlet potrebbero smettere di funzionare. 


Per altre informazioni, vedere [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>I client nativi di Office 365 includono il supporto per l'accesso SSO facile tramite un protocollo non interattivo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli utenti dei client nativi di Office 365 (versione 16.0.8730.xxxx e successive) possono usufruire di un'esperienza di accesso non interattiva usando l'accesso SSO facile. Questo supporto viene fornito aggiungendo un protocollo non interattivo (WS-Trust) ad Azure AD.

Per altre informazioni, vedere [Funzionamento dell'accesso in un client nativo con accesso SSO facile](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work).

 
---
 

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenanted-endpoints"></a>È disponibile un'esperienza di accesso non interattiva, con accesso SSO facile, se un'applicazione invia le richieste di accesso agli endpoint con tenant di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli utenti possono usufruire di un'esperienza di accesso non interattiva, con accesso SSO facile, se un'applicazione, ad esempio `https://contoso.sharepoint.com`, invia le richieste di accesso agli endpoint con tenant di Azure AD, ovvero `https://login.microsoftonline.com/contoso.com/<..>` o `https://login.microsoftonline.com/<tenant_ID>/<..>`, anziché all'endpoint comune di Azure AD (`https://login.microsoftonline.com/common/<...>`).

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Per implementare l'accesso SSO facile è necessario aggiungere un solo URL di Azure AD, anziché due URL come in precedenza, alle impostazioni dell'area Intranet degli utenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Per consentire agli utenti di usufruire dell'accesso SSO facile, è necessario aggiungere un solo URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory: `https://autologon.microsoftazuread-sso.com`. In precedenza era necessario aggiungere due URL.

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di marzo 2018, alla raccolta di app sono state aggiunte le 15 nuove app seguenti con supporto per la federazione:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
La documentazione relativa a tutte le applicazioni è disponibile a questo indirizzo: [https://aka.ms/appstutorial](https://aka.ms/appstutorial)


 
---
 

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM per Risorse di Azure è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Se si usa Azure AD Privileged Identity Management (PIM) per i ruoli della directory, è ora possibile eseguire le relative funzionalità di assegnazione e accesso con vincoli di tempo per i ruoli di Risorse di Azure, ad esempio Sottoscrizioni, Gruppi di risorse, Macchine virtuali e qualsiasi altra risorsa supportata da Azure Resource Manager. Applicare Multi-Factor Authentication quando si attivano i ruoli in modalità Just-In-Time e si pianificano le attivazioni in base agli intervalli di modifica approvati. Inoltre, questa versione introduce miglioramenti non disponibili nell'anteprima pubblica, tra cui un'interfaccia utente aggiornata, i flussi di lavoro di approvazione e la possibilità di estendere i ruoli prossimi alla scadenza e rinnovare quelli scaduti.

Per altre informazioni, vedere [PIM per Risorse di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).
 
---
 

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Aggiunta di attestazioni facoltative ai token delle app (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Un'app di Azure AD può ora richiedere attestazioni personalizzate o facoltative nei token JWT e SAML.  Si tratta di attestazioni relative all'utente o al tenant che non sono incluse per impostazione predefinita nel token a causa di vincoli di dimensione o applicabilità.  Questa funzionalità è attualmente in anteprima pubblica per le app di Azure AD negli endpoint 1.0 e 2.0.  Vedere la documentazione per informazioni su quali attestazioni possono essere aggiunte e su come modificare il manifesto dell'applicazione per richiederle.  

Per altre informazioni, vedere [Attestazioni facoltative in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD supporta PKCE per flussi OAuth più sicuri

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
La documentazione di Azure AD è stata aggiornata in modo da mettere in evidenza il supporto per PKCE, che consente comunicazioni più sicure durante il flusso di concessione del codice di autorizzazione OAuth 2.0.  Negli endpoint 1.0 e 2.0 sono supportati entrambi i valori di code_challenge S256 e plaintext. 

Per altre informazioni, vedere [Richiedere un codice di autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 

 
---
 

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Supporto per il provisioning di tutti i valori degli attributi utente disponibili nell'API Get_Workers di Workday

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
L'anteprima pubblica del provisioning in ingresso da Workday ad Active Directory e Azure AD offre ora la possibilità di estrarre tutti i valori degli attributi disponibili nell'API Get_Workers di Workday ed effettuarne il provisioning. Ciò include il supporto per centinaia di attributi standard e personalizzati, oltre a quelli forniti con la versione iniziale del connettore di provisioning in ingresso di Workday.

Per altre informazioni, vedere [Personalizzazione dell'elenco di attributi utente di Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes).

---



### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Modifica dell'appartenenza ai gruppi da dinamica a statica e viceversa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 
È possibile modificare il modo in cui viene gestita l'appartenenza in un gruppo. Ciò è utile per mantenere lo stesso ID e nome di gruppo nel sistema in modo che i riferimenti al gruppo esistenti restino validi. Creando un nuovo gruppo sarebbe infatti necessario aggiornare tali riferimenti.
L'interfaccia di amministrazione di Azure AD è stata aggiornata per aggiungere il supporto per questa funzionalità. I clienti possono ora convertire i gruppi esistenti dall'appartenenza dinamica a quella assegnata e viceversa. Sono ancora disponibili anche i cmdlet di PowerShell esistenti.

Per altre informazioni, vedere [Modifica dell'appartenenza dinamica in statica e viceversa](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa).

 

 
---
 

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Miglioramento del comportamento di disconnessione con accesso SSO facile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
In precedenza, anche se disconnessi in modo esplicito da un'applicazione protetta da Azure AD, gli utenti sarebbero stati riconnessi automaticamente tramite l'accesso SSO facile se avessero provato ad accedere nuovamente a un'applicazione di AD Azure all'interno della rete aziendale dai propri dispositivi aggiunti a un dominio. Con questa modifica, la disconnessione è supportata.  Gli utenti possono pertanto scegliere lo stesso o un altro account di Azure AD per eseguire nuovamente l'accesso anziché essere riconnessi automaticamente tramite l'accesso SSO facile.

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).

 
---
 

### <a name="application-proxy-connector-version-154020-released"></a>Rilascio della versione 1.5.402.0 del connettore di Application Proxy

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Questa versione del connettore è stata gradualmente implementata nel mese di novembre. La nuova versione include le modifiche seguenti:

- Il connettore imposta ora i cookie a livello di dominio anziché a livello di sottodominio. Ciò garantisce un'esperienza di accesso SSO più uniforme, evitando richieste di autenticazione ridondanti.
- È incluso il supporto per le richieste di codifica a blocchi.
- È stato migliorato il monitoraggio dell'integrità del connettore. 
- Sono stati introdotti miglioramenti alla stabilità e correzioni di bug.

Per altre informazioni, vedere [Informazioni sui connettori di Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

 
---
 

 



## <a name="february-2018"></a>Febbraio 2018
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>Esplorazione migliorata per la gestione di utenti e gruppi

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Gestione directory  
**Funzionalità del prodotto:** Directory
 

L'esperienza di esplorazione per la gestione di utenti e gruppi è stata semplificata. Ora è possibile passare dalla panoramica della directory direttamente all'elenco di tutti gli utenti, con un più facile accesso all'elenco degli utenti eliminati. Si può anche passare dalla panoramica della directory direttamente all'elenco di tutti i gruppi, con un più facile accesso alle impostazioni di gestione dei gruppi. Inoltre, dalla pagina di panoramica della directory è possibile cercare un utente, un gruppo, un'applicazione aziendale o una registrazione di app.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilità dei report di controllo e delle attività di accesso in Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 

I report dei log attività di Azure AD sono ora disponibili nelle istanze di Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina). Sono inclusi i log seguenti:

- **Log di attività di accesso**: includono tutti i log sugli accessi associati al tenant.

- **Log di controllo delle password self-service**: includono tutti i log di controllo relativi alla reimpostazione password self-service.

- **Log di controllo della gestione directory**: includono tutti i log di controllo correlati alla gestione delle directory, come Gestione utenti, Gestione app e altri.

Questi log permettono di ottenere informazioni approfondite sullo stato dell'ambiente. I dati forniti consentono di:

- Determinare le modalità di utilizzo di app e servizi da parte degli utenti.

- Risolvere problemi che influiscono negativamente sulla produttività degli utenti.

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Usare il ruolo "Lettore report" (ruolo non amministrativo) per visualizzare i report attività di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 

In seguito alla richiesta degli utenti di consentire ai ruoli non amministrativi di accedere ai log attività di Azure AD, ora gli utenti con ruolo "Lettore report" possono accedere ai log attività di accesso e controllo nel portale di Azure o tramite le API Graph. 

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Attestazione EmployeeID disponibile come attributo utente e identificatore utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 

È possibile configurare **EmployeeID** come ID utente e attributo utente per gli utenti membri e i guest B2B nelle applicazioni con accesso basato su SAML dall'interfaccia utente delle applicazioni aziendali.

Per altre informazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestione semplificata delle applicazioni mediante caratteri jolly in Azure AD Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Autenticazione utente
 

Per facilitare la distribuzione delle applicazioni e ridurre il sovraccarico amministrativo, ora è possibile pubblicare le applicazioni usando caratteri jolly. Per pubblicare un'applicazione con caratteri jolly, è possibile seguire il flusso di pubblicazione standard delle applicazioni ma usare un carattere jolly negli URL interni ed esterni.

Per altre informazioni, vedere [Applicazioni con carattere jolly in Azure Active Directory Application Proxy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-wildcard).

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nuovi cmdlet a supporto della configurazione del proxy di applicazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Piattaforma
 

L'ultima versione del modulo di anteprima Azure AD PowerShell contiene nuovi cmdlet che consentono agli utenti di configurare applicazioni proxy di applicazione mediante PowerShell.

I nuovi cmdlet sono: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nuovi cmdlet a supporto della configurazione di gruppi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Piattaforma
 

L'ultima versione del modulo Azure AD PowerShell contiene cmdlet per la gestione dei gruppi in Azure AD. Questi cmdlet erano in precedenza disponibili nel modulo AzureADPreview e ora sono stati aggiunti al modulo AzureAD.

I cmdlet di gestione dei gruppi con disponibilità generale sono: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Disponibilità di una nuova versione di Azure AD Connect

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma
 

Azure AD Connect è lo strumento preferito per sincronizzare i dati tra Azure AD e le origini dati locali, inclusi Windows Server Active Directory e LDAP.

**Importante**
 
Questa build introduce modifiche alle regole dello schema e di sincronizzazione. Il servizio di sincronizzazione Azure AD Connect attiva i passaggi di importazione e sincronizzazione complete dopo un aggiornamento. Per informazioni su come modificare questo comportamento, vedere [Come rinviare la sincronizzazione completa dopo l'aggiornamento](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Questa versione contiene gli aggiornamenti e le modifiche seguenti:

**Problemi risolti**

- Correzione della finestra temporale nelle attività in background per la pagina Filtro partizioni quando si passa alla pagina successiva.
- Correzione di un bug che provocava la violazione di accesso durante l'azione personalizzata ConfigDB.
- Correzione di un bug per il ripristino dal timeout della connessione SQL.
- Correzione di un bug in cui i certificati con caratteri jolly SAN non superavano la verifica preliminare.
- Correzione di un bug che provocava l'arresto anomalo di miiserver.exe durante l'esportazione del connettore AAD.
- Correzione di un bug a causa del quale i tentativi di accesso con password non valide venivano registrati nel controller di dominio durante l'esecuzione della procedura guidata di Azure AD Connect per la modifica della configurazione.

**Miglioramenti e nuove funzionalità**

- In base al Regolamento generale sulla protezione dei dati (GDPR), è necessario indicare i tipi di dati dei clienti che vengono condivisi con Microsoft (dati di telemetria, integrità e così via), avere collegamenti a una documentazione online dettagliata e consentire ai clienti di modificare le preferenze.  Questa versione aggiunge le funzionalità seguenti:
    - Informativa sulla condivisione dei dati e sulla privacy nella pagina delle condizioni di licenza per l'installazione pulita.

    - Informativa sulla condivisione dei dati e sulla privacy nella pagina dell'aggiornamento.

    - Nuova attività **Impostazioni privacy** in cui l'utente può modificare le proprie preferenze.
 
- Dati di telemetria dell'applicazione: gli amministratori possono attivare o disattivare questa classe di dati.

- Dati di integrità di Azure AD: gli amministratori devono visitare il portale dei dati di integrità per gestire le impostazioni di integrità. Dopo aver modificato i criteri del servizio, gli agenti li leggeranno e applicheranno.

- Aggiunta di azioni di configurazione per il writeback dei dispositivi e indicatore di stato per l'inizializzazione della pagina.

- Miglioramento della diagnostica generale con report HTML e raccolta completa di dati in un report in formato ZIP di testo/HTML.

- Migliore affidabilità dell'aggiornamento automatico e aggiunta di ulteriori dati di telemetria per assicurare che sia possibile determinare l'integrità del server.

- Limitazione delle autorizzazioni disponibili per gli account con privilegi sull'account AD Connect. Per le nuove installazioni, la procedura guidata limita le autorizzazioni degli account con privilegi sull'account MSOL dopo la creazione dell'account MSOL stesso. Le modifiche interessano le installazioni rapide e le installazioni personalizzate con account a creazione automatica.

- Modifica del programma di installazione in modo da non richiedere il privilegio SA per l'installazione pulita di Azure AD Connect.

- Nuova utilità per risolvere i problemi di sincronizzazione per un oggetto specifico. L'utilità esegue attualmente le verifiche seguenti:

    - Mancata corrispondenza di UserPrincipalName tra l'oggetto utente sincronizzato e l'account utente nel tenant Azure AD.
  
    - Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri di dominio
  
    - Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri dell'unità organizzativa

- Nuova utilità per la sincronizzazione dell'hash delle password corrente archiviato nell'istanza locale di Active Directory per un account utente specifico. L'utilità non richiede la modifica delle password. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aggiunta di applicazioni che supportano i criteri Protezione app di Intune per l'uso con l'accesso condizionale basato sull'applicazione di Azure AD.

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Sono state aggiunte altre applicazioni che supportano l'accesso condizionale basato sull'applicazione. Ora è possibile accedere a Office 365 e ad altre app cloud connesse ad Azure AD usando queste app client approvate.

Le applicazioni seguenti verranno aggiunte entro le fine di febbraio: 

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Per altre informazioni, vedere:

- [Requisiti per le app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>Aggiornamento delle Condizioni per l'utilizzo per l'esperienza per i dispositivi mobili 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance
 

Quando vengono visualizzate le Condizioni per l'utilizzo, ora è possibile fare clic su **In caso di problemi di visualizzazione, fare clic qui**. Questo collegamento consente di aprire le Condizioni per l'utilizzo in modo nativo sul dispositivo. Indipendentemente dalle dimensioni del carattere del documento o dalle dimensioni dello schermo del dispositivo, è possibile ingrandire la visualizzazione finché non si riesce a leggere il documento. 
 

---
 
## <a name="january-2018"></a>Gennaio 2018
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 

Nel mese di gennaio 2018, alla raccolta di app sono state aggiunte le nuove app seguenti con supporto per la federazione:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [directory federata IriusRisk](https://go.microsoft.com/fwlink/?linkid=864699) e [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Per una panoramica completa di tutte le esercitazioni disponibili, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Rilevato accesso con rischi aggiuntivi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Le informazioni ottenute per un evento di rischio rilevato sono associate alla sottoscrizione di Azure AD in corso. Con l'edizione Azure AD Premium P2, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

Con l'edizione Azure AD Premium P1, i rilevamenti non previsti dalla licenza in uso vengono visualizzati come evento di rischio di tipo "Rilevato accesso con rischi aggiuntivi".

Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Possibilità di nascondere le applicazioni di Office 365 dai pannelli di accesso dell'utente finale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO
 

Ora è possibile gestire in maniera più efficiente la modalità di visualizzazione delle applicazioni di Office 365 nei pannelli di accesso dell'utente tramite una nuova impostazione utente. Questa opzione è utile per ridurre la quantità di app nei pannelli di accesso di un utente se si preferisce visualizzare solo le app di Office nel portale di Office. L'impostazione è disponibile in **Impostazioni utente** ed è denominata **Gli utenti possono visualizzare solo le app di Office 365 nel portale di Office 365**.
 

Per altre informazioni, vedere [Nascondere un'applicazione dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Accesso trasparente alle app abilitato per l'accesso SSO basato su password direttamente dall'URL dell'app 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO
 

L'estensione del browser App personali è ora disponibile tramite un utile strumento che mette a disposizione la funzionalità Single-Sign On come collegamento nel browser. Dopo l'installazione, nel browser comparirà un'icona di avvio delle app che assicurerà un accesso rapido alle app. Gli utenti possono ora sfruttare i vantaggi seguenti:

- Possibilità di accedere direttamente ad app con accesso SSO basato su password dalla pagina di accesso dell'app
- Avvio delle app tramite la funzionalità di ricerca rapida
- Collegamenti alle app usate di recente dall'estensione
- L'estensione è disponibile per Microsoft Edge, Chrome e Firefox.
 
Per altre informazioni, vedere [Estensione per l'accesso sicuro alle app personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Esperienza di amministrazione di Azure AD nel portale di Azure classico ritirata

**Tipo:** Deprecato   
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Directory
 

A partire dall'8 gennaio 2018, l'esperienza di amministrazione di Azure AD nel portale di Azure classico è stata ritirata. Questo ritiro ha avuto luogo in concomitanza con quello del portale di Azure classico. In futuro, è consigliabile usare il [centro di amministrazione di Azure AD](https://aad.portal.azure.com) per tutte le operazioni di amministrazione basate sul portale di Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Il portale Web di PhoneFactor è stato ritirato

**Tipo:** Deprecato  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Directory
 

A partire dall'8 gennaio 2018, il portale Web di PhoneFactor è stato ritirato. Questo portale è stato usato per l'amministrazione del server MFA, ma tali funzionalità sono state spostate nel portale di Azure all'indirizzo portal.azure.com. 

La configurazione di MFA è accessibile da **Azure Active Directory \> MFA Server**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati


**Tipo:** Deprecato  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  


Considerando la disponibilità generale della nuova console di amministrazione di Azure Active Directory e delle nuove API ora disponibili per i report sulle attività e sulla sicurezza, le API di report nell'endpoint "/reports" sono state ritirate a partire dal 31 dicembre 2017.


**Elementi disponibili**

Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API fornisce funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa agli eventi di rischio di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.

Per altre informazioni, vedere:

- [Get started with the Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) (Introduzione all'API di creazione report di Azure Active Directory)

- [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>Dicembre 2017
 

### <a name="terms-of-use-in-the-access-panel"></a>Condizioni per l'utilizzo nel riquadro di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità
 
È ora possibile passare al riquadro di accesso e visualizzare le condizioni per l'utilizzo che sono state precedentemente accettate.

A tale scopo, seguire questa procedura:

1. Aprire il [portale App personali](https://myapps.microsoft.com) ed eseguire l'accesso.

2. Nell'angolo in alto a destra selezionare il nome e quindi scegliere **Profilo** dall'elenco. 

3. In **Profilo** selezionare **Verificare le condizioni d'uso**. 

4. È ora possibile verificare le condizioni per l'utilizzo accettate. 

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nuova esperienza di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Autenticazione utente
 
Azure AD e le interfacce utente del sistema di identità dell'account Microsoft sono stati riprogettati in modo da avere un aspetto uniforme e coerente. Inoltre, nella pagina di accesso di Azure AD viene visualizzato prima di tutto il nome utente, seguito dalle credenziali in una seconda schermata.

Per altre informazioni, vedere [The new Azure AD sign-in experience is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (La nuova esperienza di accesso di AD Azure è ora in anteprima pubblica).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Meno richieste di accesso: nuova esperienza di tipo "Mantieni l'accesso" per l'accesso ad Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Autenticazione utente
 
La casella di controllo **Mantieni l'accesso** nella pagina di accesso di Azure AD è stata sostituita con un nuovo prompt che viene visualizzato dopo la corretta esecuzione dell'autenticazione. 

Se risponde **Sì** a questo prompt, il servizio offre un token di aggiornamento permanente. Questo comportamento è analogo a quello che succedeva selezionando la casella di controllo **Mantieni l'accesso** nell'esperienza precedente. Per i tenant federati, questo prompt viene visualizzato dopo la corretta autenticazione con il servizio federato.

Per altre informazioni, vedere [Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Meno prompt di accesso: la nuova esperienza di tipo "Mantieni l'accesso" per Azure AD è in anteprima). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Aggiunta configurazione per richiedere l'espansione delle condizioni per l'utilizzo prima dell'accettazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità
 
Un'opzione per gli amministratori richiede che gli utenti espandano le condizioni per l'utilizzo prima di accettarle.

Selezionare **Sì** o **No** per richiedere che gli utenti espandano le condizioni per l'utilizzo. L'impostazione **Sì** richiede che gli utenti visualizzino le condizioni per l'utilizzo prima di accettarle.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Attivazione con ambito per le assegnazioni di ruolo idonee

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
È possibile usare l'attivazione con ambito per attivare le assegnazioni di ruolo delle risorse di Azure idonee con meno autonomia rispetto alle impostazioni predefinite dell'assegnazione originale. Un esempio è se all'utente viene assegnato il ruolo di proprietario di una sottoscrizione nel tenant. Con l'attivazione con ambito, è possibile attivare il ruolo di proprietario per un massimo di cinque risorse contenute all'interno della sottoscrizione (ad esempio gruppi di risorse e macchine virtuali). La definizione dell'ambito per l'attivazione potrebbe ridurre la possibilità di apportare modifiche indesiderate alle risorse di Azure critiche.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (Che cos'è Azure AD Privileged Identity Management?).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nuove app federate nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di dicembre 2017, alla raccolta di app sono state aggiunte le nuove app seguenti con supporto per la federazione:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integrazione di Azure AD con MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integrazione di Azure AD con Zenegy.

Per una panoramica completa di tutte le esercitazioni disponibili, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flussi di lavoro di approvazione per i ruoli della directory di Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Il flusso di lavoro di approvazione per i ruoli della directory di Azure AD è disponibile a livello generale.

Con il flusso di lavoro di approvazione, gli amministratori dei ruoli con privilegi possono fare in modo che i membri con ruolo idoneo richiedano l'attivazione del ruolo prima di poter usare il ruolo con privilegi. A più utenti e gruppi possono essere delegate responsabilità di approvazione. I membri con ruolo idoneo ricevono una notifica quando l'approvazione è completata e il ruolo è attivo.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticazione pass-through: supporto per Skype for Business

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente


L'autenticazione pass-through supporta ora l'accesso degli utenti alle applicazioni client Skype for Business che supportano l'autenticazione moderna, che include topologie online e ibride. 

Per altre informazioni, vedere [Skype for Business topologies supported with modern authentication](https://technet.microsoft.com/library/mt803262.aspx) (Topologie di Skype for Business supportate con l'autenticazione moderna).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aggiornamenti di Azure AD Privileged Identity Management per Controllo degli accessi in base al ruolo di Azure (anteprima)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Con l'aggiornamento dell'anteprima pubblica di Azure AD Privileged Identity Management (PIM) per Controllo degli accessi in base al ruolo di Azure, è ora possibile:

* Usare Just Enough Administration.
* Richiedere l'approvazione per l'attivazione dei ruoli delle risorse.
* Pianificare l'attivazione futura di un ruolo che richiede l'approvazione per entrambi i ruoli di Azure AD e Controllo degli accessi in base al ruolo di Azure.

 
Per altre informazioni, vedere [Privileged Identity Management for Azure resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure (anteprima)).

 
---
 
## <a name="november-2017"></a>Novembre 2017
 
### <a name="access-control-service-retirement"></a>Ritiro del Servizio di controllo di accesso



**Tipo:** Modifica pianificata  
**Categoria di servizio:** Servizio di controllo di accesso  
**Funzionalità del prodotto:** Servizio di controllo di accesso 


 Controllo di accesso di Azure Active Directory, noto anche come Servizio di controllo di accesso, verrà deprecato alla fine del 2018. Altre informazioni, tra cui una pianificazione dettagliata e istruzioni di carattere generale sulla migrazione, saranno disponibili nelle prossime settimane. È possibile lasciare un commento in questa pagina con eventuali domande sul Servizio di controllo di accesso. Un membro del team sarà a disposizione per rispondere.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Limitazione dell'accesso del browser a Intune Managed Browser 


**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità




È possibile limitare l'accesso del browser a Office 365 e ad altre app cloud connesse ad Azure AD tramite Intune Managed Browser come app approvata. 

È ora possibile configurare la condizione seguente per l'accesso condizionale basato sull'applicazione:

**App client:** Browser

**Qual è l'effetto della modifica?**

Attualmente, l'accesso è bloccato quando si usa questa condizione. Quando l'anteprima sarà disponibile, tutti gli accessi richiederanno l'uso dell'applicazione del browser gestita. 

Cercare questa funzionalità e altre informazioni nelle note sulla versione e nei blog che verranno pubblicati a breve. 

Per altre informazioni, vedere [Conditional access in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) (Accesso condizionale in Azure AD).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

 
**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità




Si prevede di aggiungere le app seguenti all'elenco di [app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Per altre informazioni, vedere:

- [Requisiti per le app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Supporto di più lingue per le condizioni per l'utilizzo



**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità





Gli amministratori possono ora creare nuove condizioni per l'utilizzo contenenti più documenti PDF. È possibile contrassegnare i documenti PDF con una lingua corrispondente. Gli utenti visualizzano il PDF con la lingua corrispondente in base alle proprie preferenze. Se non c'è corrispondenza, viene visualizzata la lingua predefinita.


---
 

### <a name="real-time-password-writeback-client-status"></a>Stato del client relativamente al writeback delle password in tempo reale



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente


 

È ora possibile esaminare lo stato del client relativamente al writeback delle password in locale. Questa opzione è disponibile nella sezione **Integrazione locale** della pagina [Reimpostazione password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Se ci sono problemi con la connessione al client di writeback locale, viene visualizzato un messaggio di errore che specifica:

- Informazioni sui motivi per cui non è possibile connettersi al client di writeback locale.
- un collegamento alla documentazione di supporto alla risoluzione del problema. 


Per altre informazioni, vedere [Integrazione locale](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Accesso condizionale basato su app di Azure AD 



 
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità





È ora possibile limitare l'accesso a Office 365 e ad altre app cloud connesse ad Azure AD alle [app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) che supportano i criteri di protezione app di Intune tramite l'[accesso condizionale basato su app di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). I criteri di protezione app di Intune vengono usati per configurare e proteggere i dati aziendali in queste applicazioni client.

Combinando i criteri di accedo condizionale [basato su app](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) con quelli dell'accesso condizionale [basato su dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) si ottiene la flessibilità necessaria per proteggere i dati dei dispositivi personali e aziendali.

I controlli e le condizioni seguenti sono ora disponibili per l'uso con l'accesso condizionale basato su app:

**Condizione per le piattaforme supportate**

- iOS
- Android

**Condizione per le app client**

- App per dispositivi mobili e client desktop

**Controllo di accesso**

- Richiedere app client approvata


Per altre informazioni, vedere [Accesso condizionale basato su app di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gestione di dispositivi di Azure AD nel portale di Azure



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione e registrazione del dispositivo  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

 



È ora possibile individuare tutti i dispositivi connessi ad Azure AD e le attività correlate al dispositivo in un'unica posizione. È disponibile una nuova esperienza di amministrazione per gestire tutte le impostazioni e le identità dei dispositivi nel portale di Azure. In questa versione è possibile:

- Visualizzare tutti i dispositivi disponibili per l'accesso condizionale in Azure AD.
- Visualizzare le proprietà, inclusi i dispositivi aggiunti ad Azure AD ibridi.
- Trovare le chiavi di BitLocker per i dispositivi aggiunti ad Azure AD, gestire il dispositivo con Intune e altro ancora.
- Gestire le impostazioni relative al dispositivo di Azure AD.

Per altre informazioni, vedere [Manage devices by using the Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal) (Gestire dispositivi tramite il portale di Azure).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Supporto per macOS come piattaforma del dispositivo per l'accesso condizionale ad Azure AD 



**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità 
 

È ora possibile includere o escludere macOS come condizione per la piattaforma del dispositivo nei criteri di accesso condizionale di Azure AD. Con l'aggiunta di macOS alle piattaforme di dispositivi supportati, è possibile:

- **Registrare e gestire i dispositivi macOS con Intune.** Analogamente ad altre piattaforme quali iOS e Android, un'applicazione del portale aziendale è disponibile per macOS per eseguire le registrazioni unificate. È possibile usare la nuova app del portale aziendale per macOS per registrare un dispositivo con Intune e registrarlo con Azure AD.
- **Assicurarsi che i dispositivi macOS siano conformi ai criteri di conformità dell'organizzazione definiti in Intune.** Con Intune nel portale di Azure è ora possibile impostare criteri di conformità per i dispositivi macOS. 
- **Limitare l'accesso alle applicazioni in Azure AD solo ai dispositivi macOS conformi.** La creazione di criteri di accesso condizionale prevede macOS come opzione di piattaforma del dispositivo distinta. È ora possibile creare criteri di accesso condizionale specifici per macOS per il set dell'applicazione di destinazione in Azure.

Per altre informazioni, vedere:

- [Creare criteri di conformità per i dispositivi macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Conditional access in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) (Accesso condizionale in Azure AD)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Estensione di Server dei criteri di rete per Azure Multi-Factor Authentication 


**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Autenticazione a più fattori  
**Funzionalità del prodotto:** Autenticazione utente




L'estensione di Server dei criteri di rete per Azure Multi-Factor Authentication aggiunge funzionalità di autenticazione a più fattori basata sul cloud nell'infrastruttura di autenticazione corrente usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente. Pertanto, non è necessario installare, configurare e gestire nuovi server. 

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni della rete privata virtuale senza distribuire il server Azure Multi-Factor Authentication. L'estensione di Server dei criteri di rete funge da adattatore tra RADIUS e Azure Multi-Factor Authentication basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.


Per altre informazioni, vedere [Integrate your existing Network Policy Server infrastructure with Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension) (Integrare l'infrastruttura di Server dei criteri di rete esistente con Azure Multi-Factor Authentication).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Ripristinare o rimuovere definitivamente gli utenti eliminati


**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Gestione utenti  
**Funzionalità del prodotto:** Directory 



Nel centro di amministrazione di Azure Active Directory è possibile:

- Ripristinare un utente eliminato. 
- Eliminare definitivamente un utente.


**Per provare il servizio:**

1. Nel centro di amministrazione di Azure AD selezionare [Tutti gli utenti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) nella sezione **Gestisci**. 

2. Dall'elenco **Mostra** selezionare **Utenti eliminati di recente**. 

3. Selezionare uno o più utenti eliminati di recente e quindi ripristinarli oppure eliminarli definitivamente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

 
**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità


Le app seguenti sono state aggiunte all'elenco di [app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Per altre informazioni, vedere:

- [Requisiti per le app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usare "OR" tra i controlli in un criterio di accesso condizionale 


**Tipo:** Funzionalità modificata    
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

 
È ora possibile usare "OR", ovvero richiedere uno dei controlli selezionati, per i controlli di accesso condizionale. È possibile usare questa funzionalità per creare criteri usando l'operatore "OR" tra i controlli di accesso. Ad esempio, è possibile usare questa funzionalità per creare un criterio che richiede all'utente di accedere usando l'autenticazione a più fattori o in alternativa (operatore "OR") di essere in un dispositivo conforme.

Per altre informazioni, vedere [Controls in Azure AD conditional access](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls) (Controlli nell'accesso condizionale di Azure AD).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Aggregazione di eventi di rischio in tempo reale


**Tipo:** Funzionalità modificata    
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità


In Azure AD Identity Protection tutti gli eventi di rischio in tempo reale creati dallo stesso indirizzo IP in un determinato giorno vengono ora aggregati per ciascun tipo di evento di rischio. Questa modifica consente di limitare il volume degli eventi di rischio mostrati senza modificare la protezione dell'utente.

Il rilevamento in tempo reale sottostante funziona ogni volta che l'utente effettua l'accesso. Se si impostano criteri di sicurezza relativi al rischio di accesso per l'autenticazione a più fattori o il blocco dell'accesso, verranno attivati per ogni accesso rischioso.

 
---
 




## <a name="october-2017"></a>Ottobre 2017


### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati


**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  



Il portale di Azure offre:

- Una nuova console di amministrazione di Azure AD.
- Nuove API per i report sulle attività e sulla sicurezza.
 
Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint /reports verranno ritirate il 10 dicembre 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Rilevamento automatico dei campi di accesso


**Tipo:** Correzione   
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Single Sign-On  



Azure AD supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password. Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](http://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettere un URL Web e quindi salvare la pagina.
 
A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

### <a name="new-multi-factor-authentication-features"></a>Nuove funzionalità di autenticazione a più fattori


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazione a più fattori  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  



Multi-Factor Authentication (MFA) è fondamentale per proteggere l'organizzazione. Per rendere più adattive le credenziali e semplificare l'esperienza, sono state aggiunte le funzionalità seguenti: 

- I risultati della richiesta a più fattori sono integrati direttamente nel report di accesso di Azure AD, che include l'accesso a livello di codice ai risultati dell'autenticazione a più fattori.
- La configurazione di MFA è integrata in modo più approfondito nell'esperienza di configurazione di Azure AD nel portale di Azure.

Con questa versione di anteprima pubblica, la gestione e la creazione di report di MFA sono alla base dell'esperienza di configurazione di Azure Active Directory. È ora possibile gestire la funzionalità del portale di gestione di MFA nell'esperienza di Azure AD.

Per altri dettagli, vedere [Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Condizioni per l'utilizzo



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità  



È possibile usare le condizioni per l'utilizzo di Azure AD per presentare agli utenti informazioni quali dichiarazioni di non responsabilità pertinenti per requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione
- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici)
- Condizioni per l'utilizzo specifiche per l'accesso ad app aziendali a impatto elevato, come Salesforce

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Miglioramenti di Privileged Identity Management


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management  


Con Azure AD Privileged Identity Management, è possibile gestire, controllare e monitorare l'accesso alle risorse di Azure (anteprima) all'interno dell'organizzazione per:

- Sottoscrizioni
- Gruppi di risorse
- Macchine virtuali 

Tutte le risorse nel portale di Azure che usano la funzionalità Controllo degli accessi in base al ruolo di Azure possono trarre vantaggio da tutte funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD Privileged Identity Management.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure).


---

### <a name="access-reviews"></a>Verifiche di accesso


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance/conformità  



Le organizzazioni possono usare le verifiche di accesso (anteprima) per gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali: 

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso per i rispettivi accessi alle applicazioni e le appartenenze ai gruppi. I revisori possono decidere in modo efficiente se consentire ai guest l'accesso continuo sulla base delle informazioni dettagliate fornite dalle verifiche di accesso.
- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e delle appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Possibilità di nascondere le applicazioni di terze parti dall'utilità di avvio delle app di Office 365 e di App personali



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Single Sign-On  



È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. È possibile nascondere le app nei casi in cui vengano visualizzati riquadri delle app per i servizi back-end o riquadri duplicati che creano disordine nelle icone di avvio delle app degli utenti. È possibile abilitare o disabilitare questa proprietà nella sezione **Proprietà** dell'app di terze parti tramite l'opzione **Visibile agli utenti?**. È anche possibile nascondere un'app a livello di codice tramite PowerShell. 

Per altre informazioni, vedere [Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa agli eventi di rischio di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.


## <a name="september-2017"></a>Settembre 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix per Identity Manager


**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  



Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Identity Manager 2016 Service Pack 1. Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti.
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di Identity Manager 2016 Service Pack 1 fino alla build 4.4.1459.0 per Identity Manager 2016. 
- Richiede Identity Manager 2016 build 4.4.1302.0. 

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
