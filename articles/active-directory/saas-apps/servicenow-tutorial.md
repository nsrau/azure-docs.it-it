---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ServiceNow | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b3b7ea34d48c37a4e07ab073e611d5a60399a9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706932"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ServiceNow

Questa esercitazione descrive come integrare ServiceNow con Azure Active Directory (Azure AD). Integrando ServiceNow con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a ServiceNow.
* Abilitare gli utenti per l'accesso automatico a ServiceNow con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di ServiceNow abilitata per l'accesso Single Sign-On (SSO).
* Per ServiceNow, un'istanza o un tenant di ServiceNow, versione Calgary o successiva.
* Per ServiceNow Express, un'istanza di ServiceNow Express, versione Helsinki o successiva.
* Nel tenant di ServiceNow deve essere abilitato il [plug-in Multiple Provider Single Sign On](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0). A tale scopo, è possibile [inviare una richiesta di servizio](https://hi.service-now.com).
* Per la configurazione automatica, abilitare il plug-in per più provider per ServiceNow.
* Per installare l'applicazione ServiceNow Classic (Mobile), passare allo store appropriato, cercare l'applicazione ServiceNow Classic e quindi scaricarla.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. ServiceNow supporta l'accesso SSO avviato da **SP** e il [provisioning utenti automatizzato](servicenow-provisioning-tutorial.md).

È possibile configurare l'applicazione ServiceNow Classic (Mobile) con Azure AD per l'abilitazione dell'accesso SSO. Supporta sia gli utenti Android che gli utenti iOS. In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

## <a name="add-servicenow-from-the-gallery"></a>Aggiungere ServiceNow dalla raccolta

Per configurare l'integrazione di ServiceNow in Azure AD, è necessario aggiungere ServiceNow dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere **ServiceNow** nella casella di ricerca.
1. Selezionare **ServiceNow** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per ServiceNow

Configurare e testare l'accesso SSO di Azure AD con ServiceNow usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ServiceNow.

Per configurare e testare l'accesso SSO di Azure AD con ServiceNow, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
    1. [Configurare l'accesso Single Sign-On di Azure AD per ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) per consentire agli utenti di usare questa funzionalità.
2. [Configurare ServiceNow](#configure-servicenow) per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. [Creare un utente di test di ServiceNow](#create-servicenow-test-user) per avere in ServiceNow una controparte di B.Simon collegata alla rappresentazione dell'utente in Azure AD.
    1. [Configurare l'accesso Single Sign-On per ServiceNow Express](#configure-servicenow-express-sso) per configurare le impostazioni di Single Sign-On sul lato applicazione.  
3. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.
4. [Testare l'accesso Single Sign-On per ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ServiceNow** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della penna relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML con l'icona della penna evidenziata](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. In **URL di accesso** immettere un URL con il modello seguente: `https://<instance-name>.service-now.com/navpage.do`

    b. In **Identificatore (ID entità)** immettere un URL con il modello seguente: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** . 

   ![Screenshot della sezione Certificato di firma SAML, in cui è evidenziata la voce Scarica](common/certificatebase64.png)

   a. Fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note. Questo URL verrà usato più avanti nell'esercitazione.

    b. Fare clic su **Scarica** per scaricare il **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

1. Nella sezione **Configura ServiceNow** copiare gli URL appropriati in base alle esigenze.

   ![Screenshot della sezione Configura ServiceNow, in cui sono evidenziati gli URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test, di nome B.Simon, nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Per **Nome** immettere `B.Simon`.  
   1. Per **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ServiceNow.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco di applicazioni, selezionare **ServiceNow**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Screenshot di Utenti e gruppi, in cui è evidenziato il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic su **Seleziona**.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere quindi **Seleziona**.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurare l'accesso Single-Sign-On di Azure AD per ServiceNow Express

1. Nella pagina di integrazione dell'applicazione **ServiceNow** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Screenshot della pagina di integrazione dell'applicazione ServiceNow, in cui è evidenziata la voce Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Screenshot della finestra Selezionare un metodo di accesso Single Sign-On, in cui è evidenziato il metodo SAML](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona della penna per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della penna](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Per **URL di accesso** immettere un URL con il modello seguente: `https://<instance-name>.service-now.com/navpage.do`

    b. Per **Identificatore (ID entità)** immettere un URL con il modello seguente: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti. Salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con Scarica evidenziato](common/certificatebase64.png)

6. È possibile lasciare che sia Azure AD a configurare automaticamente ServiceNow per l'autenticazione basata su SAML. Per abilitare questo servizio, passare alla sezione **Configura ServiceNow** e fare clic su **Visualizzare le istruzioni dettagliate** per aprire la finestra **Configura accesso**.

    ![Screenshot della sezione Configura ServiceNow, in cui è evidenziata la voce Visualizzare le istruzioni dettagliate](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Nel modulo **Configura accesso** immettere il nome dell'istanza di ServiceNow, il nome utente con diritti amministrativi e la password di amministratore e quindi fare clic su **Configura ora**. Affinché la procedura funzioni, il nome utente con diritti amministrativi fornito deve avere il ruolo **security_admin** assegnato in ServiceNow. In caso contrario, per configurare manualmente ServiceNow per l'uso di Azure AD come provider di identità SAML, selezionare **Configura manualmente l'accesso Single Sign-On**. Copiare **URL disconnessione, Identificatore Azure AD e URL di accesso** dalla sezione di riferimento rapido.

    ![Screenshot del modulo Configura accesso, in cui è evidenziata la voce Configura ora](./media/servicenow-tutorial/configure.png "Configura URL app")

## <a name="configure-servicenow"></a>Configurare ServiceNow

1. Accedere all'applicazione ServiceNow come amministratore.

2. Attivare il plug-in **Integration - Multiple Provider Single Sign-On Installer** (Integrazione - Programma di installazione di Multiple Provider Single Sign-On) seguendo questa procedura:

    a. Nel riquadro sinistro cercare la sezione **System Definition** (Definizione sistema) dalla casella di ricerca e quindi selezionare **Plugins** (Plug-in).

    ![Screenshot della sezione System Definition (Definizione sistema), in cui sono evidenziate le voci System Definition (Definizione sistema) e Plugins (Plug-in)](./media/servicenow-tutorial/tutorial_servicenow_03.png "Attivazione del plug-in")

    b. Cercare **Integration - Multiple Provider Single Sign-On Installer** (Integrazione: Programma di installazione per più accessi Sign-On provider).

     ![Screenshot della pagina System Plugins (Plug-in di sistema), in cui è evidenziata la voce Integration - Multiple Provider Single Sign-On Installer (Integrazione - Programma di installazione di Multiple Provider Single Sign-On)](./media/servicenow-tutorial/tutorial_servicenow_04.png "Attivazione del plug-in")

    c. Selezionare il plug-in. Fare clic con il pulsante destro del mouse e scegliere **Activate/Upgrade** (Attiva/Aggiorna).

     ![Screenshot del menu di scelta rapida del plug-in, in cui è evidenziata la voce Activate/Upgrade (Attiva/Aggiorna)](./media/servicenow-tutorial/tutorial_activate.png "Attivazione del plug-in")

    d. Selezionare **Attiva**.

     ![Screenshot della finestra di dialogo Activate Plugin (Attiva plug-in), in cui è evidenziato il pulsante Activate (Attiva)](./media/servicenow-tutorial/tutorial_activate1.png "Attivazione del plug-in")

3. Nel riquadro sinistro cercare la sezione **Multi-Provider SSO** dalla barra di ricerca e quindi fare clic su **Properties** (Proprietà).

    ![Screenshot della sezione Multi-Provider SSO, in cui sono evidenziate le voci Multi-Provider SSO e Properties (Proprietà)](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configura URL app")

4. Nella finestra di dialogo **Multiple Provider SSO Properties** (Proprietà di Multiple Provider Single Sign-On) seguire questa procedura:

    ![Screenshot della finestra di dialogo Multiple Provider SSO (Proprietà di Multiple Provider Single Sign-On)](./media/servicenow-tutorial/ic7694981.png "Configura URL app")

    * Per **Enable multiple provider SSO** (Abilita SSO per più provider) selezionare **Yes** (Sì).
  
    * Per **Enable Auto Importing of users from all identity providers into the user table** (Abilita l'importazione automatica di utenti da tutti i provider di identità nella tabella degli utenti), selezionare **Yes** (Sì).

    * Per **Enable debug logging for the multiple provider SSO integration** (Abilita la registrazione debug per l'integrazione SSO per più provider) selezionare **Yes** (Sì).

    * Per **The field on the user table that...** (Il campo della tabella degli utenti che...) immettere **user_name**.
  
    * Selezionare **Salva**.

6. È possibile configurare ServiceNow automaticamente o manualmente. Per configurare ServiceNow automaticamente, seguire questa procedura:

    1. Tornare alla pagina Single Sign-On **ServiceNow** nel portale di Azure.

    1. Per ServiceNow è disponibile un servizio di configurazione con un solo clic. Per abilitare questo servizio, passare alla sezione **Configurazione di ServiceNow** e fare clic su **Configura ServiceNow** per aprire la finestra **Configura accesso**.

        ![Screenshot di Configura ServiceNow, in cui è evidenziata la voce Visualizzare le istruzioni dettagliate](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. Nel modulo **Configura accesso** immettere il nome dell'istanza di ServiceNow, il nome utente con diritti amministrativi e la password di amministratore e quindi fare clic su **Configura ora**. Affinché la procedura funzioni, il nome utente con diritti amministrativi fornito deve avere il ruolo **security_admin** assegnato in ServiceNow. In caso contrario, per configurare manualmente ServiceNow per l'uso di Azure AD come provider di identità SAML, selezionare **Configura manualmente l'accesso Single Sign-On**. Copiare **URL disconnessione, Identificatore entità SAML e URL del servizio Single Sign-On SAML** dalla sezione di riferimento rapido.

        ![Screenshot del modulo Configura accesso, in cui è evidenziata la voce Configura ora](./media/servicenow-tutorial/configure.png "Configura URL app")

    1. Accedere all'applicazione ServiceNow come amministratore.

       * Nella configurazione automatica tutte le impostazioni necessarie sono configurate sul lato **ServiceNow**, ma l'opzione **X.509 Certificate** (Certificato X.509) non è abilitata per impostazione predefinita. È necessario eseguirne manualmente il mapping al provider di identità in ServiceNow. A tale scopo, seguire questa procedura:

         1. Nel riquadro sinistro cercare la sezione **Multi-Provider SSO** dalla casella di ricerca e selezionare **Identity Providers** (Provider di identità).

            ![Screenshot della sezione Multi-Provider SSO, in cui è evidenziata la voce Identity Providers (Provider di identità)](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configura accesso Single Sign-On")

         1. Selezionare il provider di identità generato automaticamente.

            ![Screenshot dei provider di identità, in cui è evidenziato il provider di identità generato automaticamente](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configura accesso Single Sign-On")

         1.  Nella sezione **Identity Provider** (Provider di identità) seguire questa procedura:

             ![Screenshot della sezione Identity Provider (Provider di identità)](./media/servicenow-tutorial/automatic_config.png "Configura accesso Single Sign-On")

               * Per **Name** (Nome) immettere un nome per la configurazione, ad esempio **Microsoft Azure Federated Single Sign-On** (Accesso Single Sign-On federato di Microsoft Azure).

               * Rimuovere dalla casella di testo il valore **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest del provider di identità) inserito.

               * Copiare il valore **ServiceNow Homepage** (Home page di ServiceNow) e incollarlo in **URL di accesso** nella sezione **Configurazione SAML di base di ServiceNow** del portale di Azure.

                  > [!NOTE]
                  > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.

              * Copiare il valore **Entity ID / Issuer** (ID entità/emittente) e incollarlo in **Identificatore** nella sezione **Configurazione SAML di base di ServiceNow** del portale di Azure.

              * Verificare che il campo **NameID Policy** (Criterio NameID) sia impostato sul valore `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

         1. Scorrere verso il basso fino alla sezione **X.509 Certificate** (Certificato X.509) e fare clic su **Edit** (Modifica).

             ![Screenshot della sezione X.509 Certificate (Certificato X.509), in cui è evidenziato il pulsante Edit (Modifica)](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configura accesso Single Sign-On")

         1. Selezionare il certificato e fare clic sulla freccia a destra per aggiungerlo.

            ![Screenshot della raccolta, in cui sono evidenziati il certificato e la freccia a destra](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configura accesso Single Sign-On")

          1. Selezionare **Salva**.

          1. Nell'angolo in alto a destra della pagina fare clic su **Test connessione**.

             ![Screenshot della pagina, in cui è evidenziato il pulsante Test connessione](./media/servicenow-tutorial/tutorial_activate2.png "Attivazione del plug-in")

          1. Quando vengono richieste le credenziali, immetterle. Verrà visualizzata la pagina seguente. È previsto l'errore in **SSO Logout Test Results** (Risultati test disconnessione SSO). Ignorare l'errore e fare clic su **Activate** (Attiva).

             ![Screenshot della pagina dei risultati del test](./media/servicenow-tutorial/servicenowactivate.png "Configura accesso Single Sign-On")
  
6. Per configurare **ServiceNow** manualmente, seguire questa procedura:

    1. Accedere all'applicazione ServiceNow come amministratore.

    1. Nel riquadro sinistro selezionare**Identity Providers** (Provider di identità).

        ![Screenshot della sezione Multi-Provider SSO, in cui è evidenziata la voce Identity Providers (Provider di identità)](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configura accesso Single Sign-On")

    1. Nella finestra di dialogo **Identity Providers** (Provider di identità) fare clic su **New** (Nuovo).

        ![Screenshot della finestra di dialogo Identity Providers (Provider di identità), in cui è evidenziato il pulsante New (Nuovo)](./media/servicenow-tutorial/ic7694977.png "Configura accesso Single Sign-On")

    1. Nella finestra di dialogo **Identity Providers** (Provider di identità) fare clic su **SAML**.

        ![Screenshot della finestra di dialogo Identity Providers (Provider di identità), in cui è evidenziata la voce SAML](./media/servicenow-tutorial/ic7694978.png "Configura accesso Single Sign-On")

    1. In **Import Identity Provider Metadata** (Importa metadati provider di identità) seguire questa procedura:

        ![Screenshot di Import Identity Provider Metadata (Importa metadati provider di identità), in cui sono evidenziate le voci URL e Import (Importa)](./media/servicenow-tutorial/idp.png "Configura accesso Single Sign-On")

        1. Immettere l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

        1. Selezionare **Importa**.

    1. Viene letto l'URL dei metadati IdP e vengono inserite tutte le informazioni dei campi.

        ![Screenshot della sezione Identity Provider (Provider di identità)](./media/servicenow-tutorial/ic7694982.png "Configura accesso Single Sign-On")

        * Per **Name** (Nome) immettere un nome per la configurazione, ad esempio **Microsoft Azure Federated Single Sign-On** (Accesso Single Sign-On federato di Microsoft Azure).

        * Rimuovere dalla casella di testo il valore **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest del provider di identità) inserito.

        * Copiare il valore **ServiceNow Homepage** (Home page di ServiceNow). Incollarlo in **URL di accesso** nella sezione **Configurazione SAML di base di ServiceNow** del portale di Azure.

            > [!NOTE]
            > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.

        * Copiare il valore **Entity ID / Issuer** (ID entità/emittente). Incollarlo in **Identificatore** nella sezione **Configurazione SAML di base di ServiceNow** del portale di Azure.

        * Verificare che il campo **NameID Policy** (Criterio NameID) sia impostato sul valore `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Selezionare **Advanced** (Avanzate). Nella casella di testo **User Field** (Campo utente) immettere **email** o **user_name**, a seconda di quale campo viene usato per identificare in modo univoco gli utenti nella distribuzione ServiceNow.

            > [!NOTE]
            > È possibile configurare Azure AD per l'emissione dell'ID utente di Azure AD (nome dell'entità utente) o dell'indirizzo di posta elettronica come identificatore univoco nel token SAML. A tale scopo, passare alla sezione **ServiceNow** > **Attributi** > **Single Sign-On** del portale di Azure e mappare il campo desiderato all'attributo **nameidentifier**. Il valore archiviato per l'attributo selezionato in Azure AD, ad esempio il nome dell'entità utente, deve corrispondere al valore archiviato in ServiceNow per il campo immesso, ad esempio user_name.

        * Nell'angolo in alto a destra della pagina fare clic su **Test connessione**.

        * Quando vengono richieste le credenziali, immetterle. Verrà visualizzata la pagina seguente. È previsto l'errore in **SSO Logout Test Results** (Risultati test disconnessione SSO). Ignorare l'errore e fare clic su **Activate** (Attiva).

          ![Screenshot della pagina dei risultati del test](./media/servicenow-tutorial/servicenowactivate.png "Configura accesso Single Sign-On")

### <a name="create-servicenow-test-user"></a>Creare l'utente di test di ServiceNow

Questa sezione descrive come creare un utente di nome B.Simon in ServiceNow. ServiceNow supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita.

> [!NOTE]
> Se è necessario creare un utente manualmente, contattare il [team di supporto per il client ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Configurare l'accesso SSO per ServiceNow Express

1. Accedere all'applicazione ServiceNow Express come amministratore.

2. Nel riquadro sinistro selezionare **Single Sign-On**.

    ![Screenshot dell'applicazione ServiceNow Express, in cui è evidenziata la voce Single Sign-On](./media/servicenow-tutorial/ic7694980ex.png "Configura URL app")

3. Nella finestra di dialogo **Single Sign-On** fare clic sull'icona di configurazione nell'angolo in alto a destra e impostare le proprietà seguenti:

    ![Screenshot della finestra di dialogo Single Sign-On](./media/servicenow-tutorial/ic7694981ex.png "Configura URL app")

    a. Spostare **Enable multiple provider SSO** (Abilita SSO per più provider) verso destra.

    b. Spostare **Enable debug logging for the multiple provider SSO integration** (Abilita la registrazione debug per l'integrazione SSO per più provider) verso destra.

    c. In **The field on the user table that...** (Il campo della tabella degli utenti che...) immettere **user_name**.

4. Nella finestra di dialogo **Single Sign-On** fare clic su **Add New Certificate** (Aggiungi nuovo certificato).

    ![Screenshot della finestra di dialogo Single Sign-On, in cui è evidenziata la voce Add New Certificate (Aggiungi nuovo certificato)](./media/servicenow-tutorial/ic7694973ex.png "Configura accesso Single Sign-On")

5. Nella finestra di dialogo **X.509 Certificates** (Certificati X.509) seguire questa procedura:

    ![Screenshot della finestra di dialogo X.509 Certificates (Certificati X.509)](./media/servicenow-tutorial/ic7694975.png "Configura accesso Single Sign-On")

    a. Per **Name** (Nome) immettere un nome per la configurazione, ad esempio **TestSAML2.0**.

    b. Selezionare **Active**.

    c. Per **Format** (Formato) selezionare **PEM**.

    d. Per **Type** (Tipo) selezionare **Trust Store Cert** (Cert. archivio attendibilità).

    e. Aprire nel Blocco note il certificato con codifica Base64 scaricato dal portale di Azure. Copiare il contenuto negli Appunti e quindi incollarlo nella casella di testo **PEM Certificate** (Certificato PEM).

    f. Fare clic su **Update** (Aggiorna).

6. Nella finestra di dialogo **Single Sign-On** fare clic su **Add New IdP** (Aggiungi nuovo IdP).

    ![Screenshot della finestra di dialogo Single Sign-On, in cui è evidenziata la voce Add New IdP (Aggiungi nuovo IdP)](./media/servicenow-tutorial/ic7694976ex.png "Configura accesso Single Sign-On")

7. Nella finestra di dialogo **Add New Identity Provider** (Aggiungi nuovo provider di identità), in **Configure Identity Provider** (Configura provider di identità), seguire questa procedura:

    ![Screenshot della finestra di dialogo Add New Identity Provider (Aggiungi nuovo provider di identità)](./media/servicenow-tutorial/ic7694982ex.png "Configura accesso Single Sign-On")

    a. Per **Name** (Nome) immettere un nome per la configurazione, ad esempio **SAML 2.0**.

    b. Per **Identity Provider URL** (URL provider di identità) incollare il valore dell'ID provider di identità copiato dal portale di Azure.

    c. Per **Identity Provider's AuthnRequest** (AuthnRequest del provider di identità) incollare il valore dell'URL della richiesta di autenticazione copiato dal portale di Azure.

    d. Per **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest del provider di identità) incollare il valore dell'URL di disconnessione copiato dal portale di Azure.

    e. Per **Identity Provider Certificate** (Certificato del provider di identità) selezionare il certificato creato nel passaggio precedente.

8. Fare clic su **Advanced Settings** (Impostazioni avanzate). In **Additional Service Provider Properties** (Proprietà aggiuntive del provider di identità) seguire questa procedura:

    ![Screenshot della finestra di dialogo Add New Identity Provider (Aggiungi nuovo provider di identità), in cui è evidenziata la voce Advanced Settings (Impostazioni avanzate)](./media/servicenow-tutorial/ic7694983ex.png "Configura accesso Single Sign-On")

    a. Per **Protocol Binding for the IDP's SingleLogoutRequest** (Binding protocollo per SingleLogoutRequest dell'IDP) immettere **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Per **NameID Policy** (Criterio NameID) immettere **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. Per **AuthnContextClassRef Method** (Metodo AuthnContextClassRef) immettere `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Disattivare (deselezionare) **Create AuthnContextClass** (Crea AuthnContextClass).

9. In **Additional Service Provider Properties** (Proprietà del provider di identità aggiuntivo) seguire questa procedura:

    ![Screenshot della finestra di dialogo Add New Identity Provider (Aggiungi nuovo provider di identità), in cui sono evidenziate diverse proprietà](./media/servicenow-tutorial/ic7694984ex.png "Configura accesso Single Sign-On")

    a. Per **ServiceNow Homepage** (Home page di ServiceNow) immettere l'URL della home page dell'istanza di ServiceNow.

    > [!NOTE]
    > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.

    b. Per **Entity ID / Issuer** (ID entità/emittente) immettere l'URL del tenant di ServiceNow.

    c. Per **Audience URI** (URI destinatari) immettere l'URL del tenant di ServiceNow.

    d. Per **Clock Skew** (Sfasamento del clock) immettere **60**.

    e. Per **User Field** (Campo utente) immettere **email** o **user_name**, a seconda di quale campo viene usato per identificare in modo univoco gli utenti nella distribuzione ServiceNow.

    > [!NOTE]
    > È possibile configurare Azure AD per l'emissione dell'ID utente di Azure AD (nome dell'entità utente) o dell'indirizzo di posta elettronica come identificatore univoco nel token SAML. A tale scopo, passare alla sezione **ServiceNow** > **Attributi** > **Single Sign-On** del portale di Azure e mappare il campo desiderato all'attributo **nameidentifier**. Il valore archiviato per l'attributo selezionato in Azure AD, ad esempio il nome dell'entità utente, deve corrispondere al valore archiviato in ServiceNow per il campo immesso, ad esempio user_name.

    f. Selezionare **Salva**.

## <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro ServiceNow nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ServiceNow per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testare l'accesso Single Sign-On per ServiceNow Classic (Mobile)

1. Aprire l'applicazione **ServiceNow Classic (Mobile)** e seguire questa procedura:

    a. Fare clic sul segno più nell'angolo in basso a destra.

    ![Screenshot dell'applicazione ServiceNow Classic, in cui è evidenziato il segno più](./media/servicenow-tutorial/test03.png)

    b. Immettere il nome dell'istanza di ServiceNow e fare clic su **Continue** (Continua).

    ![Screenshot della pagina Add Instance (Aggiungi istanza), in cui è evidenziato il pulsante Continue (Continua)](./media/servicenow-tutorial/test04.png)

    c. Nella pagina **Log in** (Accesso) seguire questa procedura:

    ![Screenshot della pagina Log in (Accesso), in cui è evidenziato il pulsante Use external login (Usa accesso esterno)](./media/servicenow-tutorial/test01.png)

    *  Completare il campo **Username** (Nome utente), ad esempio immettendo B.simon@contoso.com.

    *  Fare clic su **USE EXTERNAL LOGIN** (USA ACCESSO ESTERNO). Si verrà reindirizzati alla pagina di Azure AD per l'accesso.
    
    *  Immettere le credenziali. Se è prevista un'autenticazione di terze parti o se è abilitata un'altra funzionalità di sicurezza, l'utente deve rispondere di conseguenza. Verrà visualizzata la **home page** dell'applicazione.

        ![Screenshot della home page dell'applicazione](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurare il provisioning utenti](servicenow-provisioning-tutorial.md)

- [Provare ServiceNow con Azure AD](https://aad.portal.azure.com)
