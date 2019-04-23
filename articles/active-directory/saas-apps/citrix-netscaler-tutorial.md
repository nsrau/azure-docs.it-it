---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix Netscaler | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix Netscaler.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281676"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix Netscaler

Questa esercitazione descrive come integrare Citrix Netscaler con Azure Active Directory (Azure AD).
L'integrazione di Citrix Netscaler con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Citrix Netscaler.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Citrix Netscaler con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Citrix Netscaler, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Citrix Netscaler abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Citrix Netscaler supporta l'accesso SSO avviato da **SP**

* Citrix Netscaler supporta il provisioning utenti **JIT**

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Aggiunta di Citrix Netscaler dalla raccolta

Per configurare l'integrazione di Citrix Netscaler in Azure AD, è necessario aggiungere Citrix Netscaler dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Citrix Netscaler dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Citrix Netscaler**, selezionare **Citrix Netscaler** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Citrix Netscaler nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Citrix Netscaler usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix Netscaler.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Citrix Netscaler, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Citrix Netscaler](#configure-citrix-netscaler-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Citrix Netscaler](#create-citrix-netscaler-test-user)**: per avere una controparte di Britta Simon in Citrix Netscaler collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Citrix Netscaler, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Citrix Netscaler** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Citrix Netscaler](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<<Your FQDN>>`

    c. Nella casella di testo **Reply URL (Assertion Consumer Service URL)** (URL di risposta - URL del servizio consumer di asserzione) digitare un URL nel formato seguente: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Citrix Netscaler](https://www.citrix.com/contact/technical-support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

    > [!NOTE]
    > Per il funzionamento dell'accesso SSO, questi URL devono essere accessibili da siti pubblici. È necessario abilitare il firewall o altre impostazioni di sicurezza sul lato Netscaler per consentire ad Azure AD di inviare il token nell'URL del servizio consumer di asserzione configurato.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Citrix Netscaler** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-citrix-netscaler-single-sign-on"></a>Configurare l'accesso Single Sign-On di Citrix Netscaler

1. In un'altra finestra del browser Web accedere al tenant Citrix Netscaler come amministratore.

2. Verificare che **NetScaler Firmware Version = NS12.1: Build 48.13.nc**.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure01.png)

3. Nella pagina **VPN Virtual Server** (Server virtuale VPN) seguire questa procedura:

     ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure02.png)

    a. Impostare le impostazioni del gateway **ICA Only** (Solo ICA) su **true**.
    
    b. Impostare **Enable Authentication** (Abilita autenticazione) su **true**.
    
    c. **DTLS** è facoltativo.
    
    d. Assicurarsi che **SSLv3** sia impostato su **Disabled** (Disabilitato).

4. Viene creato un gruppo **SSL Ciphers** (Crittografie SSL) personalizzato per ottenere A+ per https://www.ssllabs.com come illustrato di seguito:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure03.png)

5. Nella pagina **Configure Authentication SAML Server** (Configura server SAML di autenticazione) seguire questa procedura:

      ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure04.png)

    a. Nella casella di testo **Name** (Nome) digitare il nome del server.

    b. Nella casella di testo **Redirect URL** (URL reindirizzamento) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Single Logout URL** (URL di disconnessione singolo) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    d. In **IDP Certificate Name** (Nome certificato IDP) fare clic sul segno **"+"** per aggiungere il certificato scaricato dal portale di Azure. Dopo il caricamento selezionare il certificato dall'elenco a discesa.

    e. Ecco altri campi da impostare in questa pagina.

      ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure24.png)

    f. Impostare **Requested Authentication Context** (Contesto di autenticazione richiesto) su **Exact** (Esatto).

    g. Impostare **Signature Algorithm** (Algoritmo di firma) su **RSA-SHA256**.

    h. Impostare **Digest Method** (Metodo digest) su **SHA256**.

    i. Selezionare **Enforce Username** (Imponi nome utente).

    j. Fare clic su **OK**.

6. Per configurare **Session Profile** (Profilo sessione), seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure06.png)

    a. Nella casella di testo **Name** (Nome) digitare il nome del profilo di sessione.

    b. Nella scheda **Client Experience** (Esperienza client) apportare le modifiche come illustrato nello screenshot seguente.

    c. Continuare ad apportare le modifiche nella scheda **General** (Generale) come illustrato di seguito e fare clic su **OK**.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure07.png)

    d. Nella scheda **Published Applications** (Applicazioni pubblicate) apportare le modifiche come illustrato nello screenshot seguente e fare clic su **OK**.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure08.png)

    e. Nella scheda **Security** (Sicurezza) apportare le modifiche come illustrato nello screenshot seguente e fare clic su **OK**.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure09.png)

