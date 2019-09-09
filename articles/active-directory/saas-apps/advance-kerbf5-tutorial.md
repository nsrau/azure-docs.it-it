---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5 | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e8e6a9eb50d15e6e4eb788fefe13d7e5983a834
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165474"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5

Questa esercitazione descrive come integrare F5 con Azure Active Directory (Azure AD). Integrando F5 con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a F5.
* Abilitare gli utenti per l'accesso automatico a F5 con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di F5 abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* F5 supporta l'accesso SSO avviato da **SP e IDP**
* L'accesso SSO di F5 può essere configurato in tre diversi modi.

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](headerf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Aggiunta di F5 dalla raccolta

Per configurare l'integrazione di F5 in Azure AD è necessario aggiungere F5 dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **F5** nella casella di ricerca.
1. Selezionare **F5** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per F5

Configurare e testare l'accesso SSO di Azure AD con F5 usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in F5.

Per configurare e testare l'accesso SSO di Azure AD con F5, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di F5](#configure-f5-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di F5](#create-f5-test-user)** : per avere una controparte di B.Simon in F5 collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **F5** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<YourCustomFQDN>.f5.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<YourCustomFQDN>.f5.com/`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura F5** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a F5.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **F5**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-f5-sso"></a>Configurare l'accesso Single Sign-On di F5

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](headerf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos

1. Aprire una nuova finestra del Web browser, accedere al sito aziendale di F5 (Advanced Kerberos) come amministratore e seguire questa procedura:

1. È necessario importare il Certificato dei metadati in F5 (Advanced Kerberos) che verrà usato più avanti nel processo di configurazione. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Fare clic su **Import** (Importa) nell'angolo destro.

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Per configurare l'IDP SAML, passare a **Access > Federation > SAML Service Provider > Create > From Metadata** (Accesso > Federazione > Provider di servizi SAML > Crea > Da metadati).

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Specificare il Certificato caricato dall'attività 3

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Per configurare il provider di servizi SAML, passare a **Access > Federation > SAML Service Federation > Local SP Services > Create** (Accesso > Federazione > Federazione di servizi SAML > Servizi SP locali > Crea).

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Fare clic su **OK**.

1. Selezionare la configurazione SP e fare clic su **Bind/UnBind IdP Connectors** (Associa/Dissocia connettori IdP).

     ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Fare clic su **Add New Row** (Aggiungi nuova riga) e selezionare il valore di **External IdP connector** (Connettore IdP esterno) creato nel passaggio precedente.

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Per la configurazione dell'accesso SSO di Kerberos, scegliere **Access > Single Sign-on > Kerberos** (Accesso > Single Sign-On > Kerberos)

    >[!Note]
    >Sarà necessario creare e specificare l'account di delega Kerberos. Fare riferimento alla sezione KCD (vedere l'appendice relativa ai riferimenti per le variabili)

    •   Origine nome utente  `session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   Origine area di autenticazione utente  `session.logon.last.domain`

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. Per la configurazione del profilo di accesso, specificare **Access > Profile/Policies > Access Profile (per session policies)** (Accesso > Profilo/Criteri > Profilo di accesso (criteri per sessione)).

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Per l'aggiunta di un nuovo nodo, passare a **Local Traffic > Nodes > Node List > +** (Traffico locale > Nodi > Elenco nodi > +).

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Per creare un nuovo pool, passare a **Local Traffic > Pool > Pool List > +** (Traffico locale > Pool > Elenco pool > Crea).

     ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Per creare un nuovo server virtuale, passare a **Local Traffic > Virtual Servers > Virtual Server List > +** (Traffico locale > Server virtuale > Elenco server virtuali > +).

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Specificare il profilo di accesso creato nel passaggio precedente.

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Configurazione della delega Kerberos 

>[!Note]
>Per altri dettagli, vedere [qui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Passaggio 1: Creare un account di delega**

    * Esempio: nome di dominio: superdemo.live; nome dell'account SAM: big-ipuser

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Passaggio 2: Configurare SPN (sull'account di delega APM)**

    *  Esempio: setspn –A host/big-ipuser.superdemo.live big-ipuser

* **Passaggio 3: Delega SPN (per l'account del servizio app)**

    * Configurare la delega appropriata per l'account di delega F5.
    * Nell'esempio seguente l'account di delega APM viene configurato per KCD per l'app FRP-App1.superdemo.live.

        ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Fornire i dettagli come indicato nel documento di riferimento precedente disponibile [qui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Appendice per SAML: mapping delle variabili BIG-IP di F5:

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![Configurazione di F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Di seguito è riportato l'elenco completo degli attributi SAML predefiniti. GivenName è rappresentato dalla stringa seguente.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6.session.saml.last.assertionIssueInstant  | 2019-06-16T19:18:03.054Z |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/objectidentifier | 90d5f0e5-8f46-4bfd-b40f-ec973d00fcb7 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/tenantid | e6abffcf-4d23-4388-91c2-bfdfcbb1530c |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | 2019-06-16T19:18:00.318Z |
| eb46b6b6.session.saml.last.identity | user0@superdemo.live |
| eb46b6b6.session.saml.last.inResponseTo | _b9c67faa63a224d7a63f4f3cbb09f78dc05fab |
| eb46b6b6.session.saml.last.nameIDValue | user0@superdemo.live |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | _a1eca95a-6c41-449e-bb53-1477ba106470 |
| eb46b6b6.session.saml.last.responseIssueInstant | 2019-06-16T19:18:03.070Z |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | 2019-06-16T19:23:03.054Z |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | 2019-06-16T19:13:03.054Z |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | 2019-06-16T20:13:03.054Z |

### <a name="create-f5-test-user"></a>Creare l'utente di test di F5

In questa sezione viene creato un utente di nome B.Simon in F5. Collaborare con il  [team di supporto clienti di F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) per aggiungere gli utenti alla piattaforma F5. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di F5 nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di F5 per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare F5 con Azure AD](https://aad.portal.azure.com/)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](headerf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

