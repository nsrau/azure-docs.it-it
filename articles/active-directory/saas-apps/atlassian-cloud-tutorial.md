---
title: 'Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7690977f23b193bb3ba282df14d348b3fac0ad6b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457538"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Esercitazione: Integrare Atlassian Cloud con Azure Active Directory

Questa esercitazione descrive come integrare Atlassian Cloud con Azure Active Directory (Azure AD). Integrando Atlassian Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Atlassian Cloud.
* Abilitare gli utenti per l'accesso automatico ad Atlassian Cloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Atlassian Cloud abilitata per l'accesso Single Sign-On (SSO).
* Per abilitare l'accesso Single Sign-On SAML (Security Assertion Markup Language) per i prodotti Atlassian Cloud, è necessario configurare l'accesso Atlassian. Altre informazioni sull'[accesso Atlassian]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

* Atlassian Cloud supporta l'accesso SSO avviato da **SP e IdP**
* Atlassian Cloud supporta [provisioning e deprovisioning utenti automatici](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Aggiunta di Atlassian Cloud dalla raccolta

Per configurare l'integrazione di Atlassian Cloud in Azure AD, è necessario aggiungere Atlassian Cloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Atlassian Cloud** nella casella di ricerca.
1. Selezionare **Atlassian Cloud** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Atlassian Cloud usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Atlassian Cloud.

Per configurare e testare l'accesso SSO di Azure AD con Atlassian Cloud, completare i passaggi seguenti:

1. **[Configurare l'accesso SSO di Azure AD con Atlassian Cloud](#configure-azure-ad-sso)** : per consentire agli utenti di usare l'accesso SSO SAML basato su Azure AD con Atlassian Cloud.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare un utente di test di Atlassian Cloud](#create-atlassian-cloud-test-user)** : per avere una controparte di B.Simon in Atlassian Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Per automatizzare la configurazione all'interno di Atlassian Cloud, è necessario installare l' **estensione del browser MyApps per l'accesso sicuro** facendo clic su **Installa l'estensione** .

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Atlassian Cloud** per passare direttamente all'applicazione Atlassian Cloud. Da qui, fornire le credenziali di amministratore per accedere ad Atlassian Cloud. L'estensione del browser configurerà automaticamente l'applicazione.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Atlassian Cloud, accedere al sito aziendale di Atlassian Cloud come amministratore e completare la procedura seguente.

1. Prima di iniziare, passare all'istanza del prodotto Atlassian e copiare/salvare l'URL dell'istanza
   > [!NOTE]
   > L'URL deve essere conforme al formato `https://<instancename>.atlassian.net`

   ![nome istanza](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Aprire il [portale di amministrazione di Atlassian](https://admin.atlassian.com/) e fare clic sul nome dell'organizzazione

   ![Organizzazione](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. È necessario verificare il dominio prima di passare alla configurazione dell'accesso Single Sign-On. Per altre informazioni, vedere il documento [Atlassian domain verification](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) (Verifica del dominio di Atlassian).
1. Nella schermata del portale di amministrazione di Atlassian selezionare **Security** (Sicurezza) nel riquadro a sinistra

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Nella schermata Security (Sicurezza) del portale di amministrazione di Atlassian selezionare **SAML single sign** (Accesso Single Sign-On SAML) nel riquadro a sinistra

   ![Opzione SAML single sign](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Fare clic su **Add SAML Configuration** (Aggiungi configurazione SAML) e lasciare aperta la pagina

   ![Opzione Add SAML Configuration](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![Opzione Add SAML Configuration 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. Nella pagina di integrazione dell'applicazione **Atlassian Cloud** del portale di Azure individuare la sezione **Gestione** e selezionare **Set up single sign-on** (Configura l'accesso Single Sign-On).

   ![Opzione Set up single sign-on](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .

   ![SAML in Azure](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** scorrere verso il basso fino a **Configura Atlassian Cloud**
   
   a. Fare clic su **URL di configurazione**

   ![urls](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Copiare il valore di **Identificatore Azure AD** dal portale di Azure e incollarlo nella casella di testo **Identity provider Entity ID** (ID entità del provider di identità) in Atlassian
   
   c. Copiare il valore di **URL di accesso** dal portale di Azure e incollarlo nella casella di testo **Identity Provider SSO URL** (URL di accesso SSO del provider di identità) in Atlassian

   ![Opzione Identity Provider SSO URL](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![Opzione Entity ID](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** , trovare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Certificato di firma](./media/atlassian-cloud-tutorial/certificate.png)

   ![Certificato 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. Fare clic su **Add/Save** (Aggiungi/Salva) per aggiungere o salvare la configurazione SAML in Atlassian

1. Se si vuole configurare l'applicazione in modalità avviata da **IDP** , modificare la sezione **Configurazione SAML di base** della pagina **Configura l'accesso Single Sign-On con SAML** in Azure e aprire la **pagina per l'accesso Single Sign-On SAML** nel portale di amministrazione di Atlassian

   a. Copiare il valore di **SP Entity ID** (ID entità SP) da Atlassian, incollarlo nella casella **Identificatore (ID entità)** in Azure e impostarlo come predefinito
   
   b. Copiare il valore di **SP Assertion Consumer Service URL** (URL del servizio consumer di asserzione SP) da Atlassian, incollarlo nella casella **URL di risposta (URL del servizio consumer di asserzione)** in Azure e impostarlo come predefinito
   
   c. Copiare il valore di **Instance URL** (URL istanza), copiato nel passaggio 1, e incollarlo nella casella **Stato dell'inoltro** in Azure

   ![Copia degli URL](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![Pulsante di modifica](./media/atlassian-cloud-tutorial/edit-button.png)

   ![Immagine degli URL](./media/atlassian-cloud-tutorial/urls.png)
   
1. Se si vuole configurare l'applicazione in modalità avviata da **IDP** , modificare la sezione **Configurazione SAML di base** della pagina **Configura l'accesso Single Sign-On con SAML** in Azure. Copiare il valore di **Instance URL** (URL istanza) dal passaggio 1 e incollarlo nella casella **URL di accesso** in Azure

   ![Pulsante di modifica negli URL](./media/atlassian-cloud-tutorial/edit-button.png)

   ![URL di accesso](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. L'applicazione Atlassian Cloud prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Per modificare il mapping degli attributi, fare clic sull'icona **Modifica** . 

   ![attributes](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. Mapping degli attributi per un tenant di Azure AD con una licenza di Microsoft 365
      
      a. Fare clic sull'attestazione **Identificatore utente univoco (ID nome)**

      ![Attributi e attestazioni](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud prevede il mapping di **nameidentifier** ( **Identificatore univoco dell'utente** ) all'indirizzo di posta elettronica dell'utente ( **user.email** ). Modificare il valore di **Attributo di origine** impostandolo su **user.mail** . Salvare le modifiche apportate all'attestazione.

      ![ID utente univoco](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. I mapping finali degli attributi dovrebbero essere simili ai seguenti.

      ![immagine 2](common/default-attributes.png)
      
   1. Mapping degli attributi per un tenant di Azure AD senza la licenza di Microsoft 365 

      a. Fare clic sull'attestazione `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

      ![immagine 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Azure non popola l'attributo **user.mail** per gli utenti creati in tenant di Azure AD senza licenze di Microsoft 365 e archivia l'indirizzo di posta elettronica per tali utenti nell'attributo **userprincipalname** . Atlassian Cloud prevede il mapping di **nameidentifier** ( **Identificatore univoco dell'utente** ) all'indirizzo di posta elettronica dell'utente ( **user.userprincipalname** ).  Modificare il valore di **Attributo di origine** impostandolo su **user.userprincipalname** . Salvare le modifiche apportate all'attestazione.

      ![Impostazione dell'indirizzo di posta elettronica](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. I mapping finali degli attributi dovrebbero essere simili ai seguenti.

      ![Immagine 4](common/default-attributes.png)
     
### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Atlassian Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Atlassian Cloud** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo** . Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

### <a name="create-atlassian-cloud-test-user"></a>Creare l'utente di test di Atlassian Cloud

Per consentire agli utenti di Azure AD di accedere ad Atlassian Cloud, effettuare il provisioning manuale degli account utente in Atlassian Cloud seguendo questa procedura:

1. Nel riquadro **Administration** (Amministrazione) selezionare **Users** (Utenti).

    ![Collegamento Users (Utenti) di Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Per creare un utente in Atlassian Cloud, selezionare **Invite user** (Invita l'utente).

    ![Creare un utente di Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Nella casella **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente e quindi assegnare l'accesso all'applicazione.

    ![Utente di Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Per inviare un invito di posta elettronica all'utente, selezionare **Invite users** (Invita gli utenti). Un invito di posta elettronica viene inviato all'utente e, dopo avere accettato l'invito, l'utente è attivo nel sistema.

> [!NOTE]
> È anche possibile creare gli utenti in blocco selezionando il pulsante **Bulk Create** (Creazione in blocco) nella sezione **Users** (Utenti).

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Atlassian Cloud, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Atlassian Cloud e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Atlassian Cloud per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Atlassian Cloud nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Atlassian Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Atlassian Cloud, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).