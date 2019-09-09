---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5 | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165524"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con F5

Questa esercitazione descrive come integrare F5 con Azure Active Directory (Azure AD). Integrando F5 con Azure AD, è possibile:

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

* L'accesso SSO di F5 può essere configurato in tre diversi modi.

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based](#configure-f5-single-sign-on-for-header-based-application)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Aggiunta di F5 dalla raccolta

Per configurare l'integrazione di F5 in Azure AD, è necessario aggiungere F5 dalla raccolta all'elenco di app SaaS gestite.

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
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

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
   1. Fare clic su **Create**(Crea).

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

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurare l'accesso Single Sign-On di F5 per l'applicazione Header Based

1. Aprire una nuova finestra del Web browser, accedere al sito aziendale di F5 (Header Based) come amministratore e seguire questa procedura:

1. È necessario importare il Certificato dei metadati in F5 (Header Based) che verrà usato più avanti nel processo di configurazione. Passare a **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (Sistema > Gestione certificati > Traffico gestione certificati > Elenco certificati SSL). Fare clic su **Import** (Importa) nell'angolo destro.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure12.png)
 
1. Inoltre, è necessario un **certificato SSL** per il nome host (`headerapp.superdemo.live`), in questo esempio viene usato il certificato Wildcard.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure13.png)

1. Passare a **F5 (Header Based) BIG-IP Click Access > Guided Configuration > Federation > SAML Service Provider** (Accesso clic BIG-IP di F5 (Header Based) > Configurazione guidata > Federazione > Provider di servizi SAML).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure01.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure02.png)
 
1. Specificare il valore di **Entity ID** (ID entità), indicando lo stesso valore specificato nella configurazione dell'applicazione di Azure AD.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure03.png) 

1. Creare un nuovo server virtuale e specificare un valore per **Destination Address** (Indirizzo di destinazione). Il valore di **Redirect Port** (Reindirizza porta) è facoltativo. Scegliere il **certificato Wildcard** (o il **certificato** caricato per l'applicazione) che è stato caricato in precedenza e la **chiave privata associata**.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure04.png) 

1. Caricare i **metadati** della configurazione e specificare un nuovo valore per **Name for SAML IDP Connector** (Nome per il connettore IDP SAML). Sarà inoltre necessario specificare il certificato di federazione caricato in precedenza.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure05.png)
 
1. **Creare il nuovo** pool di app back-end e specificare gli **indirizzi IP** dei server applicazione back-end.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure06.png)

1. In Single Sign-On scegliere **HTTP header-based** (HTTP basato su intestazione). È possibile aggiungere altre intestazioni in base all'applicazione. Vedere l'appendice per l'elenco delle variabili della sessione SAML

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure07.png) 

1. Contattare il [team di supporto clienti di F5 (Header Based)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) per i dettagli sulla documentazione delle **proprietà di controllo dell'endpoint**.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure08.png)

1. Contattare il [team di supporto clienti di F5 (Header Based)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) per i dettagli sulla documentazione delle **proprietà di gestione della sessione**.

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure09.png)

1. **Rivedere il riepilogo** e fare clic su **Deploy** (Distribuisci).

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure10.png)

    ![Configurazione di F5 (Header Based)](./media/headerf5-tutorial/configure11.png)

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

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Kerberos](kerbf5-tutorial.md)

- [Configurare l'accesso Single Sign-On di F5 per l'applicazione Advanced Kerberos](advance-kerbf5-tutorial.md)

