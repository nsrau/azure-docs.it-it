---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Citrix NetScaler (autenticazione basata su intestazione) | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On (SSO) tra Azure Active Directory e Citrix NetScaler usando l'autenticazione basata su intestazione.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.openlocfilehash: 48fedf7f3a73ce7fde60a1df80d971a5d7f88dd7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540586"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Citrix NetScaler (autenticazione basata su intestazione)

Questa esercitazione descrive come integrare Citrix NetScaler con Azure Active Directory (Azure AD). Integrando Citrix NetScaler con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Citrix NetScaler.
* Abilitare gli utenti per l'accesso automatico a Citrix NetScaler con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Citrix NetScaler abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. L'esercitazione include gli scenari seguenti:

* Accesso SSO **avviato da SP** per Citrix NetScaler

* Provisioning utenti **JIT** per Citrix NetScaler

* [Autenticazione basata su intestazione per Citrix NetScaler](#publish-the-web-server)

* [Autenticazione basata su Kerberos per Citrix NetScaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Aggiungere Citrix NetScaler dalla raccolta

Per integrare Citrix NetScaler con Azure AD, innanzitutto aggiungere Citrix NetScaler all'elenco di app SaaS gestite dalla raccolta:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.

1. Nel menu a sinistra, selezionare **Azure Active Directory**.

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.

1. Nella sezione **Aggiungi dalla raccolta** immettere **Citrix NetScaler** nella casella di ricerca.

1. Nei risultati selezionare **Citrix NetScaler** e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Citrix NetScaler

Configurare e testare l'accesso SSO di Azure AD con Citrix NetScaler usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix NetScaler.

Per configurare e testare l'accesso SSO di Azure AD con Citrix NetScaler, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.

    1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user): per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.

    1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user): per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.