7. Fare in modo che le connessioni ICA usino la porta di attendibilità sessione **2598**, come illustrato nello screenshot seguente.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure10.png)

8. Nella sezione **SAML** aggiungere i valori per **Servers** (Server) come illustrato nello screenshot seguente.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure11.png)

9. Nella sezione **SAML** aggiungere i valori per **Policies** (Criteri) come illustrato nello screenshot seguente.

     ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure12.png)

10. Nella pagina **Global Settings** (Impostazioni globali) passare alla sezione **Clientless Access** (Accesso senza client).

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure13.png)

11. Nella scheda **Configuration** (Configurazione) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure14.png)

    a. Selezionare **Allow Domains** (Consenti domini).

    b. Nella casella di testo **Domain Name** (Nome di dominio) selezionare il dominio.

    c. Fare clic su **OK**.

12. Impostare **StoreFront** su **Receiver for Web Sites** (Ricevitore per siti Web) come illustrato nello screenshot seguente:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure15.png)

13. Nel popup **Manage Authentication Methods - Corp** (Gestisci metodi di autenticazione - Azienda) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure16.png)

    a. Selezionare **Nome utente e password**.

    b. Selezionare **Pass-through from NetScaler Gateway** (Pass-through da gateway NetScaler).

    c. Fare clic su **OK**.

14. Nel popup **Configure Trusted Domains** (Configura domini attendibili) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure17.png)

    a. Selezionare **Trusted domains only** (Solo domini attendibili).

    b. Fare clic su **Add** (Aggiungi) per aggiungere il dominio nella casella di testo **Trusted domains** (Domini attendibili).

    c. Selezionare il dominio predefinito nell'elenco **Default domain** (Dominio predefinito).

    d. Selezionare **Show domains list in logon page** (Mostra elenco domini nella pagina di accesso).

    e. Fare clic su **OK**.

15. Nel popup **Manage NetScaler Gateways** (Gestisci gateway NetScaler) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure18.png)

    a. Fare clic su **Add** (Aggiungi) per aggiungere i gateway NetScaler nella casella di testo **NetScaler Gateways** (Gateway NetScaler).

    b. Fare clic su **Close**.

16. Nella scheda **StoreFront General Settings** (Impostazioni generali StoreFront) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure19.png)

    a. Nella casella di testo **Display name** (Nome visualizzato) digitare il nome del gateway NetScaler.

    b. Nella casella di testo **NetScaler Gateway URL** (URL gateway NetScaler) digitare l'URL del gateway NetScaler.

    c. Impostare **Usage or role** (Utilizzo o ruolo) su **Authentication and HDX routing** (Autenticazione e routing HDX).

    d. Fare clic su **OK**.

17. Nella scheda **StoreFront Secure Ticket Authority** (Autorità ticket sicuri StoreFront) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure20.png)

    a. Fare clic su **Add** (Aggiungi) per aggiungere il valore di **Secure Ticket Authority URL** (URL autorità ticket sicuro) nella casella di testo.

    b. Selezionare **Enable session reliability** (Abilita attendibilità sessione).

    c. Fare clic su **OK**.

18. Nella scheda **StoreFront Authentication Settings** (Impostazioni autenticazione StoreFront) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure21.png)

    a. Selezionare un valore per **Version** (Versione).

    b. Impostare **Logon type** (Tipo di accesso) su **Domain** (Dominio).

    c. Immettere un valore per **Callback URL** (URL callback).

    d. Fare clic su **OK**.

19. Nella scheda **StoreFront Deploy Citrix Receiver** (Ricevitore Citrix di distribuzione StoreFront) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure22.png)

    a. Impostare **Deployment option** (Opzione di distribuzione) su **Use Receiver for HTML5 if local Receiver is unavailable** (Usa ricevitore per HTML5 se quello locale non è disponibile).

    b. Fare clic su **OK**.

20. Nel popup **Manage Beacons** (Gestisci beacon) seguire questa procedura:

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure23.png)

    a. Impostare **Internal beacon** (Beacon interno) su **Use the service URL** (Usa l'URL del servizio).

    b. Fare clic su **Add** (Aggiungi) per aggiungere gli URL nella casella di testo **External beacons** (Beacon esterni).

    c. Fare clic su **OK**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Citrix Netscaler.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Citrix Netscaler**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Citrix Netscaler**.

    ![Collegamento di Citrix Netscaler nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-citrix-netscaler-test-user"></a>Creare l'utente di test di Citrix Netscaler

In questa sezione viene creato un utente di nome Britta Simon in Citrix Netscaler. Citrix Netscaler supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Citrix Netscaler, ne viene creato uno nuovo dopo l'autenticazione.

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il [team di supporto clienti di Citrix Netscaler](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Citrix Netscaler nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Citrix Netscaler per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

