---
title: Implementazione del playbook di PoC di Azure Active Directory | Documentazione Microsoft
description: "Esplorare e implementare rapidamente gli scenari di Gestione delle identità e degli accessi"
services: active-directory
keywords: azure active directory, playbook, modello di verifica, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 298ff9b851f604b89e5672e41ab112b67990b0d6
ms.contentlocale: it-it
ms.lasthandoff: 04/12/2017

---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Playbook di PoC di Azure Active Directory: implementazione

## <a name="foundation---syncing-ad-to-azure-ad"></a>Introduzione: sincronizzazione di AD con Azure AD 

Un'identità ibrida è la base per la maggior parte dei clienti aziendali che dispongono già di una directory locale. L'obiettivo qui è intenzionalmente di trascorrere il minor tempo possibile per mostrare il valore degli scenari di identità e di accessi effettivi. 

| Scenario | Blocchi predefiniti| 
| --- | --- |  
| [Estensione dell'identità locale nel cloud](#extending-your-on-premises-identity-to-the-cloud) | [Sincronizzazione directory: sincronizzazione dell'hash delle password](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Nota**: se si dispone già di DirSync/ADSync o di versioni precedenti di Azure AD Connect, questo passaggio è facoltativo. Alcuni scenari in questa guida potrebbero richiedere la versione più recente di Azure AD Connect.  <br/>[Personalizzazione](active-directory-playbook-building-blocks.md#branding) | 
| [Assegnare licenze di Azure AD usando i gruppi](#assigning-azure-ad-licenses-using-groups) | [Licenze basate sui gruppi](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Estensione dell'identità locale nel cloud 

1. Bob è l'amministratore di Active Directory in Contoso. Ha il compito di abilitare l'identità come servizio per un gruppo di utenti. Dopo l'esecuzione della procedura guidata di Azure AD Connect, l'identità degli utenti di destinazione è disponibile nel cloud. 
2. Bob chiede a Susie, una utente di destinazione, di accedere al pannello di accesso di Azure Active Directory e verificare di poter eseguire l'autenticazione. Susie vedrà una pagina di accesso personalizzata e un pannello di accesso vuoto pronto per l'abilitazione dell'accesso futuro alle applicazioni.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Assegnare le licenze di Azure AD usando i gruppi 

1. Bob, l'amministratore globale di Azure AD, vuole assegnare le licenze di Azure AD a un gruppo specifico di utenti come parte della distribuzione iniziale di Azure AD.
2. Bob crea un gruppo per gli utenti pilota. 
3. Assegna le licenze al gruppo
4. Susie, una delle Information Worker, viene aggiunta al gruppo di sicurezza nell'ambito delle sue funzioni lavorative
5. Dopo un certo tempo, Susie ha accesso alla licenza Premium di Azure AD. Ciò abiliterà più degli scenari PoC in seguito.

## <a name="theme---lots-of-apps-one-identity"></a>Tema: molte app, una sola identità

| Scenario | Blocchi predefiniti| 
| --- | --- |  
| [Integrare applicazioni SaaS: SSO federato](#integrate-saas-applications---federated-sso) | [SaaS: configurazione SSO federato](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Gruppi: proprietà delegata](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integrare applicazioni SaaS: SSO con password](#integrate-saas-applications---password-sso) | [SaaS: configurazione SSO con password](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [SSO ed eventi del ciclo di vita delle identità](#sso-and-identity-lifecycle-events) | [SaaS e ciclo di vita delle identità](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Proteggere l'accesso agli account condivisi](#secure-access-to-shared-accounts) | [SaaS: configurazione di account condivisi](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Proteggere l'accesso remoto alle applicazioni locali](#secure-remote-access-to-on-premises-applications) | [Configurazione del proxy delle app](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Sincronizzare le identità LDAP con Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Configurazione del connettore LDAP generico](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integrare applicazioni SaaS: SSO federato 

1. Bob, l'amministratore globale di Azure AD, riceve una richiesta dal reparto marketing di abilitare l'accesso all'istanza di ServiceNow. Bob trova ed esegue l'esercitazione dettagliata nella documentazione di Azure AD e delega l'assegnazione di utenti per l'app a Kevin, il responsabile del team di marketing. 
2. Kevin accede come proprietario dei diritti per ServiceNow e assegna Susie all'app. Kevin nota anche che il profilo di Susie è stato creato automaticamente in ServiceNow.
3. Susie è una Information Worker del reparto marketing. Accede ad Azure AD e vede tutte le applicazioni SaaS a cui è stata assegnata nel portale App personali. Da qui accede senza problemi a ServiceNow.
4. Il reparto marketing vuole controllare chi ha avuto accesso a ServiceNow. Bob scarica un report delle attività e lo condivide con Kevin tramite posta elettronica.  

### <a name="sso-and-identity-lifecycle-events"></a>SSO ed eventi del ciclo di vita delle identità

1. Susie prende un congedo e l'account AD locale viene disabilitato temporaneamente in base ai criteri aziendali. Susie ora non può eseguire l'accesso ad Azure AD e quindi non può accedere a ServiceNow. 
2. Susie accetta un trasferimento laterale dal reparto marketing a quello vendite. Kevin rimuove il suo accesso da ServiceNow. Susie accede al portale App personali di Azure AD e il riquadro ServiceNow non è più visibile. Dopo 10 minuti Kevin conferma che l'account di Susie è stato disabilitato dalla console di gestione di ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Integrare applicazioni SaaS: SSO con password

1. Bob configura l'accesso ad Atlassian HipChat. HipChat prevede l'integrazione dell'accesso SSO con password e concede l'accesso a Susie
2. Susie accede al portale App personali dove è presente un collegamento per scaricare l'estensione del browser IE di AD Azure che Susie scarica
3. Facendo clic, vengono richieste le credenziali nome utente e password per HipChat. Si tratta di un'operazione occasionale; al termine Susie ha accesso a HipChat
4. Pochi giorni dopo Susie apre il portale App personali e fa clic di nuovo su HipChat. Questa volta ottiene l'accesso in modo trasparente
5. Kevin, il proprietario dell'app HipChat, vuole verificare chi ha avuto accesso all'applicazione. Bob scarica un report di controllo e lo condivide con Kevin tramite posta elettronica. 

### <a name="secure-access-to-shared-accounts"></a>Proteggere l'accesso agli account condivisi 

1. A Bob viene affidato il compito di proteggere l'handle Twitter condiviso per i membri del team vendite. Aggiunge Twitter come applicazione SSO e assegna l'applicazione al gruppo di sicurezza del team vendite. Ha ricevuto le credenziali per l'account condiviso e le fornisce nel sistema. 
2. La condivisione delle credenziali Twitter non risulta più attendibile poiché sono note e più persone. Bob abilita il rollover automatico della password di Twitter.
3. Susie, un membro del team vendite, accede al portale App personali, dove è presente un collegamento per scaricare l'estensione del browser IE di AD Azure. Esegue l'installazione.
4. Facendo clic ottiene l'accesso direttamente a Twitter. Non conosce la password.
5. Anche Arnold fa parte del team vendite. La sua esperienza è la stessa di Susie nei passaggi 3 e 4
6. Il reparto vendite desidera controllare chi ha avuto accesso Twitter. Bob scarica un report delle attività e lo condivide con Kevin tramite posta elettronica. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Proteggere l'accesso remoto alle applicazioni locali

1. Bob, l'amministratore globale di AD Azure, ha ricevuto numerose richieste di consentire ai dipendenti di accedere alle numerose e utili risorse locali, ad esempio l'applicazione per le spese, mentre lavorano in modalità remota. Segue le indicazioni nella [documentazione del Proxy di applicazione](active-directory-application-proxy-enable.md) di installare un connettore e pubblica l'applicazione per le spese come applicazione Proxy dell'applicazione. 
2. Bob condivide l'URL dell'applicazione per le spese esterna con Susie, una delle dipendenti che necessita dell'accesso remoto. Susie accede al collegamento e dopo l'autenticazione con AAD è in grado di accedere all'app per le spese e restare produttiva con l'accesso remoto. 
3. Bob continua quindi a pubblicare applicazioni locali aggiuntive usando lo stesso processo e concedendo l'accesso agli utenti in base alle esigenze. Aggiunge l'accesso condizionale e l'autenticazione Multi-Factor Authentication per le applicazioni più sensibili che pubblica, per garantire una protezione aggiuntiva.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Sincronizzare le identità LDAP con Azure AD

1. L'azienda di Bob ha un'infrastruttura delle identità complessa. La maggior parte degli utenti viene gestita all'interno di Active Directory Domain Services (ADDS) di Windows Server. Alcuni utenti vengono gestiti dal sistema HR all'interno di Active Directory Lightweight Directory Services (ADLDS).
2. A Bob viene assegnato il compito di abilitazione l'accesso alle app SaaS per tutti gli utenti, anche quelli non presenti in ADDS.
3. Bob configura il connettore LDAP generico per estrarre i dati da ADLDS in Azure AD Connect.
4. Bob crea le regole di sincronizzazione per consentire che gli utenti LDAP vengano popolati in Metaverse e Azure AD
5. Susie, come utente LDAP, accede alla propria app SaaS usando l'identità sincronizzata



> [!IMPORTANT] 
> Si tratta di una configurazione avanzata che richiede una certa conoscenza di FIM o MIM. Se usata in produzione, è consigliabile leggere le domande su questa configurazione in [supporto tecnico Premier](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Tema: aumentare la sicurezza 

| Scenario | Blocchi predefiniti| 
| --- | --- |  
| [Proteggere l'accesso all'account amministratore](#secure-administrator-account-access) | [Azure MFA con le chiamate telefoniche](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Proteggere l'accesso per le applicazioni](#secure-access-to-applications) | [Accesso condizionale per applicazioni SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Abilitare l'amministrazione JIT](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Proteggere le identità in base al rischio](#protect-identities-based-on-risk) | [Rilevare eventi di rischio](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Distribuire criteri di rischio di accesso](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Eseguire l'autenticazione senza password con l'autenticazione basata su certificati](#authenticate-without-passwords-using-certificate-based-authentication) | [Configurare l'autenticazione basata su certificati](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Proteggere l'accesso all'account amministratore

1. Bob è l'amministratore globale di Azure AD. Ha identificato Stuart come co-amministratore del servizio. 
2. Bob configura l'account di Stuart in modo che venga richiesta sempre l'autenticazione MFA per migliorare le condizioni di sicurezza
3. Stuart accede al portale di Azure e verifica che deve registrare il suo numero di telefono per continuare l'accesso
4. Gli accessi successivi di Stuart sono ora protetti con l'autenticazione Multi-Factor Authentication e Stuart riceve una telefonata a verifica della propria identità.

### <a name="secure-access-to-applications"></a>Proteggere l'accesso alle applicazioni

1. Kevin è il proprietario aziendale dei diritti di ServiceNow. L'azienda vuole ora che gli utenti eseguano l'accesso con l'autenticazione MFA quando accedono all'esterno della rete aziendale.
2. Bob, l'amministratore globale di Azure AD, aggiunge un criterio di accesso condizionale all'applicazione ServiceNow per abilitare l'autenticazione MFA per l'accesso esterno
3. Susie, una Information Worker, accede al portale App personali e fa clic sul riquadro ServiceNow. A Susie viene visualizzata una richiesta di autenticazione MFA.

### <a name="enable-just-in-time-jit-administration"></a>Abilitare l'amministrazione JIT

1. Bob e Stuart sono amministratori globali di Azure AD. Vogliono attivare l'accesso JIT ai ruoli di gestione e anche tenere traccia dell'uso dei ruoli con privilegi.
2. Bob abilita la gestione PIM nel tenant di Azure AD e diventa amministratore della sicurezza. Modifica sia sé stesso come utente sia l'appartenenza al ruolo di amministratore globale di Stuart da permanente a idoneo.
3. Bob e Stuart ora devono attivare il proprio ruolo tramite il portale di Azure prima di eseguire eventuali modifiche alla configurazione di Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Proteggere le identità in base al rischio 

1. Susie, una Information Worker, tenta di eseguire l'accesso da un browser Tor. 
2. Bob controlla il dashboard Azure AD Identity Protection e visualizza l'account di accesso di Susie da un indirizzo IP anonimo. Il team che gestisce la sicurezza vuole richiedere l'autenticazione MFA per questi accessi
3. Bob abilita i criteri di Azure AD Identity Protection per richiedere l'autenticazione MFA per gli eventi a medio o alto rischio
4. Dopo un po' di tempo Susie accede di nuovo dal browser Tor. Questa volta le verrà visualizzata la richiesta di autenticazione MFA

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Eseguire l'autenticazione senza password con l'autenticazione basata su certificati

1. Bob è l'amministratore globale per l'istituto finanziario, che impedisce l'uso di password come fattore di autenticazione per le proprie applicazioni.
2. Bob abilita e applica l'autenticazione mediante certificato in ADFS e Azure AD
3. Durante l'accesso dell'applicazione a Susie viene richiesto di eseguire l'autenticazione tramite il certificato

## <a name="theme---scale-with-self-service"></a>Tema: scalabilità self-service

| Scenario | Blocchi predefiniti| 
| --- | --- |  
| [Reimpostazione password self-service](#self-service-password-reset) | [Reimpostazione password self-service](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Accesso self-service alle applicazioni](#self-service-access-to-applications) | [Accesso self-service alle applicazioni](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Reimpostazione della password self-service 

1. Bob, l'amministratore globale di Azure AD, consente la gestione delle password self-service a un gruppo specifico di utenti, tra cui Susie. 
2. Susie accede al portale App personali, dove viene visualizzato un messaggio che indica di registrare le informazioni di sicurezza per eventi futuri di reimpostazione delle password.
3. Dopo pochi giorni Susie dimentica la password e la reimposta tramite il portale di Azure AD

### <a name="self-service-access-to-applications"></a>Accesso self-service alle applicazioni 

1. Kevin è il proprietario aziendale dei diritti di ServiceNow. Vuole che gli utenti si "registrino"su richiesta, anziché aggiungerli tutti contemporaneamente
2. Bob, l'amministratore globale di Azure AD, modifica l'applicazione ServiceNow per abilitare le richieste self-service
3. Susie, una Information Worker, accede al portale App personali e fa clic sul pulsante "Aggiungi altre applicazioni"; ServiceNow è presente come una delle applicazioni consigliate. Quindi Susie torna al portale App personali dove è visibile l'applicazione ServiceNow.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