1. [Configurare l'accesso Single Sign-On di Citrix NetScaler](#configure-citrix-netscaler-sso): per configurare le impostazioni di Single Sign-On sul lato applicazione.

    * [Creare l'utente di test di Citrix NetScaler](#create-a-citrix-netscaler-test-user): per avere una controparte di B.Simon in Citrix NetScaler collegata alla rappresentazione dell'utente in Azure AD.

1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD usando il portale di Azure, completare questa procedura:

1. Nella pagina di integrazione dell'applicazione **Citrix NetScaler** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On** in **Gestione**.

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** per configurare l'applicazione nella modalità **avviata da IDP**:

    1. Nella casella di testo **Identificatore** immettere un URL nel formato seguente: `https://<Your FQDN>`

    1. Nella casella di testo **URL di risposta** immettere un URL nel formato seguente: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Per configurare l'applicazione in modalità **avviata da SP**, selezionare **Imposta URL aggiuntivi** e completare il passaggio seguente:

    * Nella casella di testo **URL di accesso** immettere un URL nel formato seguente: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`.

    > [!NOTE]
    > * Gli URL usati in questa sezione non sono valori reali. Aggiornarli con i valori effettivi di identificatore, URL di risposta e URL di accesso. Per ottenere questi valori, contattare il [team di supporto clienti di Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.
    > * Per configurare l'accesso SSO, gli URL devono essere accessibili da siti Web pubblici. È necessario abilitare il firewall o altre impostazioni di sicurezza sul lato Citrix NetScaler per consentire ad Azure AD di pubblicare il token all'URL configurato.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare l'**URL dei metadati di federazione dell'app**, quindi copiarlo e salvarlo nel Blocco note.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. L'applicazione Citrix NetScaler prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona **Modifica** e modificare i mapping degli attributi.

    ![Modificare il mapping degli attributi SAML](common/edit-attribute.png)

1. L'applicazione Citrix NetScaler prevede inoltre il passaggio di altri attributi nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** completare questa procedura per aggiungere gli attributi del token SAML come illustrato nella tabella:

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    1. Nella casella di testo **Nome** immettere il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per **Attributo** selezionare **Origine**.

    1. Nell'elenco **Attributo di origine** immettere il valore dell'attributo indicato per la riga.

    1. Selezionare **OK**.

    1. Selezionare **Salva**.

1. Nella sezione **Configura Citrix NetScaler** copiare gli URL pertinenti in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel menu di sinistra del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

1. Selezionare **Nuovo utente** nella parte superiore del riquadro.

1. In **Proprietà utente** completare questa procedura:

   1. Per **Nome** immettere `B.Simon`.  

   1. Per **Nome utente** immettere _username@companydomain.extension_ . Ad esempio: `B.Simon@contoso.com`.

   1. Selezionare la casella di controllo **Mostra password** e quindi annotare o copiare il valore visualizzato in **Password**.

   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita l'utente B.Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a Citrix NetScaler.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

1. Nell'elenco delle applicazioni selezionare **Citrix NetScaler**.

1. Nella panoramica dell'app fare clic su **Utenti e gruppi** in **Gestisci**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti**. Scegliere **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo pertinente per l'utente dall'elenco e quindi scegliere **Seleziona**.

1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-citrix-netscaler-sso"></a>Configurare l'accesso Single Sign-On di Citrix NetScaler

Selezionare il collegamento alla procedura relativa al tipo di autenticazione che si vuole configurare:

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione](#publish-the-web-server)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Pubblicare il server Web 

Per creare un server virtuale:

1. Selezionare **Traffic Management** > **Load Balancing** > **Services** (Gestione traffico > Bilanciamento del carico > Servizi).
    
1. Selezionare **Aggiungi**.

    ![Configurazione di Citrix NetScaler - Riquadro Services](./media/header-citrix-netscaler-tutorial/web01.png)

1. Impostare i valori seguenti per il server Web che esegue le applicazioni:

   * **Nome servizio**
   * **Indirizzo IP del server/Server esistente**
   * **Protocollo**
   * **Porta**

     ![Riquadro di configurazione di Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Configurare il servizio di bilanciamento del carico

Per configurare il servizio di bilanciamento del carico:

1. Passare a **Traffic Management** > **Load Balancing** > **Virtual Servers** (Gestione traffico > Bilanciamento del carico > Server virtuali).

1. Selezionare **Aggiungi**.

1. Impostare i valori seguenti come descritto nello screenshot:

    * **Nome**
    * **Protocollo**
    * **Indirizzo IP**
    * **Porta**

1. Selezionare **OK**.

    ![Configurazione di Citrix NetScaler - Riquadro Basic Settings](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Associare il server virtuale

Per associare il servizio di bilanciamento del carico al server virtuale:

1. Nel riquadro **Services and Service Groups** (Servizi e gruppi di servizi) selezionare **No Load Balancing Virtual Server Service Binding** (Nessuna associazione di servizi del server virtuale per il bilanciamento del carico).

   ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server Service Binding](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Verificare le impostazioni come illustrato nello screenshot seguente e quindi selezionare **Close** (Chiudi).

   ![Configurazione di Citrix NetScaler - Verifica dell'associazione dei servizi del server virtuale](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Associare il certificato

Per pubblicare questo servizio come TLS, associare il certificato del server e quindi testare l'applicazione:

1. In **Certificate** (Certificato) selezionare **No Server Certificate** (Nessun certificato server).

   ![Configurazione di Citrix NetScaler - Riquadro Server Certificate](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Verificare le impostazioni come illustrato nello screenshot seguente e quindi selezionare **Close** (Chiudi).

   ![Configurazione di Citrix NetScaler - Verifica del certificato](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profilo SAML di Citrix ADC

Per configurare il profilo SAML di Citrix ADC, completare le sezioni seguenti:

### <a name="create-an-authentication-policy"></a>Creare un criterio di autenticazione

Per creare un criterio di autenticazione:

1. Passare a **Security** > **AAA – Application Traffic** > **Policies** > **Authentication** > **Authentication Policies** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Criteri di autenticazione).

1. Selezionare **Aggiungi**.

1. Nel riquadro **Create Authentication Policy** (Crea criterio di autenticazione) immettere o selezionare i valori seguenti:

    * **Name**: immettere un nome per il criterio di autenticazione.
    * **Azione**: immettere **SAML** e quindi selezionare **Add** (Aggiungi).
    * **Expression** (Espressione):  immettere **true**.     
    
    ![Configurazione di Citrix NetScaler - Riquadro Create Authentication Policy](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Selezionare **Create** (Crea).

### <a name="create-an-authentication-saml-server"></a>Creare un server SAML di autenticazione

Per creare un server SAML di autenticazione, passare al riquadro **Create Authentication SAML Server** (Crea server SAML di autenticazione) e quindi completare la procedura seguente:

1. In **Name** (Nome) immettere un nome per il server SAML di autenticazione.

1. In **Export SAML Metadata** (Esporta metadati SAML):

   1. Selezionare la casella di controllo **Import Metadata** (Importa metadati).

   1. Immettere l'URL dei metadati di federazione copiato in precedenza dall'interfaccia utente SAML di Azure.
    
1. In **Issuer Name** (Nome autorità di certificazione) immettere l'URL pertinente.

1. Selezionare **Create** (Crea).

![Configurazione di Citrix NetScaler - Riquadro Create Authentication SAML Server](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Creare un server virtuale di autenticazione

Per creare un server virtuale di autenticazione:

1.  Passare a **Security** > **AAA - Application Traffic** > **Policies** > **Authentication** > **Authentication Virtual Servers** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Server virtuali di autenticazione).

1.  Selezionare **Add** (Aggiungi) e quindi completare la procedura seguente:

    1. In **Name** (Nome) immettere un nome per il server virtuale di autenticazione.

    1. Selezionare la casella di controllo **Non-Addressable** (Non indirizzabile).

    1. Per **Protocol** (Protocollo) selezionare **SSL**.

    1. Selezionare **OK**.

    ![Configurazione di Citrix NetScaler - Riquadro Authentication Virtual Server](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurare il server virtuale di autenticazione per l'uso di Azure AD

Modificare due sezioni per il server virtuale di autenticazione:

1.  Nel riquadro **Advanced Authentication Policies** (Criteri di autenticazione avanzati) selezionare **No Authentication Policy** (Nessun criterio di autenticazione).

    ![Configurazione di Citrix NetScaler - Riquadro Advanced Authentication Policies](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. Nel riquadro **Policy Binding** (Associazione criteri) selezionare il criterio di autenticazione e quindi **Bind** (Associa).

    ![Configurazione di Citrix NetScaler - Riquadro Policy Binding](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Nel riquadro **Form Based Virtual Servers** (Server virtuali basati su moduli) selezionare **No Load Balancing Virtual Server** (Nessun server virtuale di bilanciamento del carico).

    ![Configurazione di Citrix NetScaler - Riquadro Form Based Virtual Servers](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. In **Authentication FQDN** (Nome di dominio completo per autenticazione) immettere un nome di dominio completo (FQDN) (obbligatorio).

1. Selezionare il server virtuale di bilanciamento del carico che si vuole proteggere con Autenticazione di Azure AD.

1. Selezionare **Bind** (Associa).

    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server Binding](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Assicurarsi di selezionare **Done** (Fine) nel riquadro **Authentication Virtual Server Configuration** (Configurazione server virtuale di autenticazione).

1. Per verificare le modifiche, in un browser passare all'URL dell'applicazione. Dovrebbe essere visualizzata la pagina di accesso al tenant invece del messaggio di accesso non autenticato visualizzato in precedenza.

    ![Configurazione di Citrix NetScaler - Pagina di accesso in un Web browser](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione

### <a name="configure-citrix-adc"></a>Configurare Citrix ADC

Per configurare Citrix ADC per l'autenticazione basata su intestazione, completare le sezioni seguenti.

#### <a name="create-a-rewrite-action"></a>Creare un'azione di riscrittura

1. Passare a **AppExpert** > **Rewrite** > **Rewrite Actions** (AppExpert > Riscrivi > Azioni di riscrittura).
 
    ![Configurazione di Citrix NetScaler - Riquadro Rewrite Actions](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Selezionare **Add** (Aggiungi) e quindi completare la procedura seguente:

    1. Per **Name** (Nome) immettere un nome per l'azione di riscrittura.

    1. Per **Type** (Tipo) immettere **INSERT_HTTP_HEADER**.

    1. Per **Header Name** (Nome intestazione) immettere un nome di intestazione; in questo esempio verrà usato _SecretID_.

    1. Per **Expression** (Espressione) immettere **aaa.USER.ATTRIBUTE("mySecretID")** , dove **mySecretID** è l'attestazione SAML di Azure AD inviata a Citrix ADC.

    1. Selezionare **Create** (Crea).

    ![Configurazione di Citrix NetScaler - Riquadro Create Rewrite Action](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Creare un criterio di riscrittura

1.  Passare a **AppExpert** > **Rewrite** > **Rewrite Policies** (AppExpert > Riscrivi > Criteri di riscrittura).
 
    ![Configurazione di Citrix NetScaler - Riquadro Rewrite Policies](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Selezionare **Add** (Aggiungi) e quindi completare la procedura seguente:

    1. Per **Name** (Nome) immettere un nome per il criterio di riscrittura.

    1. Per **Action** (Azione) selezionare l'azione di riscrittura creata nella sezione precedente.

    1. Per **Expression** (Espressione) immettere **true**.

    1. Selezionare **Create** (Crea).

    ![Configurazione di Citrix NetScaler - Riquadro Create Rewrite Policy](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Associare un criterio di riscrittura a un server virtuale

Per associare un criterio di riscrittura a un server virtuale usando l'interfaccia utente grafica:

1. Passare a **Traffic Management** > **Load Balancing** > **Virtual Servers** (Gestione traffico > Bilanciamento del carico > Server virtuali).

1. Nell'elenco dei server virtuali selezionare il server virtuale al quale si intende associare il criterio di riscrittura e quindi fare clic su **Open** (Apri).

1. Nel riquadro **Load Balancing Virtual Server** (Server virtuale di bilanciamento del carico) selezionare **Policies** (Criteri) in **Advanced Settings** (Impostazioni avanzate). Nell'elenco sono visualizzati tutti i criteri configurati per l'istanza di NetScaler.
 
    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Selezionare la casella di controllo accanto al nome del criterio da associare a questo server virtuale.
 
    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server Traffic Policy Binding](./media/header-citrix-netscaler-tutorial/header08.png)

1. Nella finestra di dialogo **Choose Type** (Scegli tipo):

    1. Per **Choose Policy** (Scegli criterio) selezionare **Traffic** (Traffico).

    1. Per **Choose Type** (Scegli tipo) selezionare **Request** (Richiesta).

    ![Configurazione di Citrix NetScaler - Finestra di dialogo Policies](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Selezionare **OK**. Un messaggio visualizzato nella barra di stato informa che il criterio è stato configurato correttamente.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Modificare il server SAML per estrarre attributi da un'attestazione

1.  Passare a **Security** > **AAA - Application Traffic** > **Policies** > **Authentication** > **Advanced Policies** > **Actions** > **Servers** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Criteri avanzati > Azioni > Server).

1.  Selezionare il server SAML di autenticazione appropriato per l'applicazione.
 
    ![Configurazione di Citrix NetScaler - Riquadro Configure Authentication SAML Server](./media/header-citrix-netscaler-tutorial/header09.png)

1. Nel riquadro **Attributes** (Attributi) immettere gli attributi SAML da estrarre separandoli con una virgola. In questo esempio viene immesso l'attributo `mySecretID`.
 
    ![Configurazione di Citrix NetScaler - Riquadro Attributes](./media/header-citrix-netscaler-tutorial/header10.png)

1. Per verificare l'accesso, specificare l'URL in un browser e cercare l'attributo SAML in **Headers Collection** (Raccolta intestazioni).

    ![Configurazione di Citrix NetScaler - Headers Collection visualizzata tramite URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Creare l'utente di test di Citrix NetScaler

In questa sezione viene creato un utente di nome B.Simon in Citrix NetScaler. Citrix NetScaler supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è necessario alcun intervento da parte dell'utente. Se non esiste già un utente in Citrix NetScaler, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente, contattare il [team di supporto clienti di Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Citrix NetScaler nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Citrix NetScaler per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Citrix NetScaler con Azure AD](https://aad.portal.azure.com/)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos](citrix-netscaler-tutorial.md)
