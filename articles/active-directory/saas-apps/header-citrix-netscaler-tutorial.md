---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Citrix NetScaler (Header Based Authentication) | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470536"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Citrix NetScaler (Header Based Authentication)

Questa esercitazione descrive come integrare Citrix NetScaler con Azure Active Directory (Azure AD). Integrando Citrix NetScaler con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Citrix NetScaler.
* Abilitare gli utenti per l'accesso automatico a Citrix NetScaler con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Citrix NetScaler abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Citrix NetScaler supporta l'accesso SSO avviato da **SP**

* Citrix NetScaler supporta il provisioning utenti **JIT**

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>Aggiunta di Citrix NetScaler dalla raccolta

Per configurare l'integrazione di Citrix NetScaler in Azure AD, è necessario aggiungere Citrix NetScaler dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Citrix NetScaler** nella casella di ricerca.
1. Selezionare **Citrix NetScaler** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Citrix NetScaler

Configurare e testare l'accesso SSO di Azure AD con Citrix NetScaler usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix NetScaler.

Per configurare e testare l'accesso SSO di Azure AD con Citrix NetScaler, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Citrix NetScaler](#configure-citrix-netscaler-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Citrix NetScaler](#create-citrix-netscaler-test-user)** : per avere una controparte di B.Simon in Citrix NetScaler collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Citrix NetScaler** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<<Your FQDN>>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Per il funzionamento dell'accesso SSO, questi URL devono essere accessibili da siti pubblici. È necessario abilitare il firewall o altre impostazioni di sicurezza sul lato Netscaler per consentire ad Azure AD di inviare il token nell'URL del servizio consumer di asserzione configurato.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare l'**URL dei metadati di federazione dell'app**, quindi copiarlo e salvarlo nel Blocco note.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. L'applicazione Citrix NetScaler prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona **Modifica** e modificare il mapping degli attributi.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Citrix NetScaler prevede il passaggio di altri attributi nella risposta SAML. Nella sezione Attestazioni utente della finestra di dialogo Attributi utente eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per Origine selezionare **Attributo**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Fare clic su **OK**.

    1. Fare clic su **Salva**.


1. Nella sezione **Configura Citrix NetScaler** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Citrix NetScaler.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Citrix NetScaler**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-citrix-netscaler-sso"></a>Configurare l'accesso Single Sign-On di Citrix NetScaler

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Pubblicazione del server Web 

1. Creare un **server virtuale**.

    a. Passare a **Traffic Management > Load Balancing > Services** (Gestione traffico > Bilanciamento del carico > Servizi).
    
    b. Scegliere **Aggiungi**.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

    c. Specificare i dettagli del server Web che esegue le applicazioni seguenti:
    * **Nome servizio**
    * **Indirizzo IP del server/Server esistente**
    * **Protocollo**
    * **Porta**

     ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Configurazione del bilanciamento del carico

1. Per configurare il bilanciamento del carico, seguire questa procedura:

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/load01.png)

    a. Passare a **Traffic Management > Load Balancing > Virtual Servers** (Gestione traffico > Bilanciamento del carico > Server virtuali).

    b. Scegliere **Aggiungi**.

    c. Specificare i dettagli seguenti:

    * **Nome**
    * **Protocollo**
    * **Indirizzo IP**
    * **Porta**
    * Fare clic su **OK**.

### <a name="bind-virtual-server"></a>Associare il server virtuale

Associare il bilanciamento del carico al server virtuale creato in precedenza.

![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind01.png)

![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Associare il certificato

Dal momento che il servizio verrà pubblicato SSL, associare il certificato del server e quindi testare l'applicazione.

![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind03.png)

![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profilo SAML di Citrix ADC

### <a name="create-authentication-policy"></a>Creare i criteri di autenticazione

1. Passare a **Security > AAA – Application Traffic > Policies > Authentication > Authentication Policies** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Criteri di autenticazione).

2. Fare clic su **Aggiungi** e quindi specificare i dettagli.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/policy01.png)

    a. In Name (Nome) specificare un nome per il **criterio di autenticazione**.

    b. Impostare Expression (Espressione) su **true**.

    c. Impostare Action type (Tipo di azione) su **SAML**.

    d. Per Action (Azione) fare clic su **Add** (Aggiungi) e seguire la procedura guidata Create Authentication SAML Server Wizard (Creazione guidata server SAML di autenticazione).
    
    e. Fare clic su Create (Crea) per il **criterio di autenticazione**.

### <a name="create-authentication-saml-server"></a>Creare il server SAML di autenticazione

