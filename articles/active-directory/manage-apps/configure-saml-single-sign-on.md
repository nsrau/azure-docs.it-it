---
title: Informazioni sulle Single Sign-On basate su SAML (SSO) per le app in Azure Active Directory
description: Informazioni sulle Single Sign-On basate su SAML (SSO) per le app in Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: ec020ecd4c2bcf6e9186afb3d2c4a79ef235c371
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658911"
---
# <a name="understand-saml-based-single-sign-on"></a>Informazioni Single Sign-On basate su SAML

Nella [serie di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni si è appreso come usare Azure ad come provider di identità (IDP) per un'applicazione. Questo articolo illustra in modo più dettagliato l'opzione basata su SAML per Single Sign-On. 


## <a name="before-you-begin"></a>Prima di iniziare

L'utilizzo di Azure AD come provider di identità (IdP) e la configurazione di Single Sign-On (SSO) può essere semplice o complesso a seconda dell'applicazione utilizzata. Alcune applicazioni possono essere configurate solo con poche azioni. Altri richiedono una configurazione approfondita. Per velocizzare la conoscenza, esaminare la [serie di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni. Se l'applicazione che si sta aggiungendo è semplice, probabilmente non è necessario leggere questo articolo. Se l'applicazione che si sta aggiungendo richiede una configurazione personalizzata per SSO basato su SAML, questo articolo è adatto all'utente.

Nella [serie di guide introduttive](add-application-portal-setup-sso.md)è disponibile un articolo sulla configurazione di Single Sign-on. Si apprenderà come accedere alla pagina di configurazione SAML per un'app. La pagina di configurazione SAML include cinque sezioni. Queste sezioni sono descritte in dettaglio in questo articolo.

> [!IMPORTANT] 
> Esistono alcuni scenari in cui l'opzione **Single Sign-on** non sarà presente nella navigazione per un'applicazione nelle **applicazioni aziendali**. 
>
> Se l'applicazione è stata registrata usando **registrazioni app** , la funzionalità Single Sign-on è configurata in modo da usare OAuth OIDC per impostazione predefinita. In questo caso, l'opzione **Single Sign-on** non verrà visualizzata nella finestra di navigazione in **applicazioni aziendali**. Quando si usa **registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per ulteriori informazioni sul file manifesto, vedere [Azure Active Directory manifesto dell'applicazione](../develop/reference-app-manifest.md). Per altre informazioni sugli standard SSO, vedere [autenticazione e autorizzazione con la piattaforma di identità Microsoft](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Gli altri scenari in cui l' **accesso Single Sign-on** non sarà presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non ha le autorizzazioni necessarie (amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire l' **accesso Single Sign-on** , ma non sarà possibile salvarlo. Per ulteriori informazioni sui ruoli amministrativi Azure AD, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Configurazione SAML di base

È necessario ottenere i valori necessari dal fornitore dell'applicazione. È possibile immettere manualmente i valori o caricare un file di metadati per estrarre il valore dei campi.

> [!TIP]
> Molte app sono già preconfigurate per l'uso con Azure AD. Queste app sono elencate nella raccolta di app che è possibile esplorare quando si aggiunge un'app al tenant di Azure AD. La [serie di guide introduttive](add-application-portal-setup-sso.md) illustra il processo. Per le app nella raccolta sono disponibili istruzioni dettagliate e dettagliate. Per accedere ai passaggi è possibile fare clic sul collegamento nella pagina di configurazione SAML per l'app, come descritto nella serie di guide introduttive oppure è possibile visualizzare un elenco di tutte le esercitazioni sulla configurazione delle app in [app Saas](../saas-apps/tutorial-list.md).

| Impostazione della configurazione SAML di base | SSO avviato da provider di servizi | SSO avviato da IdP | Descrizione |
|:--|:--|:--|:--|
| **Identificatore (ID entità)** | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica l'applicazione in modo univoco. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML. L'applicazione dovrebbe convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione. Immettere un URL con il modello seguente: "https://<subdomain>.contoso.com" *È possibile trovare questo valore come elemento **Autorità di certificazione** nella richiesta **AuthRequest** (SAML) inviata dall'applicazione.* |
| **URL di risposta** | Obbligatoria | Obbligatoria | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. È possibile usare i campi URL di risposta aggiuntivi per specificare più URL di risposta. Ad esempio, potrebbero essere necessari URL di risposta aggiuntivi per più sottodomini. In alternativa, a scopo di test è possibile specificare contemporaneamente più URL di risposta (URL pubblici e host locale). |
| **URL di accesso** | Obbligatoria | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Microsoft 365 o Azure AD app personali. Se è vuoto, Azure AD esegue un accesso avviato da IdP quando un utente avvia l'applicazione da Microsoft 365, Azure AD app personali o l'URL SSO Azure AD.|
| **Stato dell'inoltro** | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. In genere il valore è un URL valido per l'applicazione. Tuttavia, alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.
| **URL di disconnessione** | Facoltativo | Facoltativo | Usato per restituire una risposta di disconnessione SAML all'applicazione.

## <a name="user-attributes-and-claims"></a>Attributi e attestazioni utente 

Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia all'applicazione un token SAML contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. Potrebbe essere necessario personalizzare queste attestazioni se, ad esempio, l'applicazione richiede valori di attestazione specifici o un formato del **Nome** diverso dal nome utente. 

> [!IMPORTANT]
> Molte app sono già preconfigurate e nella raccolta di app e non è necessario preoccuparsi di impostare le attestazioni di utenti e gruppi. La [serie di guide introduttive](add-application-portal.md) illustra come aggiungere e configurare app.


Il valore identificatore **univoco dell'identificatore utente (ID nome)** è un'attestazione obbligatoria ed è importante. Il valore predefinito è *User. userPrincipalName*. L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

Per altre informazioni sulla personalizzazione delle attestazioni SAML, vedere [procedura: personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).

È possibile aggiungere nuove attestazioni. per informazioni dettagliate, vedere [aggiunta di attestazioni specifiche dell'applicazione](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) o aggiunta di attestazioni di gruppo, vedere [configurare attestazioni di gruppo](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Per informazioni sugli altri modi disponibili per personalizzare il token SAML di Azure AD nell'applicazione, vedere le risorse seguenti.
>- Per creare ruoli personalizzati tramite il portale di Azure, vedere [Configurare le attestazioni dei ruoli](../develop/active-directory-enterprise-app-role-management.md).
>- Per personalizzare le attestazioni tramite PowerShell, vedere [Personalizzare le attestazioni - PowerShell](../develop/active-directory-claims-mapping.md).
>- Per modificare il manifesto dell'applicazione e configurare attestazioni facoltative per l'applicazione, vedere [Configurare le attestazioni facoltative](../develop/active-directory-optional-claims.md).
>- Per impostare i criteri di durata per i token di aggiornamento, i token di accesso, i token di sessione e i token ID, vedere [Configurare la durata dei token](../develop/active-directory-configurable-token-lifetimes.md). In alternativa, per limitare le sessioni di autenticazione tramite Accesso condizionale di Azure AD, vedere [Funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>Certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. Questo certificato è necessario per configurare la relazione di trust tra Azure AD e l'applicazione. Per informazioni dettagliate sul formato del certificato, vedere la documentazione sul protocollo SAML dell'applicazione. Per altre informazioni, vedere [Gestione di certificati per l'accesso Single Sign-On federato](manage-certificates-for-federated-single-sign-on.md) e [Opzioni avanzate di firma del certificato nel token SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Molte app sono già preconfigurate e nella raccolta di app e non è necessario approfondire i certificati. La [serie di guide introduttive](add-application-portal.md) illustra come aggiungere e configurare app.

Da Azure AD è possibile scaricare il certificato attivo in formato Base64 o RAW direttamente dalla pagina principale **Configurare l'accesso Single Sign-On con SAML**. È anche possibile ottenere il certificato attivo scaricando il file XML dei metadati dell'applicazione o usando l'URL dei metadati di Federazione dell'app. Per visualizzare, creare o scaricare i certificati (attivi o inattivi), seguire questa procedura.

Di seguito sono riportati alcuni aspetti comuni da verificare per verificare un certificato: 
   - *Data di scadenza corretta.* È possibile configurare la data di scadenza fino a un massimo di tre anni nel futuro.
   - *Stato attivo per il certificato corretto.* Se lo stato è **Inattivo**, modificarlo in **Attivo**. Per modificare lo stato, fare clic con il pulsante destro del mouse sulla riga del certificato e selezionare **Rendi attivo il certificato**.
   - *Opzione e algoritmo di firma corretti.*
   - *Indirizzi del messaggio di notifica corretti.* Quando la data del certificato attivo si avvicina alla scadenza, Azure AD invia una notifica all'indirizzo di posta elettronica configurato in questo campo.

In alcuni casi potrebbe essere necessario scaricare il certificato. Prestare attenzione quando si salva il percorso. Per scaricare il certificato, selezionare una delle opzioni per formato Base64, RAW o XML metadati federazione. Azure AD fornisce anche l'**URL dei metadati della federazione dell'app** tramite cui è possibile accedere ai metadati specifici dell'applicazione nel formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

> [!NOTE]
> L'applicazione deve essere in grado di gestire il marcatore dell'ordine dei byte presente nel file XML sottoposto a rendering quando si usa https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Byte Order Mark è rappresentato come carattere ASCII non stampabile» ¿e in Hex è rappresentato come EF BB BF quando si esaminano i dati XML.

Per apportare modifiche ai certificati, selezionare il pulsante modifica. Nella pagina **certificato di firma SAML** è possibile eseguire diverse operazioni:
   - Creare un nuovo certificato: selezionare **nuovo certificato**, selezionare la **Data di scadenza** e quindi fare clic su **Salva**. Per attivare il certificato, selezionare il menu di scelta rapida **(...)** e scegliere **Rendi attivo il certificato**.
   - Caricare un certificato con chiave privata e credenziali PFX: selezionare **Importa certificato** e selezionare il certificato. Immettere la **Password PFX** e quindi selezionare **Aggiungi**.  
   - Configurare la firma avanzata del certificato. Per ulteriori informazioni su queste opzioni, vedere [Advanced Certificate Signing Options](certificate-signing-options.md).
   - Invia una notifica a persone aggiuntive quando il certificato attivo è prossimo alla data di scadenza: immettere gli indirizzi di posta elettronica nei campi degli **indirizzi di posta elettronica di notifica** .

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurare l'applicazione per l'uso di Azure AD

La sezione **Configura \<applicationName>** elenca i valori che devono essere configurati nell'applicazione in modo da usare Azure ad come provider di identità SAML. È possibile impostare i valori nella pagina configurazione nel sito Web delle applicazioni. Ad esempio, se si sta configurando GitHub, si passa al sito di github.com e si impostano i valori. Se l'applicazione è già preconfigurata e nella raccolta Azure AD, viene visualizzato un collegamento per **visualizzare le istruzioni dettagliate**. In caso contrario, sarà necessario trovare la documentazione per l'applicazione che si sta configurando. 

I valori **URL di accesso** e URL di **disconnessione** si risolvono entrambi nello stesso endpoint, che è l'endpoint di gestione delle richieste SAML per Azure ad tenant. 

**Identificatore Azure AD** è il valore di **Issuer** nel token SAML inviato all'applicazione.

## <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Dopo aver configurato l'applicazione per l'uso di Azure AD come provider di identità basato su SAML, è possibile testare le impostazioni per verificare che l'accesso Single Sign-On funzioni per l'account. 

Selezionare **test** , quindi scegliere di eseguire il test con l'utente attualmente connesso o come un altro utente. 

Se l'accesso riesce, è possibile assegnare utenti e gruppi all'applicazione SAML. Congratulazioni.

Se viene visualizzato un messaggio di errore, completare la procedura seguente:

1. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?** .

    ![Ottieni procedure per la risoluzione](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selezionare **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.

3. Leggere le linee guida per la risoluzione e quindi tentare di risolvere il problema.

4. Eseguire di nuovo il test finché non viene completato correttamente.

Per altre informazioni, vedere [Eseguire il debug di Single Sign-On basato su SAML per applicazioni in Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
- [Assegnare utenti o gruppi all'applicazione](./assign-user-or-group-access-portal.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md)