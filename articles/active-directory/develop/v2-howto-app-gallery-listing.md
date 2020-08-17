---
title: Pubblicare l'app nella raccolta di app Azure AD
description: Informazioni su come elencare un'applicazione che supporta Single Sign-On nella raccolta di app Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 3d810d14dd6b49bc054e3844a60ec33c62dc084c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271244"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Pubblicare l'app nella raccolta di app Azure AD

È possibile pubblicare l'app nella raccolta di app Azure AD. Quando l'app viene pubblicata, viene visualizzata come opzione per i clienti che aggiungono app al tenant. 

Alcuni dei vantaggi derivanti dall'aggiunta dell'app alla raccolta Azure AD includono:

- I clienti trovano la migliore esperienza possibile Single Sign-On per l'app.
- Configurazione minima e semplice dell'applicazione.
- Ricerca rapida per l'individuazione dell'applicazione nella raccolta.
- Uso di questa integrazione per tutti i clienti con account Free, Basic e Premium di Azure AD.
- Esercitazione dettagliata per la configurazione per i clienti reciproci.

Sono inoltre disponibili molti vantaggi quando i clienti usano Azure AD come provider di identità per l'app. tra cui:

- Fornire Single Sign-On per gli utenti. Con l'accesso SSO si riducono i costi di supporto rendendo più semplice per i clienti Single Sign-On. Se SSO con un clic è abilitato, gli amministratori IT dei clienti non devono apprendere come configurare l'applicazione per l'uso nell'organizzazione. Per ulteriori informazioni su Single Sign-On, vedere [che cos'è Single Sign-on?](../manage-apps/what-is-single-sign-on.md).
- L'app può essere individuabile nella raccolta di app Microsoft 365, nell'utilità di avvio delle app Microsoft 365 e in Microsoft Search in Office.com. 
- Gestione integrata delle app. Per ulteriori informazioni sulla gestione delle app in Azure AD, vedere informazioni sulla [gestione delle applicazioni](../manage-apps/what-is-application-management.md).
- L'app può usare la [API Graph](https://docs.microsoft.com/graph/) per accedere ai dati che guidano la produttività degli utenti nell'ecosistema Microsoft.
- La documentazione specifica dell'applicazione coprodotta con il team di Azure AD per i clienti reciproci semplifica l'adozione.
- Si fornisce ai clienti la possibilità di gestire completamente l'autenticazione e l'autorizzazione delle identità dei dipendenti e dei guest.
- Inserimento di tutte le responsabilità di gestione e conformità degli account con il proprietario del cliente di tali identità.
- Possibilità di abilitare o disabilitare l'accesso SSO per provider di identità, gruppi o utenti specifici per soddisfare le esigenze aziendali.
- Si aumenta la commercializzazione e l'adozione. Molte organizzazioni di grandi dimensioni richiedono che (o aspirano) i dipendenti abbiano esperienze SSO senza problemi in tutte le applicazioni. Semplificare l'accesso SSO è importante.
- Si riduce l'attrito degli utenti finali, che può aumentare l'utilizzo degli utenti finali e aumentare i ricavi.
- I clienti che usano il sistema per la gestione delle identità tra domini ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) possono usare il provisioning per la stessa app.
- Aggiungere sicurezza e praticità quando gli utenti effettuano l'accesso alle applicazioni usando Azure AD SSO e rimuovendo la necessità di credenziali separate.

