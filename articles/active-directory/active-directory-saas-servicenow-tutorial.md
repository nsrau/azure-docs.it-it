---
title: 'Esercitazione: Integrazione di Azure Active Directory con ServiceNow | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: jeedes
ms.openlocfilehash: d893b55e2e771035bbd1097da678830fafb24e7a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Esercitazione: Integrazione di Azure Active Directory con ServiceNow

Questa esercitazione descrive come integrare ServiceNow con Azure Active Directory (Azure AD).

L'integrazione di ServiceNow con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ServiceNow.
- È possibile abilitare gli utenti per l'accesso automatico a ServiceNow (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con ServiceNow, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Per ServiceNow, un'istanza o un tenant di ServiceNow, versione Calgary o successiva
- Per ServiceNow Express, un'istanza di ServiceNow Express, versione Helsinki o successiva
- Nel tenant di ServiceNow deve essere abilitato il [plug-in Multiple Provider Single Sign On](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0). Questa operazione può essere eseguita [inviando una richiesta di servizio](https://hi.service-now.com).
- Per la configurazione automatica, abilitare il plug-in per più provider per ServiceNow.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ServiceNow dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-servicenow-from-the-gallery"></a>Aggiunta di ServiceNow dalla raccolta
Per configurare l'integrazione di ServiceNow in Azure AD, è necessario aggiungere ServiceNow dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ServiceNow dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **ServiceNow**, selezionare **ServiceNow** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![ServiceNow nell'elenco risultati](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ServiceNow in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di ServiceNow e che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ServiceNow.

Per stabilire la relazione di collegamento, in ServiceNow assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ServiceNow, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD per ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Azure AD per ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**: per abilitare gli utenti all'uso di questa funzionalità.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creare un utente di test per ServiceNow](#create-a-servicenow-test-user)**: per avere una controparte di Britta Simon in ServiceNow collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurare l'accesso Single Sign-On di Azure AD per ServiceNow

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ServiceNow.

**Per configurare Single Sign-On di Azure AD con ServiceNow, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Nella sezione **URL e dominio ServiceNow** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di ServiceNow](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<instance-name>.service-now.com/navpage.do`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli in seguito con l'URL di accesso e l'identificatore effettivi. La procedura è descritta più avanti nell'esercitazione.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Per generare l'URL dei **metadati**, seguire questa procedura:

    a. Fare clic su **Registrazioni per l'app**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/appregistrations.png)

    b. Fare clic su **Endpoint** per aprire la finestra di dialogo **Endpoint**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/endpointicon.png)
    
    c. Fare clic sul pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/endpoint.png)

    d. Passare ora alle proprietà di **ServiceNow**, copiare l'**ID applicazione** usando il pulsante **Copia** e incollarlo nel Blocco note.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.  Copiare nel Blocco note il valore generato poiché l'URL dei metadati verrà usato più avanti nell'esercitazione.

7. Accedere all'applicazione ServiceNow come amministratore.

8. Attivare il plug-in **Integration - Multiple Provider Single Sign-On Installer** seguendo questa procedura:

    a. Nel riquadro di spostamento a sinistra cercare la sezione **System Definition** (Definizione sistema) dalla barra di ricerca e quindi fare clic su **Plugins** (Plug-in).

    ![Attivare il plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Attivare il plug-in")

     b. Cercare **Integration - Multiple Provider Single Sign-On Installer**.

     ![Attivare il plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Attivare il plug-in")

    c. Selezionare il plug-in. Fare clic con il pulsante destro del mouse e scegliere **Activate/Upgrade** (Attiva/Aggiorna).

    d. Fare clic sul pulsante **Activate** (Attiva).

9. Esistono due modi per configurare **ServiceNow**: automaticamente e manualmente.

10. For configurare **ServiceNow** automaticamente, eseguire questa procedura

    a. Tornare alla pagina Single Sign-On per **ServiceNow** nel portale di Azure.

    b. Per ServiceNow è disponibile un servizio di configurazione con un clic, che consente la configurazione automatica di ServiceNow in Azure AD per l'autenticazione basata su SAML. Per abilitare questo servizio, passare alla sezione **Configurazione di ServiceNow** e fare clic su **Configura ServiceNow** per aprire la finestra Configura accesso.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Immettere il nome dell'istanza di ServiceNow, il nome utente con diritti amministrativi e la password di amministratore nel modulo **Configura accesso** e quindi fare clic su **Configura ora**. Affinché la procedura funzioni, il nome utente con diritti amministrativi fornito deve avere il ruolo **security_admin** assegnato in ServiceNow. In caso contrario, per configurare manualmente ServiceNow in modo da usare Azure AD come provider di identità SAML, fare clic su **Configura manualmente l'accesso Single Sign-On** e copiare **l'URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione di riferimento rapido.

    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/configure.png "Configurare l'URL dell'app")

    d. Accedere all'applicazione ServiceNow come amministratore.

    e. Nella configurazione automatica tutte le impostazioni necessarie sono configurate sul lato **ServiceNow**, ma l'opzione **X.509 Certificate** (Certificato X.509) non è abilitata per impostazione predefinita. È necessario eseguirne manualmente il mapping al provider di identità in ServiceNow. Eseguire queste operazioni:
    
    * Nel riquadro di spostamento a sinistra fare clic su **Identity Providers** (Provider di identità) in **Multi-Provider SSO** (SSO più provider).

      ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurare l'accesso Single Sign-On")

    * Fare clic sul provider di identità generato automaticamente

      ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_08.png "Configurare l'accesso Single Sign-On")

    * Scorrere verso il basso fino alla sezione **X.509 Certificate** (Certificato X.509). Selezionare **Modifica**.

      ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_09.png "Configurare l'accesso Single Sign-On")
    
    * Selezionare il certificato e fare clic sulla freccia a destra per aggiungerlo

      ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_11.png "Configurare l'accesso Single Sign-On")

    * Fare clic su **Save**.

    * Fare clic su **Activate** (Attiva) nell'angolo in alto a destra della pagina.

11. For configurare **ServiceNow** manualmente, eseguire queste operazioni

12. Accedere all'applicazione ServiceNow come amministratore.

13. Nel riquadro di spostamento a sinistra cercare la sezione **Multi-Provider SSO** dalla barra di ricerca e quindi fare clic su **Properties** (Proprietà).

    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurare l'URL dell'app")

14. Nella finestra di dialogo **Multiple Provider SSO Properties** seguire questa procedura:

    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "Configurare l'URL dell'app")

    a. Per **Enable multiple provider SSO** selezionare **Yes**.

    b. Per **Enable Auto Importing of users from all identity providers into the user table** (Abilita l'importazione automatica di utenti da tutti i provider di identità nella tabella degli utenti), selezionare **Yes** (Sì).

    c. Per **Enable debug logging for the multiple provider SSO integration** (Abilita la registrazione debug per l'integrazione SSO per più provider) selezionare **Yes** (Sì).

    d. Nella casella di testo **The field on the user table that...** digitare **user_name**.

    e. Fare clic su **Save**.

14. Nel riquadro di spostamento a sinistra cercare la sezione **Multi-Provider SSO** dalla barra di ricerca e quindi fare clic su **x509 Certificates** (Certificati x509).

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurare l'accesso Single Sign-On")

15. Nella finestra di dialogo **X.509 Certificates** fare clic su **New**.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "Configurare l'accesso Single Sign-On")

16. Nella finestra di dialogo **X.509 Certificates** seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione, ad esempio **TestSAML2.0**.

    b. Selezionare **Active**.

    c. Per **Format** selezionare **PEM**.

    d. Per **Type** selezionare **Trust Store Cert**.

    e. Aprire nel Blocco note il certificato con codifica Base 64 scaricato da Azure, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PEM Certificate** (Certificato PEM).

     f. Fare clic su **Submit**.

17. Nel riquadro di spostamento a sinistra fare clic su **Identity Providers**.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurare l'accesso Single Sign-On")

18. Nella finestra di dialogo **Identity Providers** (Provider di identità) fare clic su **New** (Nuovo).

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "Configurare l'accesso Single Sign-On")

19. Nella finestra di dialogo **Identity Providers** (Provider di identità) fare clic su **SAML2 Update1?**.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "Configurare l'accesso Single Sign-On")

20. Nella finestra di dialogo SAML2 Update1 Properties seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/idp.png "Configurare l'accesso Single Sign-On")

    a. Selezionare l'opzione **URL** nella finestra di dialogo **Import Identity Provider Metadata** (Importa i metadati del provider di identità).

    b. Immettere l'**URL dei metadati** generato dal portale di Azure.

    c. Fare clic su **Importa**.

21. Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione, ad esempio **SAML 2.0**.
    
    b. Copiare il valore di **ServiceNow Homepage** (Home page di ServiceNow) e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio ServiceNow** del portale di Azure.

    > [!NOTE]
    > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.

    c. Copiare il valore di **Entity ID / Issuer** (ID entità/Emittente) e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio ServiceNow** del portale di Azure.

    d. Fare clic su **Advanced**. Nella casella di testo **User Field** (Campo utente) digitare **email** o **user_name**, a seconda di quale campo viene usato per identificare in modo univoco gli utenti nella distribuzione ServiceNow.

    > [!NOTE]
    > Per configurare Azure AD in modo da generare l'ID utente di Azure AD, ovvero il nome dell'entità utente, o l'indirizzo di posta elettronica come identificatore univoco nel token SAML, passare alla sezione **ServiceNow > Attributi > Single Sign-On** del portale di Azure ed eseguire il mapping del campo scelto all'attributo **nameidentifier**. Il valore archiviato per l'attributo selezionato in Azure AD, ad esempio il nome dell'entità utente, deve corrispondere al valore archiviato in ServiceNow per il campo immesso, ad esempio user_name.

     e. In **x509 Certificate** (Certificato x509) è elencato il certificato creato nel passaggio precedente.

     > [!NOTE]
     > ServiceNow non consente l'attivazione del servizio Idp senza fare clic sul pulsante di test della connessione. Per sostituire questo comportamento, eseguire queste operazioni.

22. Fare clic sull'icona di menu nel nuovo provider di identità creato nell'ambito della configurazione e selezionare **copy sys_id** nell'elenco

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694992.png "Configurare l'accesso Single Sign-On")

23. Nella casella di ricerca in alto a destra cercare **sys_properties.list** e premere INVIO.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694993.png "Configurare l'accesso Single Sign-On")

24. Fare clic su **Nuovo**.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "Configurare l'accesso Single Sign-On")

25. Nella sezione **System Property** (Proprietà sistema) eseguire queste operazioni:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694995.png "Configurare l'accesso Single Sign-On")

    a. Immettere il valore `glide.authenticate.sso.redirect.idp` nella casella di testo del nome.

    b. Nella casella di testo **Value** (Valore) incollare il valore di copy sys_id copiato nei passaggi precedenti.

    c. Selezionare **Private** (Privato).

    d. Fare clic su **Submit**.

26. Fare clic su **Nuovo**.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "Configurare l'accesso Single Sign-On")

27. Nella sezione **System Property** (Proprietà sistema) eseguire queste operazioni:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694996.png "Configurare l'accesso Single Sign-On")

    a. Immettere il valore `glide.authenticate.multisso.test.connection.mandatory` nella casella di testo del nome.

    b. Nella casella di testo **Value** (Valore) immettere **false**.

    c. Fare clic su **Submit**.

28. Dopo aver completato il passaggio precedente, è ora possibile attivare il nuovo provider di identità e l'accesso Single Sign-On funzionerà

> [!NOTE]
> Notare inoltre che è necessario testare la configurazione del nuovo servizio Idp in una nuova finestra in incognito

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configurare l'accesso Single Sign-On di Azure AD per ServiceNow Express

1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Nella sezione **URL e dominio ServiceNow** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://<instance-name>.service-now.com/navpage.do`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ServiceNow](https://www.servicenow.com/support/contact-support.html).

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Per ServiceNow è disponibile un servizio di configurazione con un clic, che consente la configurazione automatica di ServiceNow in Azure AD per l'autenticazione basata su SAML. Per abilitare questo servizio, passare alla sezione **Configurazione di ServiceNow** e fare clic su **Configura ServiceNow** per aprire la finestra Configura accesso.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. Immettere il nome dell'istanza di ServiceNow, il nome utente con diritti amministrativi e la password di amministratore nel modulo **Configura accesso** e quindi fare clic su **Configura ora**. Affinché la procedura funzioni, il nome utente con diritti amministrativi fornito deve avere il ruolo **security_admin** assegnato in ServiceNow. In caso contrario, per configurare manualmente ServiceNow in modo da usare Azure AD come provider di identità SAML, fare clic su **Configura manualmente l'accesso Single Sign-On** e copiare **l'URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione di riferimento rapido.

    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/configure.png "Configurare l'URL dell'app")

