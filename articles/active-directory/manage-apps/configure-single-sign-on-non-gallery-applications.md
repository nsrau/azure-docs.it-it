---
title: Single Sign-On SAML-applicazioni non della raccolta-piattaforma di identità Microsoft | Microsoft Docs
description: Configurare Single Sign-On (SSO) per le applicazioni non della raccolta in Microsoft Identity Platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dcc2d6fc252f288f15e2583012798b4d0e9cee6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169446"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configurare Single Sign-On basate su SAML per applicazioni non della raccolta

Quando si [aggiunge un'app della raccolta](add-gallery-app.md) o un' [app Web non della raccolta](add-non-gallery-app.md) alle applicazioni aziendali Azure ad, una delle opzioni di Single Sign-on disponibili è [Single Sign-on basata su SAML](what-is-single-sign-on.md#saml-sso). Scegliere SAML laddove possibile per le applicazioni che eseguono l'autenticazione usando uno dei protocolli SAML. Con l'accesso Single Sign-On SAML, Azure AD esegue l'autenticazione all'applicazione usando l'account Azure AD dell'utente. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. È possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML. Questo articolo descrive come configurare l'accesso Single Sign-On basato su SAML per un'app non della raccolta. 

> [!NOTE]
> Si vuole aggiungere un'app della raccolta? Seguire le istruzioni dettagliate nell'[elenco di esercitazioni sulle app SaaS](../saas-apps/tutorial-list.md)

Per configurare SAML Single Sign-On per un'applicazione non raccolta senza scrivere codice, è necessario disporre di una sottoscrizione o Azure AD Premium e l'applicazione deve supportare SAML 2,0. Per altre informazioni sulle versioni di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Prima di iniziare

Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [aggiungere un'app non della raccolta](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passaggio 1. Modificare la configurazione SAML di base

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

2. Passare a **Azure Active Directory** > **Applicazioni aziendali** e selezionare l'applicazione nell'elenco. 
   
   - Per cercare l'applicazione, nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**. Immettere il nome dell'applicazione nella casella di ricerca, quindi selezionarla nei risultati.

3. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

4. Selezionare **SAML**. Viene visualizzata la pagina **Configura l'accesso Single Sign-On con SAML - Anteprima**.

   ![Passaggio 1 modificare la configurazione SAML di base](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Per modificare le opzioni di configurazione SAML di base, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Configurazione SAML di base**.

1. Immettere le impostazioni seguenti. È necessario ottenere i valori dal fornitore dell'applicazione. È possibile immettere manualmente i valori o caricare un file di metadati per estrarre il valore dei campi.

    | Impostazione della configurazione SAML di base | SSO avviato da provider di servizi | SSO avviato da IdP | DESCRIZIONE |
    |:--|:--|:--|:--|
    | **Identificatore (ID entità)** | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica in modo univoco l'applicazione. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML. L'applicazione dovrebbe convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione. Immettere un URL che usa il modello seguente:' https://<subdomain>. contoso.com ' *è possibile trovare questo valore come elemento **Issuer** in **AuthnRequest** (richiesta SAML) inviato dall'applicazione.* |
    | **URL di risposta** | obbligatori | obbligatori | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. È possibile usare i campi aggiuntivi URL di risposta per specificare più URL di risposta. Ad esempio, potrebbero essere necessari URL di risposta aggiuntivi per più sottodomini. In alternativa, a scopo di test è possibile specificare contemporaneamente più URL di risposta (host locale e URL pubblici). |
    | **URL di accesso** | obbligatori | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Office 365 o dal pannello di accesso di Azure AD. Se è vuoto, Azure AD esegue l'accesso avviato da IdP quando un utente avvia l'applicazione da Office 365, dal pannello di accesso Azure AD o dall'URL Azure AD SSO.|
    | **Stato inoltro** | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. In genere il valore è un URL valido per l'applicazione. Tuttavia, alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.
    | **URL di disconnessione** | Facoltativo | Facoltativo | Usato per restituire una risposta di disconnessione SAML all'applicazione.

Per ulteriori informazioni, vedere [protocollo SAML per Single Sign-on](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Passaggio 2. Configurare attributi e attestazioni utente 

Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia all'applicazione un token SAML con informazioni (o attestazioni) sull'utente che li identificano in modo univoco. Per impostazione predefinita, queste informazioni includono il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. Potrebbe essere necessario personalizzare queste attestazioni se, ad esempio, l'applicazione richiede valori di attestazione specifici o un formato di **nome** diverso da username. I requisiti per le app della raccolta sono descritti nelle [esercitazioni specifiche dell'applicazione](../saas-apps/tutorial-list.md) oppure è possibile rivolgersi al fornitore dell'applicazione. I passaggi generali per la configurazione degli attributi e delle attestazioni utente sono descritti di seguito.

1. Nell'angolo in alto a destra della sezione **Attributi e attestazioni utente** selezionare l'icona **Modifica** (a forma di matita).

   ![Passaggio 2 configurare gli attributi utente e le attestazioni](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verificare il **Valore identificatore nome**. Il valore predefinito è *user.principalname*. L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

3. Per modificare il **Valore identificatore nome**, selezionare l'icona **Modifica** (a forma di matita) per il campo **Valore identificatore nome** campo. Apportare le modifiche appropriate al formato e all'origine dell'identificatore in base alle esigenze. Per informazioni dettagliate, vedere [Modifica di NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Al termine, salvare le modifiche. 
 
4. Per configurare le attestazioni di gruppi, selezionare l'icona **Modifica** relativa al campo **Gruppi restituiti nell'attestazione**. Per informazioni dettagliate, vedere [Configurare le attestazioni di gruppi](../hybrid/how-to-connect-fed-group-claims.md).

5. Per aggiungere un'attestazione, selezionare **Aggiungi nuova attestazione** nella parte superiore della pagina. Immettere il **Nome** e selezionare l'origine appropriata. Se si seleziona l'origine **Attributo**, è necessario scegliere l'**Attributo di origine** da usare. Se si seleziona l'origine **Traduzione**, è necessario scegliere la **Trasformazione** e il **Parametro 1** da usare. Per informazioni dettagliate, vedere [Aggiunta di attestazioni specifiche dell'applicazione](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Al termine, salvare le modifiche. 

6. Selezionare **Salva**. Nella tabella viene visualizzata la nuova attestazione.

   > [!NOTE]
   > Per informazioni sugli altri modi disponibili per personalizzare il token SAML di Azure AD nell'applicazione, vedere le risorse seguenti.
   >- Per creare ruoli personalizzati tramite il portale di Azure, vedere [Configurare le attestazioni dei ruoli](../develop/active-directory-enterprise-app-role-management.md).
   >- Per personalizzare le attestazioni tramite PowerShell, vedere [Personalizzare le attestazioni - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Per modificare il manifesto dell'applicazione e configurare attestazioni facoltative per l'applicazione, vedere [Configurare le attestazioni facoltative](../develop/active-directory-optional-claims.md).
   >- Per impostare i criteri di durata per i token di aggiornamento, i token di accesso, i token di sessione e i token ID, vedere [Configurare la durata dei token](../develop/active-directory-configurable-token-lifetimes.md). In alternativa, per limitare le sessioni di autenticazione tramite Accesso condizionale di Azure AD, vedere [Funzionalità di gestione delle sessioni di autenticazione](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passaggio 3. Gestire il certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. Per configurare il trust tra Azure AD e l'applicazione, è necessario questo certificato. Per informazioni dettagliate sul formato del certificato, vedere la documentazione sul protocollo SAML dell'applicazione. Per altre informazioni, vedere [Manage Certificates for federate Single Sign-on](manage-certificates-for-federated-single-sign-on.md) e [Advanced Certificate Signing Options nel token SAML](certificate-signing-options.md).

Da Azure AD è possibile scaricare il certificato attivo in formato Base64 o RAW direttamente dalla pagina principale **configurare l'accesso Single Sign-on con SAML** . In alternativa, è possibile ottenere il certificato attivo scaricando il file XML dei metadati dell'applicazione o usando l'URL dei metadati di Federazione dell'app. Per visualizzare, creare o scaricare i certificati (attivi o inattivi), seguire questa procedura.

1. Passare alla sezione **Certificato di firma SAML**. 

   ![Passaggio 3 gestione del certificato di firma SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Verificare che il certificato abbia:

   - *Data di scadenza desiderata.* È possibile configurare la data di scadenza per un massimo di tre anni nel futuro.
   - *Stato attivo per il certificato desiderato.* Se lo stato è **inattivo**, impostare lo stato su **attivo**. Per modificare lo stato, fare clic con il pulsante destro del mouse sulla riga del certificato desiderato e selezionare **Rendi attivo il certificato**.
   - *Opzione e algoritmo di firma corretti.*
   - *Indirizzi di posta elettronica di notifica corretti.* Quando il certificato attivo si avvicina alla data di scadenza, Azure AD invia una notifica all'indirizzo di posta elettronica configurato in questo campo.

2. Per scaricare il certificato, selezionare una delle opzioni per formato Base64, formato non elaborato o XML metadati federazione. Azure AD fornisce anche l'**URL dei metadati della federazione dell'app** tramite cui è possibile accedere ai metadati specifici dell'applicazione nel formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Per gestire, creare o importare un certificato, selezionare l'icona di **modifica** (una matita) nell'angolo superiore destro della sezione certificato di **firma SAML** .

   ![Certificato di firma SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Eseguire una delle azioni seguenti:

   - Per creare un nuovo certificato, selezionare **Nuovo certificato**, selezionare un valore per **Data di scadenza** e quindi selezionare **Salva**. Per attivare il certificato, selezionare il menu di scelta rapida **(...)** e scegliere **Rendi attivo il certificato**.
   - Per caricare un certificato con la chiave privata e le credenziali PFX, selezionare **Importa certificato** e passare al certificato. Immettere la **Password PFX** e quindi selezionare **Aggiungi**.  
   - Per configurare le opzioni avanzate di firma del certificato, usare le opzioni seguenti. Per le descrizioni di queste opzioni, vedere l'articolo [Opzioni avanzate di firma del certificato](certificate-signing-options.md).
      - Nell'elenco a discesa **Opzioni di firma** scegliere **Firma risposta SAML**, **Firma asserzione SAML** o **Firma risposta e asserzione SAML**.
      - Nell'elenco a discesa **Algoritmo di firma** scegliere **SHA-1** o **SHA-256**.
   - Per notificare ad altre persone quando il certificato attivo è prossimo alla data di scadenza, immettere gli indirizzi di posta elettronica nei campi **Indirizzi di posta elettronica per le notifiche**.

4. Se sono state apportate modifiche, selezionare **Salva** nella parte superiore della sezione **certificato di firma SAML** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passaggio 4. Configurare l'applicazione per l'uso di Azure AD

La sezione **Configura \<NomeApplicazione>** contiene un elenco dei valori che è necessario configurare nell'applicazione in modo che usi Azure AD come provider di identità SAML. I valori richiesti variano in base all'applicazione. Per informazioni dettagliate, vedere la documentazione sul protocollo SAML dell'applicazione. Per trovare la documentazione, passare all'intestazione **imposta \<nome dell'applicazione >** e selezionare **Visualizza istruzioni dettagliate**. La documentazione viene visualizzata nella pagina **Configura accesso** . Questa pagina consente di compilare i valori **URL di accesso**, **identificatore Azure ad**e **URL di disconnessione** nell'intestazione **Configura \<nome dell'applicazione >** .

1. Scorrere verso il basso fino alla sezione **Configurare \<applicationName>** . 
   
   ![Passaggio 4 configurare l'applicazione](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copiare il valore di ogni riga di questa sezione secondo necessità e seguire le istruzioni specifiche dell'applicazione per aggiungerlo all'applicazione. Per le app della raccolta, è possibile visualizzare la documentazione selezionando **Visualizzare le istruzioni dettagliate**. 
   - I valori di **URL di accesso** e **URL di disconnessione** restituiscono entrambi lo stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per questa istanza di Azure AD. 
   - **Identificatore Azure AD** è il valore di **Issuer** nel token SAML inviato all'applicazione.
2. Dopo avere incollato tutti i valori nei campi appropriati, selezionare **Salva**.

## <a name="step-5-validate-single-sign-on"></a>Passaggio 5. Convalidare l'accesso Single Sign-On

Dopo aver configurato l'applicazione per l'uso di Azure AD come provider di identità basato su SAML, è possibile verificare le impostazioni per verificare se Single Sign-On funziona per l'account. 

2. Scorrere fino alla sezione **Verificare l'accesso Single Sign-On con <applicationName>** .

   ![Passaggio 5 convalidare Single Sign-On](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selezionare **Convalida**. Verranno visualizzate le opzioni di test.

4. Selezionare **Accedi con l'account utente corrente**. 

Se l'accesso riesce, è possibile assegnare utenti e gruppi all'applicazione SAML.
Se viene visualizzato un messaggio di errore, completare la procedura seguente:

1. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?** .

    ![Ottieni procedure per la risoluzione](media/configure-single-sign-on-portal/error-guidance.png)

2. Selezionare **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.

3. Leggere le procedure per la risoluzione e quindi, se possibile, risolvere il problema.

4. Eseguire di nuovo il test finché non viene completato correttamente.

Per altre informazioni, vedere [eseguire il debug di Single Sign-on basate su SAML per le applicazioni Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](configure-automatic-user-provisioning-portal.md)
