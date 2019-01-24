---
title: 'Esercitazione: Integrazione di Azure Active Directory con SharePoint locale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SharePoint locale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: bfba45018ff15864e6a1e9a745358dc1233b8104
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825494"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Esercitazione: Integrazione di Azure Active Directory con SharePoint locale

Questa esercitazione descrive come integrare SharePoint locale con Azure Active Directory (Azure AD).
L'integrazione di SharePoint locale con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SharePoint locale.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SharePoint locale con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SharePoint locale, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SharePoint locale abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SharePoint locale supporta l'accesso SSO avviato da **SP**

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Aggiunta di SharePoint locale dalla raccolta

Per configurare l'integrazione di SharePoint locale in Azure AD, è necessario aggiungere SharePoint locale dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SharePoint locale dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SharePoint locale**, selezionare **SharePoint locale** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SharePoint locale nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SharePoint locale usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SharePoint locale.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SharePoint locale, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SharePoint locale](#configure-sharepoint-on-premises-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Concedere l'accesso a un utente di test di SharePoint locale](#grant-access-to-sharepoint-on-premises-test-user)**: per avere una controparte di Britta Simon in SharePoint locale collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SharePoint locale, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SharePoint locale** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SharePoint locale](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `urn:sharepoint:federation`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SharePoint locale](https://support.office.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

    > [!Note]
    > Prendere nota del percorso del file in cui è stato scaricato il file del certificato. Il file sarà necessario in seguito nello script PowerShell per la configurazione.

6. Nella sezione **Configura SharePoint locale** copiare gli URL appropriati in base alle esigenze. Per **URL servizio Single Sign-On**, usare il valore nel formato seguente: `https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_ è l'ID tenant della sottoscrizione di Azure AD.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

    > [!NOTE]
    > L'applicazione SharePoint locale usa il token SAML 1.1, di conseguenza Azure AD attende la richiesta di WS Fed dal server di SharePoint e, dopo l'autenticazione, rilascia il token SAML 1.1.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Configurare l'accesso Single Sign-On di SharePoint locale

1. In un'altra finestra del Web browser accedere al sito aziendale di SharePoint locale come amministratore.

2. **Configurare un nuovo provider di identità attendibile in SharePoint Server 2016**

    Accedere al server di SharePoint Server 2016 e aprire la shell di gestione SharePoint 2016. Immettere i valori di $realm (valore dell'identificatore dal dominio locale di SharePoint e le sezioni URL nel portale di Azure), $wsfedurl (URL servizio Single Sign-On) e $filepath (percorso del file in cui è stato scaricato il file del certificato) dal portale di Azure ed eseguire i comandi seguenti per configurare un nuovo provider di identità attendibile.

    > [!TIP]
    > Se non si ha familiarità con l'uso di PowerShell o per altre informazioni sul funzionamento di PowerShell, vedere [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Successivamente, eseguire questi passaggi per abilitare il provider di identità attendibili per l'applicazione:

    a. In Amministrazione centrale passare a **Gestione applicazione Web** e selezionare l'applicazione Web che si vuole proteggere con Azure AD.

    b. Nella barra multifunzione fare clic su **Provider di autenticazione** e selezionare la zona che si vuole usare.

    c. Selezionare **Provider di identità attendibili** e selezionare il provider di identità appena registrato denominato *AzureAD*.

    d. Nell'impostazione dell'URL della pagina di accesso scegliere **Personalizza pagina di accesso** e fornire il valore "/_trust/".

    e. Fare clic su **OK**.

    ![Configurare il provider di autenticazione](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Alcuni degli utenti esterni non potranno usare questa integrazione Single Sign-On perché il relativo UPN avrà un valore alterato simile a `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. A breve sarà possibile eseguire una configurazione personalizzata dell'app per gestire l'UPN in base al tipo di utente. In seguito tutti gli utenti guest dovrebbero essere in grado di usare l'accesso SSO senza problemi come i dipendenti dell'organizzazione.

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

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Concedere l'accesso a un utente di test di SharePoint locale

Gli utenti che si registreranno ad Azure AD ed effettueranno l'accesso a SharePoint dovranno disporre dell'accesso all'applicazione  Usare i passaggi seguenti per impostare le autorizzazioni per accedere all'applicazione Web.

1. In Amministrazione centrale fare clic su **Gestione applicazioni**.

2. Nella pagina **Gestione applicazioni**, nella sezione **Applicazioni Web** fare clic su **Gestisci applicazioni Web**.

3. Fare clic sull'applicazione Web appropriata e quindi fare clic su **Criteri utente**.

4. In Criteri per applicazioni Web fare clic su **Aggiungi utenti**.

    ![Ricerca di un utente in base all'attestazione del nome](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. Nella finestra di dialogo **Aggiungi utenti** fare clic sulla zona appropriata in **Zone**e quindi fare clic su **Avanti**.

6. Nella finestra di dialogo **Criteri per applicazioni Web**, nella sezione **Seleziona utenti** fare clic sull'icona **Sfoglia**.

7. Nella casella di testo **Trova** digitare il valore del **nome dell'entità utente (UPN)** per il quale è stata configurata l'applicazione locale di SharePoint in Azure AD e fare clic su **Ricerca**. </br>Esempio: *brittasimon@contoso.com*.

8. Sotto l'intestazione AzureAD nella visualizzazione elenco selezionare la proprietà relativa al nome e fare clic su **Aggiungi**, quindi fare clic su **OK** per chiudere la finestra di dialogo.

9. In Autorizzazioni fare clic su **Controllo completo**.

    ![Concessione del controllo completo a un utente di attestazioni](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Fare clic su **Avanti**, quindi su **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurazione di un provider di identità attendibile per più applicazioni Web

La configurazione funziona per una singola applicazione Web, ma necessita di una configurazione aggiuntiva se si prevede di usare lo stesso provider di identità attendibile per più applicazioni Web. Si supponga ad esempio di aver esteso un'applicazione Web per usare l'URL `https://portal.contoso.local` e di voler ora autenticare anche gli utenti `https://sales.contoso.local`. A tale scopo, è necessario aggiornare il provider di identità per rispettare il parametro WReply e aggiornare la registrazione dell'applicazione in Azure AD per aggiungere un URL di risposta.

1. Nel portale di Azure aprire la directory di Azure AD. Fare clic su **Registrazioni per l'app**, quindi su **Visualizza tutte le applicazioni**. Fare clic sull'applicazione creata in precedenza (integrazione SAML di SharePoint).

2. Fare clic su **Impostazioni**.

3. Nel pannello Impostazioni fare clic su **URL di risposta**. 

4. Aggiungere l'URL per l'applicazione Web aggiuntiva con `/_trust/default.aspx` aggiunto all'URL (ad esempio `https://sales.contoso.local/_trust/default.aspx`) e fare clic su **Salva**.

5. Nel server di SharePoint aprire la **shell di gestione SharePoint 2016** ed eseguire i comandi seguenti, usando il nome dell'autorità di certificazione del token di identità attendibili adoperato in precedenza.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. In Amministrazione centrale passare all'applicazione Web e abilitare il provider di identità attendibili esistente. È necessario anche configurare l'URL nella pagina di accesso come una pagina di accesso personalizzata `/_trust/`.

7. In Amministrazione centrale fare clic sull'applicazione Web e scegliere **Criteri utente**. Aggiungere un utente con le autorizzazioni appropriate, come illustrato in precedenza in questo articolo.

### <a name="fixing-people-picker"></a>Correzione della selezione utenti

Gli utenti possono ora accedere a SharePoint 2016 con identità di Azure AD, ma l'esperienza utente può ancora migliorare. Ad esempio, la ricerca di un utente presenta molteplici risultati della ricerca nella selezione utenti. È disponibile un risultato di ricerca per ognuno dei 3 tipi di attestazioni creati nel mapping delle attestazioni. Per scegliere un utente con la selezione utenti, è necessario digitare esattamente il nome utente e scegliere il risultato dell'attestazione **name**.

![Risultati della ricerca di attestazioni](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Non essendo previste convalide per i valori cercati, possono verificarsi errori di ortografia o la scelta accidentale del tipo errato di attestazione da assegnare, ad esempio l'attestazione **SurName**. Questo può impedire agli utenti di accedere correttamente alle risorse.

Per assistenza in questo scenario, esiste una soluzione open source denominata [AzureCP](https://yvand.github.io/AzureCP/) che fornisce un provider di attestazioni personalizzate per SharePoint 2016. Tale soluzione userà Azure AD Graph per risolvere quanto immesso dagli utenti ed eseguirà la convalida. Altre informazioni su [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a SharePoint locale.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SharePoint locale**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **SharePoint locale**.

    ![Collegamento a SharePoint locale nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sharepoint-on-premises-test-user"></a>Creare un utente di test di SharePoint locale

In questa sezione viene creato un utente di nome Britta Simon in SharePoint locale. Collaborare con il  [team di supporto di SharePoint locale](https://support.office.com/) per aggiungere gli utenti nella piattaforma di SharePoint locale. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SharePoint locale nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SharePoint locale per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