1. Eseguire la procedura seguente:

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/server01.png)

    a. Specificare un valore in **Name** (Nome).

    b. Selezionare Import Metadata (Importa metadati) e specificare l'URL dei metadati dall'interfaccia utente SAML di Azure copiato in precedenza.
    
    c. Specificare un valore in **Issuer Name** (Nome dell'autorità emittente).

    d. Fare clic su **Create** (Crea).

### <a name="create-authentication-virtual-server"></a>Creare il server virtuale di autenticazione

1.  Passare a **Security > AAA - Application Traffic >> Authentication Virtual Servers** (Sicurezza > AAA - Traffico applicazione > Server virtuali di autenticazione).

2.  Fare clic su **Add** (Aggiungi) e seguire questa procedura:

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/server02.png)

    a.  Specificare un **Nome**.

    b.  Scegliere **Non-Addressable** (Non indirizzabile).

    c.  Impostare Protocol (Protocollo) su **SSL**.

    d.  Fare clic su **OK**.

    e.  Fare clic su **Continua**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurare il server virtuale di autenticazione per l'uso di Azure AD

Sarà necessario modificare le due sezioni del server virtuale di autenticazione.

1.  **Advanced Authentication Policies** (Criteri di autenticazione avanzati)

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual01.png)

    a. Selezionare il **criterio di autenticazione** creato in precedenza.

    b. Fare clic su **Bind** (Associa).

      ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **Form Based Virtual Servers** (Server virtuali basati su form)

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual03.png)

    a.  È necessario specificare un **nome di dominio completo** dal momento che è applicato dall'interfaccia utente.

    b.  Scegliere il **server virtuale di bilanciamento del carico** da proteggere con Autenticazione di Azure AD.

    c.  Fare clic su **Bind** (Associa).

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Assicurarsi di fare clic su **Done** (Fine) anche nella pagina di configurazione del server virtuale di autenticazione.

3. Verificare le modifiche. Passare all'URL dell'applicazione. Verrà visualizzata la pagina di accesso al tenant invece del messaggio di accesso non autenticato visualizzato in precedenza.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione

### <a name="citrix-adc-configuration"></a>Configurazione di Citrix ADC

### <a name="create-an-rewrite-action"></a>Creare un'azione di riscrittura

1. Passare a **AppExpert > Rewrite > Rewrite Actions** (AppExpert > Riscrivi > Azioni di riscrittura).
 
    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header01.png)

2.  Scegliere **Aggiungi**.

    a.  Specificare un valore in **Name** (Nome).

    b.  Impostare Type (Tipo) su **INSERT_HTTP_HEADER**.

    c.  Specificare un valore in **HEADER NAME** (NOME INTESTAZIONE), ad esempio SecretID.

    d.  Specificare l'espressione **aaa.USER.ATTRIBUTE(`mySecretID`)** , dove **mySecretID** è l'attestazione SAML di Azure AD inviata a Citrix ADC.

    e.  Fare clic su **Crea**.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>Creare un criterio di riscrittura.

1.  Passare a **AppExpert > Rewrite > Rewrite Policies** (AppExpert > Riscrivi > Criteri di riscrittura).
 
    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header03.png)

2.  Scegliere **Aggiungi**.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header04.png)

    a.  Specificare un valore in **Name** (Nome).

    b.  Scegliere l'**azione** creata in precedenza.

    c. In Expression (Espressione) specificare **true**.

    d.  Fare clic su **Create** (Crea).

### <a name="bind-rewrite-policy-to-virtual-servers"></a>Associare il criterio di riscrittura ai server virtuali

1. È possibile associare un criterio di riscrittura a un server virtuale specifico usando l'interfaccia utente grafica.

2. Passare a **Traffic Management > Load Balancing > Virtual Servers** (Gestione traffico > Bilanciamento del carico > Server virtuali).

3. Nell'elenco dei server virtuali del riquadro dei dettagli selezionare il **server virtuale** al quale si intende associare il criterio di riscrittura e quindi fare clic su **Open** (Apri).

4. Nella finestra di dialogo Configure Virtual Server (Load Balancing) (Configura server virtuale - Bilanciamento del carico) selezionare la scheda **Policies** (Criteri). Nell'elenco sono visualizzati tutti i criteri configurati in NetScaler.
 
    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header06.png)

5.  Selezionare la **casella di controllo** accanto al nome del criterio da associare a questo server virtuale.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  Fare clic su **OK**. Nella barra di stato viene visualizzato un messaggio per informare che il criterio è stato configurato correttamente.

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>Modificare il server SAML per estrarre gli attributi dall'attestazione

1.  Passare a **Security > AAA - Application Traffic > Policies > Authentication > Advanced Policies > Actions > Servers** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Criteri avanzati > Azioni > Server).

2.  Selezionare il **server SAML di autenticazione** appropriato per l'applicazione.
 
    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header09.png)

3. Nella sezione Attributes (Attributi) digitare gli attributi SAML da estrarre separandoli con una virgola (","). In questo caso viene specificato l'attributo denominato **mySecretID**.
 
    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header10.png)

4. Verificare l'accesso alle applicazioni.

    ![Configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>Creare l'utente di test di Citrix NetScaler

In questa sezione viene creato un utente di nome B.Simon in Citrix NetScaler. Citrix NetScaler supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Citrix NetScaler, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto clienti di Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Citrix NetScaler nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Citrix NetScaler per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Citrix NetScaler con Azure AD](https://aad.portal.azure.com/)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos](citrix-netscaler-tutorial.md)