> [!TIP]
> Quando si offre un'applicazione per l'uso da parte di altre aziende tramite un acquisto o una sottoscrizione, l'applicazione viene reso disponibile ai clienti all'interno dei propri tenant di Azure. Questa operazione è nota come creazione di un'applicazione multi-tenant. Per una panoramica di questo concetto, vedere [applicazioni multi-tenant in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [locazione in Azure Active Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Per pubblicare l'app nella raccolta Azure AD è necessario accettare termini e condizioni specifici. Prima di iniziare, assicurarsi di leggere e accettare i [termini e le condizioni](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

I passaggi per la pubblicazione dell'app nella raccolta di app Azure AD sono:
1. Scegliere la Single Sign-On standard corretta per l'app.
2. Implementare Single Sign-On nell'app.
3. Creare il tenant di Azure e testare l'app.
4. Creare e pubblicare la documentazione.
5. Invia l'app.
6. Partecipa a Microsoft Partner Network.


## <a name="prerequisites"></a>Prerequisiti

È necessario un account permanente per i test con almeno due utenti registrati.


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Passaggio 1: scegliere la Single Sign-On standard corretta per l'app

Per elencare un'applicazione nella raccolta Azure AD app, è necessario implementare almeno una delle opzioni di Single Sign-On supportate. Per informazioni sulle opzioni di Single Sign-On e sul modo in cui i clienti li configureranno in Azure AD, vedere [opzioni SSO](../manage-apps/sso-options.md).

La tabella seguente confronta gli standard principali: Open Authentication 2,0 (OAuth 2,0) con OpenID Connect (OIDC), Security Assertion Markup Language (SAML) e Web Services Federation (WS-Fed).

| Funzionalità| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Single Sign-On basati sul Web| √| √ |
| Single Sign-out basato sul Web| √| √ |
| Single Sign-On basati su dispositivi mobili| √| √* |
| Single Sign-out basato su dispositivi mobili| √| √* |
| Criteri di accesso condizionale per le applicazioni per dispositivi mobili| √| X |
| Esperienza multi-factor authentication per applicazioni per dispositivi mobili| √| X |
| Microsoft Graph di accesso| √| X |

* Possibile, ma Microsoft non fornisce esempi o indicazioni.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 e OpenID Connect
OAuth 2,0 è un protocollo [standard del settore](https://oauth.net/2/) per l'autorizzazione. OpenID Connect (OIDC) è un livello di autenticazione delle identità [standard di settore](https://openid.net/connect/) basato sul protocollo OAuth 2,0. 

**Motivi per scegliere OAuth/OIDC**
- L'autorizzazione inerente a questi protocolli consente all'applicazione di accedere e di integrarsi con dati utente e aziendali avanzati tramite l'API Microsoft Graph.
- Semplifica l'esperienza dell'utente finale dei clienti quando si adotta SSO per l'applicazione. È possibile definire facilmente i set di autorizzazioni necessari, che vengono quindi rappresentati automaticamente all'amministratore o al consenso dell'utente finale.
- L'uso di questi protocolli consente ai clienti di usare i criteri di accesso condizionale e di Multi-Factor Authentication (multi-factor authentication) per controllare l'accesso alle applicazioni. 
- Microsoft fornisce librerie ed [esempi di codice in più piattaforme tecnologiche](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) per favorire lo sviluppo.  

**Aspetti da considerare**
- Se è già stata implementata Single Sign-On basata su SAML per l'applicazione, è possibile che non si voglia implementare un nuovo standard per ottenere l'app nella raccolta.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 o WS-Fed

SAML è uno standard Single Sign-On maturo e ampiamente adottato per le applicazioni Web. Per altre informazioni su come Azure usa SAML, vedere [come Azure usa il protocollo SAML](active-directory-saml-protocol-reference.md). 

Web Services Federation (WS-Fed) è uno [standard di settore](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) generalmente usato per le applicazioni Web sviluppate con la piattaforma .NET.

**Motivi per scegliere SAML**
- SAML 2,0 è una piattaforma standard e la maggior parte delle tecnologie supporta le librerie open source per SAML 2,0. 
- È possibile fornire ai clienti un'interfaccia di amministrazione per configurare SAML SSO. Possono configurare SAML SSO per Microsoft Azure AD e qualsiasi altro provider di identità che supporta SAML.

**Aspetti da considerare**
- Quando si usano protocolli SAML 2,0 o WSFed per le applicazioni per dispositivi mobili, alcuni criteri di accesso condizionale che includono l'autenticazione a più fattori (multi-factor authentication) avranno una riduzione dell'esperienza.
- Per accedere ai Microsoft Graph, sarà necessario implementare l'autorizzazione tramite OAuth 2,0 per generare i token necessari. 

### <a name="password-based"></a>Basato su password
L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. È utile anche per gli scenari in cui più utenti devono condividere un singolo account, ad esempio gli account di app di social media dell'organizzazione.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Passaggio 2: implementare Single Sign-On nell'app
Ogni app nella raccolta deve implementare una delle opzioni di Single Sign-On supportate. Per ulteriori informazioni sulle opzioni supportate, vedere [opzioni SSO](../manage-apps/sso-options.md).

Per OAuth e OIDC, vedere [linee guida sui modelli di autenticazione](v2-app-types.md) e gli [esempi di codice di Azure Active Directory](sample-v2-code.md).

Per SAML e WS-Fed, l'applicazione deve avere la possibilità di eseguire l'integrazione SSO in modalità SP o IDP. Verificare che questa funzionalità funzioni correttamente prima di inviare la richiesta.

Per ulteriori informazioni sull'autenticazione, vedere [la](../azuread-dev/v1-authentication-scenarios.md)pagina relativa all'autenticazione.

> [!IMPORTANT]
> Per le applicazioni federate (OpenID e SAML/WS-Fed), l'app deve supportare il modello SaaS (software as a Service). Le applicazioni della raccolta di Azure AD devono supportare più configurazioni dei clienti e non devono essere specifiche di un singolo cliente.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementare OAuth 2,0 e OpenID Connect

Per OpenID Connect, l'applicazione deve essere multi-tenant e il [Framework di consenso Azure ad](consent-framework.md) deve essere implementato correttamente per l'applicazione. L'utente può inviare la richiesta di accesso a un endpoint comune in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente in base all'ID del tenant e all'UPN dell'utente ricevuti nel token.

Per esaminare esempi specifici, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md). 

Per esaminare gli esempi specifici per dispositivi mobili, vedere: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Piattaforma UWP (Universal Windows Platform)](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementare SAML 2,0

Se l'app supporta SAML 2,0, è possibile integrarla direttamente con un tenant di Azure AD. Per altre informazioni sulla configurazione SAML con Azure AD, vedere [configurare Single Sign-on basato su SAML](../manage-apps/configure-saml-single-sign-on.md).

Microsoft non fornisce o consiglia librerie per le implementazioni SAML. Sono disponibili molte librerie open source.

### <a name="implement-ws-fed"></a>Implementare WS-Fed
Per ulteriori informazioni su WS-Fed in ASP.NET Core, vedere [autenticare gli utenti con WS-Federation in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementare l'insieme di credenziali delle password

Creare un'applicazione Web con una pagina di accesso HTML. Assicurarsi che l'applicazione supporti l'autenticazione basata su form, Single Sign-On in modo che l'insieme di credenziali delle password possa essere eseguito come previsto.


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>Passaggio 3: creare il tenant di Azure e testare l'app

Per poter testare l'app, è necessario un tenant di Azure AD. Per configurare l'ambiente di sviluppo, vedere [Guida introduttiva: configurare un tenant](quickstart-create-new-tenant.md).

In alternativa, un tenant Azure AD viene incluso in ogni sottoscrizione di Microsoft 365. Per configurare un ambiente di sviluppo Microsoft 365 gratuito, vedere [partecipare al programma Microsoft 365 Developer Program](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program).

Quando si dispone di un tenant, è necessario abilitare e testare l'accesso Single Sign-on. 

**Per le applicazioni OIDC o giuramento**, [registrare l'applicazione](quickstart-register-app.md) come applicazione multi-tenant. Selezionare l'opzione account in qualsiasi directory organizzativa e account Microsoft personali in tipi di account supportati.

**Per le applicazioni basate su SAML e WS-Fed**, si [configurano le applicazioni Single Sign-on basate su SAML](../manage-apps/configure-saml-single-sign-on.md) usando un modello SAML generico in Azure ad.

Se necessario, è anche possibile [convertire un'applicazione a tenant singolo in multi-tenant](howto-convert-app-to-be-multi-tenant.md) .


## <a name="step-4---create-and-publish-documentation"></a>Passaggio 4: creare e pubblicare la documentazione

### <a name="documentation-on-your-site"></a>Documentazione sul sito

La facilità di adozione è un fattore significativo nelle decisioni relative al software aziendale. La documentazione chiara facile da seguire supporta i clienti nel percorso di adozione e riduce i costi di supporto. Lavorando con migliaia di fornitori di software, Microsoft ha visto cosa funziona.

È consigliabile che la documentazione nel sito includa almeno gli elementi seguenti.

* Introduzione alla funzionalità SSO
  * Protocolli supportati
  * Versione e SKU
  * Elenco dei provider di identità supportati con collegamenti alla documentazione
* Informazioni sulle licenze per l'applicazione
* Controllo degli accessi in base al ruolo per la configurazione di SSO
* Procedura di configurazione SSO
  * Elementi di configurazione dell'interfaccia utente per SAML con i valori previsti dal provider
  * Informazioni sul provider di servizi da passare ai provider di identità
* Se OIDC/OAuth
  * Elenco delle autorizzazioni necessarie per il consenso con le motivazioni aziendali
* Passaggi di test per gli utenti pilota
* Informazioni sulla risoluzione dei problemi, inclusi codici e messaggi di errore
* Meccanismi di supporto per i clienti

### <a name="documentation-on-the-microsoft-site"></a>Documentazione sul sito Microsoft

Quando l'applicazione viene elencata con la raccolta di applicazioni di Azure Active Directory, che pubblica anche l'applicazione in Azure Marketplace, Microsoft genererà la documentazione per i clienti reciproci che spiegano il processo dettagliato. È possibile vedere un esempio [qui](https://aka.ms/appstutorial). Questa documentazione viene creata in base all'invio alla raccolta ed è possibile aggiornarla facilmente se si apportano modifiche all'applicazione con l'account GitHub.


## <a name="step-5---submit-your-app"></a>Passaggio 5: inviare l'app

Dopo aver verificato che l'integrazione dell'applicazione funzioni con Azure AD, inviare la richiesta dell'applicazione nel [portale di rete delle applicazioni Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

La prima volta che si tenta di accedere al portale viene visualizzata una delle due schermate. 

Se viene visualizzato il messaggio "non funziona", sarà necessario contattare il [team di integrazione di Azure ad SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Fornire l'account di posta elettronica che si vuole usare per l'invio della richiesta. Un indirizzo di posta elettronica aziendale, ad esempio, `name@yourbusiness.com` è preferibile. Il team di Azure AD aggiungerà l'account nel portale di rete delle applicazioni Microsoft.

Se viene visualizzata una pagina "Richiedi accesso", compilare la motivazione aziendale e selezionare **Richiedi accesso**.

Dopo aver aggiunto l'account, è possibile accedere al portale di rete delle applicazioni Microsoft e inviare la richiesta selezionando il riquadro **Invia richiesta (ISV)** nella Home page.

![Riquadro Invia richiesta (ISV) in home page](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problemi di accesso al portale

Se questo errore viene visualizzato durante l'accesso, di seguito sono riportati i dettagli sul problema e questo è il modo in cui è possibile risolverlo.

* Se l'accesso è stato bloccato come illustrato di seguito:

  ![problemi di risoluzione dell'applicazione nella raccolta](./media/howto-app-gallery-listing/blocked.png)

**Cosa sta succedendo:**

L'utente Guest è federato a un tenant principale che è anche un Azure AD. L'utente Guest è a rischio elevato. Microsoft non consente agli utenti ad alto rischio di accedere alle proprie risorse. Tutti gli utenti ad alto rischio (dipendenti o ospiti/fornitori) devono correggere o chiudere i rischi per accedere alle risorse Microsoft. Per gli utenti guest, questo rischio per l'utente deriva dal tenant principale e il criterio deriva dal tenant delle risorse (in questo caso Microsoft).
 
**Soluzioni protette:**

* Gli utenti Guest registrati in multi-factor authentication correggono il proprio rischio utente. Questa operazione può essere eseguita dall'utente guest che esegue una modifica o reimpostazione della password protetta ( https://aka.ms/sspr) nel tenant principale, che richiede l'autenticazione a più fattori e SSPR nel tenant principale). La modifica o la reimpostazione della password protetta deve essere avviata in Azure AD e non in locale.

* Gli utenti Guest hanno a disposizione gli amministratori per correggere i rischi. In questo caso, l'amministratore eseguirà la reimpostazione della password (generazione temporanea delle password). Questa operazione non richiede la protezione delle identità. L'amministratore dell'utente Guest può passare a https://aka.ms/RiskyUsers e fare clic su "Reimposta password".

* Gli utenti Guest hanno gli amministratori che chiudono o ignorano i rischi. Anche in questo caso non è necessaria la protezione delle identità. L'amministratore può passare a https://aka.ms/RiskyUsers e fare clic su' ignora il rischio utente '. Tuttavia, l'amministratore deve eseguire la diligenza per assicurarsi che si tratta di una valutazione dei rischi falsa positiva prima di chiudere il rischio utente. In caso contrario, vengono messi a rischio le risorse di Microsoft, evitando una valutazione dei rischi senza alcuna indagine.

> [!NOTE]
> In caso di problemi con l'accesso, contattare il [team di integrazione di Azure ad SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Opzioni specifiche di implementazione
Per aggiungere l'applicazione all'elenco nella raccolta usando OpenID Connect, selezionare **OpenID connect & OAuth 2,0** come illustrato.

![Elenco di un'applicazione OpenID Connect nella raccolta](./media/howto-app-gallery-listing/openid.png)

Se si vuole aggiungere l'applicazione all'elenco nella raccolta con **saml 2,0** o **WS-Fed**, selezionare **SAML 2.0/WS-Fed** come illustrato.

![Elenco di un'applicazione SAML 2,0 o WS-Fed nella raccolta](./media/howto-app-gallery-listing/saml.png)

Per aggiungere l'applicazione all'elenco della raccolta usando l'accesso SSO basato su password, selezionare **SSO con password** come illustrato.

![Elenco di un'applicazione con password SSO nella raccolta](./media/howto-app-gallery-listing/passwordsso.png)

Se si sta implementando un endpoint SCIM 2,0 per il provisioning dell'utente, selezionare l'opzione come illustrato. 

   ![Richiesta di provisioning utenti](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Aggiornare o rimuovere un elenco esistente

È possibile aggiornare o rimuovere un'app della raccolta esistente nel [portale di rete delle applicazioni Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Elenco di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> In caso di problemi con l'accesso, vedere la sezione precedente relativa alla creazione dell'account. Se questo non funziona, contattare il [team di integrazione di Azure ad SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).


### <a name="timelines"></a>Tempistica

La sequenza temporale per il processo di visualizzazione di un'applicazione SAML 2,0 o WS-Fed nella raccolta è da 7 a 10 giorni lavorativi.

![Sequenza temporale per l'elenco di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/timeline.png)

La sequenza temporale per il processo di visualizzazione di un'applicazione OpenID Connect nella raccolta è di 2 a 5 giorni lavorativi.

![Sequenza temporale per l'elenco di un'applicazione OpenID Connect nella raccolta](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Escalation

Per le escalation, inviare un messaggio di posta elettronica al [team di integrazione di Azure ad SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com)e risponderemo appena possibile.


## <a name="step-6---join-the-microsoft-partner-network"></a>Passaggio 6: partecipare al Microsoft Partner Network
Il Microsoft Partner Network fornisce l'accesso immediato a risorse, programmi, strumenti e connessioni esclusivi. Per entrare in contatto con la rete e creare il piano di marketing, vedere [REACH commercial customers](https://partner.microsoft.com/explore/commercial#gtm).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di applicazioni che supportano gli accessi di Azure AD, vedere [Scenari di autenticazione per Azure AD](authentication-flows-app-scenarios.md).
