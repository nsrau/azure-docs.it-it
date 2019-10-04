---
title: Configurare l'accesso Single Sign-On - Azure Active Directory | Microsoft Docs
description: Questa esercitazione usa il portale di Azure per configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: c5e8ed4a78fccce4f3a5c631a99a8729114e5722
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422596"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Come configurare l'accesso Single Sign-On basato su SAML

Dopo aver aggiunto un'app alle applicazioni aziendali di Azure AD, è possibile configurare le impostazioni di Single Sign-on. Questo articolo descrive come configurare l'accesso Single Sign-On basato su SAML per un'app non della raccolta. 

> [!NOTE]
> Si vuole aggiungere un'app della raccolta? Seguire le istruzioni dettagliate nell'[elenco di esercitazioni sulle app SaaS](../saas-apps/tutorial-list.md)

Per configurare l'accesso Single Sign-On per un'applicazione non inclusa nella raccolta *senza scrivere codice*, è necessario disporre di una sottoscrizione Azure AD Premium e l'applicazione deve supportare SAML 2.0. Per altre informazioni sulle versioni di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Prima di iniziare

- Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [Aggiungere un'app della raccolta](add-gallery-app.md) oppure [Aggiungere un'app non della raccolta](add-non-gallery-app.md).
- Contattare il fornitore dell'applicazione per ottenere le informazioni corrette per le impostazioni seguenti:

    | Impostazione della configurazione SAML di base | SSO avviato da provider di servizi | SSO avviato da IdP | DESCRIZIONE |
    |:--|:--|:--|:--|
    | Identificatore (ID entità) | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML. L'applicazione dovrebbe convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione. *È possibile trovare questo valore come elemento **Issuer** in nella richiesta **AuthRequest** (SAML) inviata dall'applicazione.* |
    | URL di risposta | Facoltativo | Obbligatoria | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. |
    | URL di accesso | Obbligatoria | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Office 365 o dal pannello di accesso di Azure AD. Se si omette, Azure AD si basa sul provider di identità per avviare l'accesso Single Sign-On quando un utente avvia l'applicazione.|
    | Stato dell'inoltro | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. In genere il valore è un URL valido per l'applicazione. Tuttavia, alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.
    | URL di chiusura sessione | Facoltativo | Facoltativo | Usato per restituire una risposta di disconnessione SAML all'applicazione.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passaggio 1. Modificare la configurazione SAML di base

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

1. Passare a **Azure Active Directory** > **Applicazioni aziendali** e selezionare l'applicazione nell'elenco. 
   
   - Per cercare l'applicazione, nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**. Immettere il nome dell'applicazione nella casella di ricerca, quindi selezionarla nei risultati.

1. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

1. Selezionare **SAML**. Viene visualizzata la pagina **Configura l'accesso Single Sign-On con SAML - Anteprima**.

