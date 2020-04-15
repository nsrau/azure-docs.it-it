---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Citrix NetScaler (autenticazione basata su Kerberos) | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On (SSO) tra Azure Active Directory e Citrix NetScaler usando l'autenticazione basata su Kerberos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6771060f05a03c82879738dc5e8caccb67e55abc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477994"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Citrix NetScaler (autenticazione basata su Kerberos)

Questa esercitazione descrive come integrare Citrix NetScaler con Azure Active Directory (Azure AD). Integrando Citrix NetScaler con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Citrix NetScaler.
* Abilitare gli utenti per l'accesso automatico a Citrix NetScaler con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Citrix NetScaler abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. L'esercitazione include gli scenari seguenti:

* Accesso SSO **avviato da SP** per Citrix NetScaler

* Provisioning utenti **JIT** per Citrix NetScaler

* [Autenticazione basata su Kerberos per Citrix NetScaler](#publish-the-web-server)

* [Autenticazione basata su intestazione per Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

* Dopo aver configurato Citrix NetScaler, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

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

    1. Nella casella di testo **URL di risposta** immettere un URL nel formato seguente: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Per configurare l'applicazione in modalità **avviata da SP**, selezionare **Imposta URL aggiuntivi** e completare il passaggio seguente:

    * Nella casella di testo **URL di accesso** immettere un URL nel formato seguente: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`.

    > [!NOTE]
    > * Gli URL usati in questa sezione non sono valori reali. Aggiornarli con i valori effettivi di identificatore, URL di risposta e URL di accesso. Per ottenere questi valori, contattare il [team di supporto clienti di Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.
    > * Per configurare l'accesso SSO, gli URL devono essere accessibili da siti Web pubblici. È necessario abilitare il firewall o altre impostazioni di sicurezza sul lato Citrix NetScaler per consentire ad Azure AD di pubblicare il token all'URL configurato.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare l'**URL dei metadati di federazione dell'app**, quindi copiarlo e salvarlo nel Blocco note.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Citrix NetScaler** copiare gli URL pertinenti in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

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

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos](#publish-the-web-server)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Pubblicare il server Web 

Per creare un server virtuale:

1. Selezionare **Traffic Management** > **Load Balancing** > **Services** (Gestione traffico > Bilanciamento del carico > Servizi).
    
1. Selezionare **Aggiungi**.

    ![Configurazione di Citrix NetScaler - Riquadro Services](./media/citrix-netscaler-tutorial/web01.png)

1. Impostare i valori seguenti per il server Web che esegue le applicazioni:

   * **Nome servizio**
   * **Indirizzo IP del server/Server esistente**
   * **Protocollo**
   * **Porta**

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

    ![Configurazione di Citrix NetScaler - Riquadro Basic Settings](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Associare il server virtuale

Per associare il servizio di bilanciamento del carico al server virtuale:

1. Nel riquadro **Services and Service Groups** (Servizi e gruppi di servizi) selezionare **No Load Balancing Virtual Server Service Binding** (Nessuna associazione di servizi del server virtuale per il bilanciamento del carico).

   ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server Service Binding](./media/citrix-netscaler-tutorial/bind01.png)

1. Verificare le impostazioni come illustrato nello screenshot seguente e quindi selezionare **Close** (Chiudi).

   ![Configurazione di Citrix NetScaler - Verifica dell'associazione dei servizi del server virtuale](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Associare il certificato

Per pubblicare questo servizio come TLS, associare il certificato del server e quindi testare l'applicazione:

1. In **Certificate** (Certificato) selezionare **No Server Certificate** (Nessun certificato server).

   ![Configurazione di Citrix NetScaler - Riquadro Server Certificate](./media/citrix-netscaler-tutorial/bind03.png)

1. Verificare le impostazioni come illustrato nello screenshot seguente e quindi selezionare **Close** (Chiudi).

   ![Configurazione di Citrix NetScaler - Verifica del certificato](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profilo SAML di Citrix ADC

Per configurare il profilo SAML di Citrix ADC, completare le sezioni seguenti.

### <a name="create-an-authentication-policy"></a>Creare un criterio di autenticazione

Per creare un criterio di autenticazione:

1. Passare a **Security** > **AAA – Application Traffic** > **Policies** > **Authentication** > **Authentication Policies** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Criteri di autenticazione).

1. Selezionare **Aggiungi**.

1. Nel riquadro **Create Authentication Policy** (Crea criterio di autenticazione) immettere o selezionare i valori seguenti:

    * **Name**: immettere un nome per il criterio di autenticazione.
    * **Azione**: immettere **SAML** e quindi selezionare **Add** (Aggiungi).
    * **Expression** (Espressione):  immettere **true**.     
    
    ![Configurazione di Citrix NetScaler - Riquadro Create Authentication Policy](./media/citrix-netscaler-tutorial/policy01.png)

1. Selezionare **Create** (Crea).

### <a name="create-an-authentication-saml-server"></a>Creare un server SAML di autenticazione

Per creare un server SAML di autenticazione, passare al riquadro **Create Authentication SAML Server** (Crea server SAML di autenticazione) e quindi completare la procedura seguente:

1. In **Name** (Nome) immettere un nome per il server SAML di autenticazione.

1. In **Export SAML Metadata** (Esporta metadati SAML):

   1. Selezionare la casella di controllo **Import Metadata** (Importa metadati).

   1. Immettere l'URL dei metadati di federazione copiato in precedenza dall'interfaccia utente SAML di Azure.
    
1. In **Issuer Name** (Nome autorità di certificazione) immettere l'URL pertinente.

1. Selezionare **Create** (Crea).

![Configurazione di Citrix NetScaler - Riquadro Create Authentication SAML Server](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Creare un server virtuale di autenticazione

Per creare un server virtuale di autenticazione:

1.  Passare a **Security** > **AAA - Application Traffic** > **Policies** > **Authentication** > **Authentication Virtual Servers** (Sicurezza > AAA - Traffico applicazione > Criteri > Autenticazione > Server virtuali di autenticazione).

1.  Selezionare **Add** (Aggiungi) e quindi completare la procedura seguente:

    1. In **Name** (Nome) immettere un nome per il server virtuale di autenticazione.

    1. Selezionare la casella di controllo **Non-Addressable** (Non indirizzabile).

    1. Per **Protocol** (Protocollo) selezionare **SSL**.

    1. Selezionare **OK**.
    
1. Selezionare **Continua**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurare il server virtuale di autenticazione per l'uso di Azure AD

Modificare due sezioni per il server virtuale di autenticazione:

1.  Nel riquadro **Advanced Authentication Policies** (Criteri di autenticazione avanzati) selezionare **No Authentication Policy** (Nessun criterio di autenticazione).

    ![Configurazione di Citrix NetScaler - Riquadro Advanced Authentication Policies](./media/citrix-netscaler-tutorial/virtual01.png)

1. Nel riquadro **Policy Binding** (Associazione criteri) selezionare il criterio di autenticazione e quindi **Bind** (Associa).

    ![Configurazione di Citrix NetScaler - Riquadro Policy Binding](./media/citrix-netscaler-tutorial/virtual02.png)

1. Nel riquadro **Form Based Virtual Servers** (Server virtuali basati su moduli) selezionare **No Load Balancing Virtual Server** (Nessun server virtuale di bilanciamento del carico).

    ![Configurazione di Citrix NetScaler - Riquadro Form Based Virtual Servers](./media/citrix-netscaler-tutorial/virtual03.png)

1. In **Authentication FQDN** (Nome di dominio completo per autenticazione) immettere un nome di dominio completo (FQDN) (obbligatorio).

1. Selezionare il server virtuale di bilanciamento del carico che si vuole proteggere con Autenticazione di Azure AD.

1. Selezionare **Bind** (Associa).

    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server Binding](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Assicurarsi di selezionare **Done** (Fine) nel riquadro **Authentication Virtual Server Configuration** (Configurazione server virtuale di autenticazione).

1. Per verificare le modifiche, in un browser passare all'URL dell'applicazione. Dovrebbe essere visualizzata la pagina di accesso al tenant invece del messaggio di accesso non autenticato visualizzato in precedenza.

    ![Configurazione di Citrix NetScaler - Pagina di accesso in un Web browser](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Creare un account di delega Kerberos per Citrix ADC

1. Creare un account utente; in questo esempio si usa _AppDelegation_.

    ![Configurazione di Citrix NetScaler - Riquadro Properties](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Configurare un nome SPN HOST per questi account. 

    Esempio: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    Esempio:

    * `IDENTT.WORK` è il nome di dominio completo del dominio.
    * `identt` è il nome NetBIOS del dominio.
    * `appdelegation` è il nome dell'account utente di delega.

1. Configurare la delega per il server Web, come illustrato nello screenshot seguente:
 
    ![Configurazione di Citrix NetScaler - Delega nel riquadro Properties](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > Nello screenshot di esempio il nome del server Web interno che esegue il sito dell'autenticazione integrata di Windows è _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (account di delega Kerberos)

Per configurare l'account Citrix NetScaler AAA KCD:

1.  Passare a **Citrix Gateway** > **AAA KCD (Kerberos Constrained Delegation) Accounts** (Gateway Citrix > Account AAA KCD (Delega vincolata Kerberos)).

1.  Selezionare **Add** (Aggiungi) e quindi immettere o selezionare i valori seguenti:

    * **Name**: Immettere il nome dell'account di delega vincolata Kerberos.

    * **Realm** (Area di autenticazione): immettere il dominio e l'estensione in lettere maiuscole.

    * **Service SPN** (Nome dell'entità servizio): impostare su `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` è obbligatorio e deve essere specificato in lettere maiuscole. Esempio: `http/cweb2@IDENTT.WORK`.

    * **Delegated User** (Utente delegato): immettere il nome dell'utente delegato.

    * Selezionare la casella di controllo **Password for Delegated User** (Password per l'utente delegato) e quindi immettere e confermare una password.

1. Selezionare **OK**.
 
    ![Configurazione di Citrix NetScaler - Riquadro Configure KCD Account](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Profilo di traffico e criteri di traffico Citrix

Per configurare il profilo di traffico e i criteri di traffico Citrix:

1.  Passare a **Security** > **AAA - Application Traffic** > **Policies** > **Traffic Policies, Profiles and Form SSO Profiles Traffic Policies** (Sicurezza > AAA - Traffico applicazione > Criteri > Criteri di traffico, profili e criteri di traffico dei profili per l'accesso SSO al modulo).

1.  Selezionare **Traffic Profiles** (Profili di traffico).

1.  Selezionare **Aggiungi**.

1.  Per configurare un profilo di traffico, immettere o selezionare i valori seguenti.

    * **Name**: immettere un nome per il profilo di traffico.

    * **Single Sign-on** (Accesso Single Sign-On): selezionare **ON** (ATTIVATO).

    * **KCD Account** (Account di delega vincolata Kerberos): selezionare l'account di delega vincolata Kerberos creato nella sezione precedente.

1. Selezionare **OK**.

    ![Configurazione di Citrix NetScaler - Riquadro Configure Traffic Profile](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Selezionare **Traffic Policy** (Criterio di traffico).

1.  Selezionare **Aggiungi**.

1.  Per configurare un criterio di traffico, immettere o selezionare i valori seguenti:

    * **Name**: immettere un nome per il criterio di traffico.

    * **Profile** (Profilo): selezionare il profilo di traffico creato nella sezione precedente.

    * **Expression** (Espressione): immettere **true**.

1. Selezionare **OK**.

    ![Configurazione di Citrix NetScaler - Riquadro Configure Traffic Policy](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Associare un criterio di traffico a un server virtuale in Citrix

Per associare un criterio di traffico a un server virtuale usando l'interfaccia utente grafica:

1. Passare a **Traffic Management** > **Load Balancing** > **Virtual Servers** (Gestione traffico > Bilanciamento del carico > Server virtuali).

1. Nell'elenco dei server virtuali selezionare il server virtuale al quale si intende associare il criterio di riscrittura e quindi fare clic su **Open** (Apri).

1. Nel riquadro **Load Balancing Virtual Server** (Server virtuale di bilanciamento del carico) selezionare **Policies** (Criteri) in **Advanced Settings** (Impostazioni avanzate). Nell'elenco sono visualizzati tutti i criteri configurati per l'istanza di NetScaler.
 
    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configurazione di Citrix NetScaler - Finestra di dialogo Policies](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Selezionare la casella di controllo accanto al nome del criterio da associare a questo server virtuale.
 
    ![Configurazione di Citrix NetScaler - Riquadro Load Balancing Virtual Server Traffic Policy Binding](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Nella finestra di dialogo **Choose Type** (Scegli tipo):

    1. Per **Choose Policy** (Scegli criterio) selezionare **Traffic** (Traffico).

    1. Per **Choose Type** (Scegli tipo) selezionare **Request** (Richiesta).

    ![Configurazione di Citrix NetScaler - Riquadro Choose Type](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Dopo l'associazione del criterio fare clic su **Done** (Fine).
 
    ![Configurazione di Citrix NetScaler - Riquadro Policies](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Testare l'associazione usando il sito Web dell'autenticazione integrata di Windows.

    ![Configurazione di Citrix NetScaler - Pagina di test in un Web browser](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Creare l'utente di test di Citrix NetScaler

In questa sezione viene creato un utente di nome B.Simon in Citrix NetScaler. Citrix NetScaler supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è necessario alcun intervento da parte dell'utente. Se non esiste già un utente in Citrix NetScaler, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente, contattare il [team di supporto clienti di Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Citrix NetScaler nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Citrix NetScaler per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Citrix NetScaler con Azure AD](https://aad.portal.azure.com/)

- [Configurare l'accesso Single Sign-On di Citrix NetScaler per l'autenticazione basata su intestazione](header-citrix-netscaler-tutorial.md)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Citrix NetScaler con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)