8. Accedere all'applicazione ServiceNow Express come amministratore.

9. Nel riquadro di spostamento a sinistra fare clic su **Single Sign-On**.

    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurare l'URL dell'app")

10. Nella finestra di dialogo **Single Sign-On** fare clic sull'icona di configurazione nell'angolo superiore destro e impostare le proprietà seguenti:

    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurare l'URL dell'app")

    a. Spostare **Enable multiple provider SSO** (Abilita SSO per più provider) verso destra.
    
    b. Spostare **Enable debug logging for the multiple provider SSO integration** (Abilita la registrazione debug per l'integrazione SSO per più provider) verso destra.
    
    c. Nella casella di testo **The field on the user table that...** digitare **user_name**.

11. Nella finestra di dialogo **Single Sign-On** fare clic su **Add New Certificate** (Aggiungi nuovo certificato).

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurare l'accesso Single Sign-On")

12. Nella finestra di dialogo **X.509 Certificates** seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione, ad esempio **TestSAML2.0**.

    b. Selezionare **Active**.

    c. Per **Format** selezionare **PEM**.

    d. Per **Type** selezionare **Trust Store Cert**.

    e. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PEM Certificate** (Certificato PEM).

    f. Fare clic su **Update** (Aggiorna).

13. Nella finestra di dialogo **Single Sign-On** fare clic su **Add New IdP** (Aggiungi nuovo IdP).

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurare l'accesso Single Sign-On")

14. Nella finestra di dialogo **Add New Identity Provider** (Aggiungi nuovo provider di identità) in **Configure Identity Provider** (Configura provider di identità) seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione, ad esempio **SAML 2.0**.

    b. Nel campo **Identity Provider URL** (URL provider di identità) incollare il valore di **Identity Provider ID** (ID provider di identità) copiato dal portale di Azure.
    
    c. Nel campo **Identity Provider's AuthnRequest** (Richiesta di autenticazione provider di identità) incollare il valore di **Authentication Request URL** (URL richiesta di autenticazione) copiato dal portale di Azure.

    d. Nel campo **Identity Provider's SingleLogoutRequest** (Richiesta di disconnessione singola provider di identità) incollare il valore di **Single Sign-Out Service URL** (URL servizio Single Sign-Out) copiato dal portale di Azure

    e. Per **Identity Provider Certificate** (Certificato del provider di identità) selezionare il certificato creato nel passaggio precedente.

15. Fare clic su **Advanced Settings** (Impostazioni avanzate). In **Additional Identity Provider Properties** (Proprietà del provider di identità aggiuntivo) seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Protocol Binding for the IDP's SingleLogoutRequest** digitare **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Nella casella di testo **NameID Policy** (Criteri NameID) digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. In **AuthnContextClassRef Method** (Metodo AuthnContextClassRef) digitare `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Fare clic su **Create an AuthnContextClass**.

16. In **Additional Service Provider Properties** (Proprietà del provider di identità aggiuntivo) seguire questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **ServiceNow Homepage** digitare l'URL della home page dell'istanza di ServiceNow.

    > [!NOTE]
    > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.

    b. Nella casella di testo **Entity ID / Issuer** digitare l'URL del tenant ServiceNow.

    c. Nella casella di testo **Audience URI** (URI destinatario) digitare l'URL del tenant ServiceNow.

    d. Nella casella di testo **Clock Skew** digitare **60**.

    e. Nella casella di testo **User Field** (Campo utente) digitare **email** o **user_name**, a seconda di quale campo viene usato per identificare in modo univoco gli utenti nella distribuzione ServiceNow.

    > [!NOTE]
    > Per configurare Azure AD in modo da generare l'ID utente di Azure AD, ovvero il nome dell'entità utente, o l'indirizzo di posta elettronica come identificatore univoco nel token SAML, passare alla sezione **ServiceNow > Attributi > Single Sign-On** del portale di Azure ed eseguire il mapping del campo scelto all'attributo **nameidentifier**. Il valore archiviato per l'attributo selezionato in Azure AD, ad esempio il nome dell'entità utente, deve corrispondere al valore archiviato in ServiceNow per il campo immesso, ad esempio user_name.

    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-servicenow-test-user"></a>Creare un utente di test di ServiceNow

In questa sezione viene creato un utente di nome Britta Simon in ServiceNow. Se non si sa come aggiungere un utente all'account ServiceNow o ServiceNow Express, contattare il [team di supporto clienti di ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ServiceNow.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a ServiceNow, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni, selezionare **ServiceNow**.

    ![Collegamento di ServiceNow nell'elenco delle applicazioni](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ServiceNow nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ServiceNow.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

