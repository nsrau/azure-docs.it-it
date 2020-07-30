---
title: Accesso Single Sign-On basato su SAML - applicazioni non incluse nella raccolta - Microsoft Identity Platform | Microsoft Docs
description: Configurare l'accesso Single Sign-On (SSO) per applicazioni non incluse nella raccolta in Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cee2b9a0ea32a3b331849263c8a97f55930542d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379652"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configurare l'accesso Single Sign-On basato su SAML per applicazioni non incluse nella raccolta

Quando si [aggiunge un'app inclusa nella raccolta](add-gallery-app.md) o un'app Web [non inclusa nella raccolta](add-non-gallery-app.md) alle applicazioni aziendali di Azure AD, una delle opzioni di accesso Single Sign-On disponibili è [l'accesso Single Sign-On basato su SAML](what-is-single-sign-on.md#saml-sso). Scegliere SAML laddove possibile per le applicazioni che eseguono l'autenticazione usando uno dei protocolli SAML. Con l'accesso Single Sign-On SAML, Azure AD esegue l'autenticazione all'applicazione usando l'account Azure AD dell'utente. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. È possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML. Questo articolo descrive come configurare l'accesso Single Sign-On basato su SAML per un'app non della raccolta. 

> [!NOTE]
> Si vuole aggiungere un'app della raccolta? Seguire le istruzioni dettagliate nell'[elenco di esercitazioni sulle app SaaS](../saas-apps/tutorial-list.md)

Per configurare l'accesso Single Sign-On basato su SAML per un'applicazione non inclusa nella raccolta senza scrivere alcun codice, è necessario disporre di una sottoscrizione Azure AD e l'applicazione deve supportare SAML 2.0. Per altre informazioni sulle versioni di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Prima di iniziare

Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [Aggiungere un'app non inclusa nella raccolta](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passaggio 1. Modificare la configurazione SAML di base

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

2. Passare a **Azure Active Directory** > **Applicazioni aziendali** e selezionare l'applicazione nell'elenco. 
   
   - Per cercare l'applicazione, nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**. Immettere il nome dell'applicazione nella casella di ricerca, quindi selezionarla nei risultati.

3. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

   - Si noti che esistono scenari in cui l'opzione **Single Sign-on** non sarà presente. Ad esempio, se l'applicazione è stata registrata usando **registrazioni app** , la funzionalità Single Sign-on è configurata in modo da usare OAuth OIDC per impostazione predefinita. In questo caso, l'opzione **Single Sign-on** non verrà visualizzata nella finestra di navigazione in **applicazioni aziendali**. Quando si usa **registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per ulteriori informazioni sul file manifesto, vedere ( https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) . Per ulteriori informazioni sugli standard SSO, vedere ( https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform) . Gli altri scenari in cui l' **accesso Single Sign-on** non sarà presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non ha le autorizzazioni necessarie (amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire l' **accesso Single Sign-on** , ma non sarà possibile salvarlo. Per ulteriori informazioni sui ruoli amministrativi Azure AD, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

4. Selezionare **SAML**. Viene visualizzata la pagina **Configura l'accesso Single Sign-On con SAML - Anteprima**.

   ![Passaggio 1 Modificare la configurazione SAML di base](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Per modificare le opzioni di configurazione SAML di base, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Configurazione SAML di base**.

1. Immettere le impostazioni seguenti. È necessario ottenere i valori necessari dal fornitore dell'applicazione. È possibile immettere manualmente i valori o caricare un file di metadati per estrarre il valore dei campi.

    | Impostazione della configurazione SAML di base | SSO avviato da provider di servizi | SSO avviato da IdP | Descrizione |
    |:--|:--|:--|:--|
    | **Identificatore (ID entità)** | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica l'applicazione in modo univoco. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML. L'applicazione dovrebbe convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione. Immettere un URL con il modello seguente: "https://<subdomain>.contoso.com" *È possibile trovare questo valore come elemento **Autorità di certificazione** nella richiesta **AuthRequest** (SAML) inviata dall'applicazione.* |
    | **URL di risposta** | Necessario | Necessario | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. È possibile usare i campi URL di risposta aggiuntivi per specificare più URL di risposta. Ad esempio, potrebbero essere necessari URL di risposta aggiuntivi per più sottodomini. In alternativa, a scopo di test è possibile specificare contemporaneamente più URL di risposta (URL pubblici e host locale). |
    | **URL di accesso** | Obbligatoria | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Office 365 o dal pannello di accesso di Azure AD. Se questo campo viene lasciato vuoto e un utente avvia l'applicazione da Office 365, dal pannello di accesso di Azure AD o dall'URL Single Sign-On di Azure AD, Azure AD esegue l'accesso Single Sign-On avviato da IdP.|
    | **Stato dell'inoltro** | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. In genere il valore è un URL valido per l'applicazione. Tuttavia, alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.
    | **URL di disconnessione** | Facoltativo | Facoltativo | Usato per restituire una risposta di disconnessione SAML all'applicazione.

Per altre informazioni, vedere [Protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Passaggio 2. Configurare attributi e attestazioni utente 

Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia all'applicazione un token SAML contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. Potrebbe essere necessario personalizzare queste attestazioni se, ad esempio, l'applicazione richiede valori di attestazione specifici o un formato del **Nome** diverso dal nome utente. I requisiti per le app della raccolta sono descritti nelle [esercitazioni specifiche dell'applicazione](../saas-apps/tutorial-list.md) oppure è possibile rivolgersi al fornitore dell'applicazione. I passaggi generali per la configurazione degli attributi e delle attestazioni utente sono descritti di seguito.

1. Nell'angolo in alto a destra della sezione **Attributi e attestazioni utente** selezionare l'icona **Modifica** (a forma di matita).

   ![Passaggio 2 Configurare attributi e attestazioni utente](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verificare il **Valore identificatore nome**. Il valore predefinito è *user.principalname*. L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

3. Per modificare il **Valore identificatore nome**, selezionare l'icona **Modifica** (a forma di matita) per il campo **Valore identificatore nome** campo. Apportare le modifiche appropriate al formato e all'origine dell'identificatore in base alle esigenze. Per informazioni dettagliate, vedere [Modifica di NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Al termine, salvare le modifiche. 
 
4. Per configurare le attestazioni di gruppi, selezionare l'icona **Modifica** relativa al campo **Gruppi restituiti nell'attestazione**. Per informazioni dettagliate, vedere [Configurare le attestazioni di gruppi](../hybrid/how-to-connect-fed-group-claims.md).

5. Per aggiungere un'attestazione, selezionare **Aggiungi nuova attestazione** nella parte superiore della pagina. Immettere il **Nome** e selezionare l'origine appropriata. Se si seleziona l'origine **Attributo**, è necessario scegliere l'**Attributo di origine** da usare. Se si seleziona l'origine **Traduzione**, è necessario scegliere la **Trasformazione** e il **Parametro 1** da usare. Per informazioni dettagliate, vedere [Aggiunta di attestazioni specifiche dell'applicazione](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Al termine, salvare le modifiche. 

6. Selezionare **Salva**. Nella tabella viene visualizzata la nuova attestazione.

   > [!NOTE]
   > Per informazioni sugli altri modi disponibili per personalizzare il token SAML di Azure AD nell'applicazione, vedere le risorse seguenti.
   >- Per creare ruoli personalizzati tramite il portale di Azure, vedere [Configurare le attestazioni dei ruoli](../develop/active-directory-enterprise-app-role-management.md).
   >- Per personalizzare le attestazioni tramite PowerShell, vedere [Personalizzare le attestazioni - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Per modificare il manifesto dell'applicazione e configurare attestazioni facoltative per l'applicazione, vedere [Configurare le attestazioni facoltative](../develop/active-directory-optional-claims.md).
   >- Per impostare i criteri di durata per i token di aggiornamento, i token di accesso, i token di sessione e i token ID, vedere [Configurare la durata dei token](../develop/active-directory-configurable-token-lifetimes.md). In alternativa, per limitare le sessioni di autenticazione tramite Accesso condizionale di Azure AD, vedere [Funzionalità di gestione delle sessioni di autenticazione](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passaggio 3. Gestire il certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. Per configurare il trust tra Azure AD e l'applicazione, è necessario questo certificato. Per informazioni dettagliate sul formato del certificato, vedere la documentazione sul protocollo SAML dell'applicazione. Per altre informazioni, vedere [Gestione di certificati per l'accesso Single Sign-On federato](manage-certificates-for-federated-single-sign-on.md) e [Opzioni avanzate di firma del certificato nel token SAML](certificate-signing-options.md).

Da Azure AD è possibile scaricare il certificato attivo in formato Base64 o RAW direttamente dalla pagina principale **Configurare l'accesso Single Sign-On con SAML**. In alternativa, è possibile ottenere il certificato attivo scaricando il file XML dei metadati dell'applicazione o usando l'URL dei metadati della federazione dell'app. Per visualizzare, creare o scaricare i certificati (attivi o inattivi), seguire questa procedura.

1. Passare alla sezione **Certificato di firma SAML**. 

   ![Passaggio 3 Gestire il certificato di firma SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Verificare che il certificato abbia:

   - *La data di scadenza desiderata.* È possibile configurare la data di scadenza fino a un massimo di tre anni nel futuro.
   - *Stato attivo per il certificato desiderato.* Se lo stato è **Inattivo**, modificarlo in **Attivo**. Per modificare lo stato, fare clic con il pulsante destro del mouse sulla riga del certificato desiderato e selezionare **Rendi attivo il certificato**.
   - *Opzione e algoritmo di firma corretti.*
   - *Indirizzi del messaggio di notifica corretti.* Quando la data del certificato attivo si avvicina alla scadenza, Azure AD invia una notifica all'indirizzo di posta elettronica configurato in questo campo.

2. Per scaricare il certificato, selezionare una delle opzioni per formato Base64, RAW o XML metadati federazione. Azure AD fornisce anche l'**URL dei metadati della federazione dell'app** tramite cui è possibile accedere ai metadati specifici dell'applicazione nel formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Per gestire, creare o importare un certificato, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Certificato di firma SAML**.

   ![Certificato di firma SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Eseguire una delle operazioni seguenti:

   - Per creare un nuovo certificato, selezionare **Nuovo certificato**, selezionare un valore per **Data di scadenza** e quindi selezionare **Salva**. Per attivare il certificato, selezionare il menu di scelta rapida **(...)** e scegliere **Rendi attivo il certificato**.
   - Per caricare un certificato con la chiave privata e le credenziali PFX, selezionare **Importa certificato** e passare al certificato. Immettere la **Password PFX** e quindi selezionare **Aggiungi**.  
   - Per configurare le opzioni avanzate di firma del certificato, usare le opzioni seguenti. Per le descrizioni di queste opzioni, vedere l'articolo [Opzioni avanzate di firma del certificato](certificate-signing-options.md).
      - Nell'elenco a discesa **Opzioni di firma** scegliere **Firma risposta SAML**, **Firma asserzione SAML** o **Firma risposta e asserzione SAML**.
      - Nell'elenco a discesa **Algoritmo di firma** scegliere **SHA-1** o **SHA-256**.
   - Per notificare ad altre persone quando il certificato attivo è prossimo alla data di scadenza, immettere gli indirizzi di posta elettronica nei campi **Indirizzi di posta elettronica per le notifiche**.

4. In caso di modifiche, selezionare **Salva** nella parte superiore della sezione **Certificato di firma SAML**. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passaggio 4. Configurare l'applicazione per l'uso di Azure AD

La sezione **Configura \<applicationName> ** elenca i valori che devono essere configurati nell'applicazione in modo da usare Azure ad come provider di identità SAML. I valori richiesti variano in base all'applicazione. Per informazioni dettagliate, vedere la documentazione sul protocollo SAML dell'applicazione. Per trovare la documentazione, passare all'intestazione di **configurazione \<application name> ** e selezionare **Visualizza istruzioni dettagliate**. La documentazione viene visualizzata nella pagina **Configura accesso**. Questa pagina consente di compilare i ** \<application name> ** valori **URL di accesso**, **identificatore Azure ad**e **URL di disconnessione** nell'intestazione Configura.

1. Scorrere verso il basso fino alla sezione **Configurare \<applicationName>**. 
   
   ![Passaggio 4 Configurare un'applicazione](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copiare il valore di ogni riga di questa sezione secondo necessità e seguire le istruzioni specifiche dell'applicazione per aggiungerlo all'applicazione. Per le app della raccolta, è possibile visualizzare la documentazione selezionando **Visualizzare le istruzioni dettagliate**. 
   - I valori di **URL di accesso** e **URL di disconnessione** restituiscono entrambi lo stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per questa istanza di Azure AD. 
   - **Identificatore Azure AD** è il valore di **Issuer** nel token SAML inviato all'applicazione.
2. Dopo avere incollato tutti i valori nei campi appropriati, selezionare **Salva**.

## <a name="step-5-validate-single-sign-on"></a>Passaggio 5. Convalidare l'accesso Single Sign-On

Dopo aver configurato l'applicazione per l'uso di Azure AD come provider di identità basato su SAML, è possibile testare le impostazioni per verificare che l'accesso Single Sign-On funzioni per l'account. 

2. Scorrere fino alla sezione **Verificare l'accesso Single Sign-On con <applicationName>** .

   ![Passaggio 5 Convalidare l'accesso Single Sign-On](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selezionare **Convalida**. Verranno visualizzate le opzioni di test.

4. Selezionare **Accedi con l'account utente corrente**. 

Se l'accesso riesce, è possibile assegnare utenti e gruppi all'applicazione SAML.
Se viene visualizzato un messaggio di errore, completare la procedura seguente:

1. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?** .

    ![Ottieni procedure per la risoluzione](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selezionare **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.

3. Leggere le procedure per la risoluzione e quindi, se possibile, risolvere il problema.

4. Eseguire di nuovo il test finché non viene completato correttamente.

Per altre informazioni, vedere [Eseguire il debug di Single Sign-On basato su SAML per applicazioni in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