1. Per modificare le opzioni di configurazione SAML di base, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Configurazione SAML di base**.

     ![Configurare i certificati](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Immettere nei campi appropriati le informazioni descritte nella sezione [Prima di iniziare](#before-you-begin).

1. Nella parte superiore della pagina selezionare **Salva**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Passaggio 2. Configurare attributi e attestazioni utente 

Un'applicazione potrebbe richiedere specifici attributi o attestazioni utente nel token SAML che riceve da Azure AD quando un utente accede. Ad esempio, potrebbero essere richiesti specifici URI o valori di attestazione oppure per **Nome** potrebbe essere necessario immettere qualcosa di diverso rispetto al nome utente archiviato in Microsoft Identity Platform. I requisiti per le app della raccolta sono descritti nelle [esercitazioni specifiche dell'applicazione](../saas-apps/tutorial-list.md) oppure è possibile rivolgersi al fornitore dell'applicazione. I passaggi generali per la configurazione degli attributi e delle attestazioni utente sono descritti di seguito.

1. Nell'angolo in alto a destra della sezione **Attributi e attestazioni utente** selezionare l'icona **Modifica** (a forma di matita).

1. Verificare il **Valore identificatore nome**. Il valore predefinito è *user.principalname*. L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

1. Per modificare il **Valore identificatore nome**, selezionare l'icona **Modifica** (a forma di matita) per il campo **Valore identificatore nome** campo. Apportare le modifiche appropriate al formato e all'origine dell'identificatore in base alle esigenze. Per informazioni dettagliate, vedere [Modifica di NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Al termine, salvare le modifiche. 
 
1. Per configurare le attestazioni di gruppi, selezionare l'icona **Modifica** relativa al campo **Gruppi restituiti nell'attestazione**. Per informazioni dettagliate, vedere [Configurare le attestazioni di gruppi](../hybrid/how-to-connect-fed-group-claims.md).

3. Per aggiungere un'attestazione, selezionare **Aggiungi nuova attestazione** nella parte superiore della pagina. Immettere il **Nome** e selezionare l'origine appropriata. Se si seleziona l'origine **Attributo**, è necessario scegliere l'**Attributo di origine** da usare. Se si seleziona l'origine **Traduzione**, è necessario scegliere la **Trasformazione** e il **Parametro 1** da usare. Per informazioni dettagliate, vedere [Aggiunta di attestazioni specifiche dell'applicazione](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Al termine, salvare le modifiche. 

4. Selezionare **Salva**. Nella tabella viene visualizzata la nuova attestazione.

   > [!NOTE]
   > Per informazioni sugli altri modi disponibili per personalizzare il token SAML di Azure AD nell'applicazione, vedere le risorse seguenti.
   >- Per creare ruoli personalizzati tramite il portale di Azure, vedere [Configurare le attestazioni dei ruoli](../develop/active-directory-enterprise-app-role-management.md).
   >- Per personalizzare le attestazioni tramite PowerShell, vedere [Personalizzare le attestazioni - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Per modificare il manifesto dell'applicazione e configurare attestazioni facoltative per l'applicazione, vedere [Configurare le attestazioni facoltative](../develop/active-directory-optional-claims.md).
   >- Per impostare i criteri di durata per i token di aggiornamento, i token di accesso, i token di sessione e i token ID, vedere [Configurare la durata dei token](../develop/active-directory-configurable-token-lifetimes.md). In alternativa, per limitare le sessioni di autenticazione tramite Accesso condizionale di Azure AD, vedere [Funzionalità di gestione delle sessioni di autenticazione](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passaggio 3. Gestire il certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** è possibile visualizzare o scaricare il certificato attivo. È anche possibile aggiornare, creare o importare un certificato. Per le applicazioni della raccolta, i dettagli relativi al formato del certificato sono disponibili nella documentazione SAML dell'applicazione. Vedere le [esercitazioni specifiche dell'applicazione](../saas-apps/tutorial-list.md). 

1. Passare alla sezione **Certificato di firma SAML**. A seconda del tipo di applicazione, verranno visualizzate le opzioni per scaricare il certificato in formato Base64, in formato non elaborato o come XML dei metadati della federazione. Azure AD fornisce anche l'**URL dei metadati della federazione dell'app** tramite cui è possibile accedere ai metadati specifici dell'applicazione nel formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Per gestire, creare o importare un certificato, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Certificato di firma SAML**, quindi eseguire una delle operazioni seguenti:

   - Per creare un nuovo certificato, selezionare **Nuovo certificato**, selezionare un valore per **Data di scadenza** e quindi selezionare **Salva**. Per attivare il certificato, selezionare il menu di scelta rapida **(...)** e scegliere **Rendi attivo il certificato**.
   - Per caricare un certificato con la chiave privata e le credenziali PFX, selezionare **Importa certificato** e passare al certificato. Immettere la **Password PFX** e quindi selezionare **Aggiungi**.  
   - Per configurare le opzioni avanzate di firma del certificato, usare le opzioni seguenti. Per le descrizioni di queste opzioni, vedere l'articolo [Opzioni avanzate di firma del certificato](certificate-signing-options.md).
      - Nell'elenco a discesa **Opzioni di firma** scegliere **Firma risposta SAML**, **Firma asserzione SAML** o **Firma risposta e asserzione SAML**.
      - Nell'elenco a discesa **Algoritmo di firma** scegliere **SHA-1** o **SHA-256**.
   - Per notificare ad altre persone quando il certificato attivo è prossimo alla data di scadenza, immettere gli indirizzi di posta elettronica nei campi **Indirizzi di posta elettronica per le notifiche**.

1. Selezionare **Salva** nella parte superiore della sezione **Certificato di firma SAML**. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passaggio 4. Configurare l'applicazione per l'uso di Azure AD

La sezione **Configura \<NomeApplicazione>** contiene un elenco dei valori che è necessario configurare nell'applicazione in modo che usi Azure AD come provider di identità SAML. I valori richiesti variano in base all'applicazione. Per informazioni dettagliate, vedere la documentazione sul protocollo SAML dell'applicazione.

1. Scorrere verso il basso fino alla sezione **Configurare \<applicationName>** . 
2. Copiare il valore di ogni riga di questa sezione secondo necessità e seguire le istruzioni specifiche dell'applicazione per aggiungerlo all'applicazione. Per le app della raccolta, è possibile visualizzare la documentazione selezionando **Visualizzare le istruzioni dettagliate**. 
   - I valori di **URL di accesso** e **URL di disconnessione** restituiscono entrambi lo stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per questa istanza di Azure AD. 
   - **Identificatore Azure AD** è il valore di **Issuer** nel token SAML inviato all'applicazione.
1. Dopo avere incollato tutti i valori nei campi appropriati, selezionare **Salva**.

## <a name="step-5-validate-single-sign-on"></a>Passaggio 5. Convalidare l'accesso Single Sign-On

È ora possibile testare le impostazioni per verificare se l'accesso Single Sign-On funziona per l'amministratore.  

1. Aprire le impostazioni dell'accesso Single Sign-On per l'applicazione. 
2. Scorrere fino alla sezione **Verificare l'accesso Single Sign-On con <applicationName>** . Per questa esercitazione, tale sezione viene chiamata **Configurare GitHub-test**.
3. Selezionare **Test**. Verranno visualizzate le opzioni di test.
4. Selezionare **Accedi con l'account utente corrente**. 

Se l'accesso riesce, è possibile assegnare utenti e gruppi all'applicazione SAML.
Se viene visualizzato un messaggio di errore, completare la procedura seguente:

1. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?** .

    ![Usare la casella "Qual è l'errore riscontrato?" per ottenere indicazioni per la risoluzione](media/configure-single-sign-on-portal/error-guidance.png)

1. Selezionare **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.
1. Leggere le procedure per la risoluzione e quindi, se possibile, risolvere il problema.
1. Eseguire di nuovo il test finché non viene completato correttamente.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](configure-automatic-user-provisioning-portal.md)
